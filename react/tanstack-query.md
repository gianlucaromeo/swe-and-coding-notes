# TanStack Query

### `QueryClient`

The central manager in TanStack Query:
* Holds cache of all queries.
* Provides API to fetch, refetch, remove queries.
* Keeps configuration for retries, garbage collection, defaults.

### Queries, Mutations

A declarative dependency on an asynchronous source of data that is tied to a unique key.

If your method modifies data on the server, it is recommended to use Mutations instead.

You can use the hooks `useQuery` or `useMutation`. You have to provide a `queryKey` (used internally for refetching, caching, sharing queries through your app) and a `queryFn` that returns a Promise.

Result of the useQuery:
- `isPending` (query has no data yet)
- `isError`
- `isSuccess` (query succesful and data available)
- `isFecthing` (`true` if the query is fetching at any time, including background refetching)
- `error`
- `data`
- `status` (info about the `data`: Do we have it or not?)
- `fetchStatus` (info about the `queryFn`: Is it running or not?)

#### Query key

An array of values, hashed deterministically (order of keys doesn't matter).

If your queryFn depends on a variable, include it in your query key:

```typescript
function Todos({ todoId }) {
  const result = useQuery({
    queryKey: ['todos', todoId],
    queryFn: () => fetchTodoById(todoId),
  })
}
```

#### Query function

Any function that returns a Promise that either resolves or throws an error (or returns a rejected Promise).

#### Query Options

`queryOptions` is an helper to share `queryKey` and `queryFn` between multiple places, yet keeping them co-located to one another.

### Prefetch, hydration, and `HydrationBoundary`

React Query hydration means synchronizing server-fetched data into client's cache.

`HydrationBoundary` is a **client** component that makes that cache available to client so useQuery doesn’t refetch unnecessarily.

### Performance & Request Waterfalls

A **request waterfall** is what happens when a request for a resource (code, css, images, data) does not start unitl after another request for a resource has finished.

The best way to spot and analyze your request waterfalls is usually by opening opening "Network" tab of your devtools.

Consider the example of a triple waterfall (Markup > JS > CSS > Image), which actually represents 4 server roundtrips. With 250ms latency, which is not uncommon on 3g networks or in bad network conditions, we end up with a total time of 4*250=1000ms only counting latency.

#### Case 1 - Single Component Waterfalls / Serial Queries

A single component first fetches one query, and then another (request waterfall), for example when the second query is a Dependent Query (e.g., get user id > pass user id to other query).

For optimal performance, it's better to restructure your API so you only fetch one query (e.g., `getProjectsById`). Another way to mitigate dependent queries is to move the waterfall to the server where latency is lower.

Another case is using multiple `useSuspenseQuery` which will be executed in serial instead of in parallel like using regular `useQuery`. To fix this, you can use the hook `useSuspenseQueries`.

#### Case 2 - Nested (Dependend / Independent) Components Waterfalls

Happens when both a parent and a child copmonent contains queries, and the parent does not render the child until its query is done. This can happen both with `useQuery` and `useSuspenseQuery`.

We have a dependent nested component waterfall when the child renders conditionally based on the data in the parent, or if the child relies on some part of the result being passed down as a prop from the parent to make its query.

Let's make an example of child that is not dependent on the parent:
```
HomePage -> fecthes id -> passes it to Child as prop
Article(id) -> fetches /article by id and renders components such as Comments(id)
Comments(id) -> fetches /article-comments by id
```

Since the id is already available when Article renders, there is no need to wait for the articles to be fetched before fetching the comments. This can be mitigate by moving the comments query to the parent instead. This way, the two queries will be fetched in parallel:

```typescript
function Article({ id }) {
  const { data: articleData, isPending: articlePending } = useQuery({
    queryKey: ['article', id],
    queryFn: getArticleById,
  })

  const { data: commentsData, isPending: commentsPending } = useQuery({
    queryKey: ['article-comments', id],
    queryFn: getArticleCommentsById,
  })

  if (articlePending) {
    return 'Loading article...'
  }

  return (
    <>
      <ArticleHeader articleData={articleData} />
      <ArticleBody articleData={articleData} />
      {commentsPending ? (
        'Loading comments...'
      ) : (
        <Comments commentsData={commentsData} />
      )}
    </>
  )
}
```

Another way to flatten this waterfall would be to prefetch the comments in the `<Article>` component or to prefetch both of these queries at router level on page load or page navigation (see Prefetching & Router Integration below).

If we have a Dependent Nested Component Waterfall, in which the second query (in the child component) is dependent on the parent, we can't just hoist the query to the parent or add prefetching. One option is to refactor our API to include the data in one single query. Another more advanced solution is to leverage Server Components to move the waterfall to the server where latency is lower - but this can be a big architectural change.

Note that you can have good performance even with a few query waterfalls.

### Prefetching & Router Integration

You can use prefetch to populate the cache with data you know you might need ahead of time, leading to a faster experience.

There are a few different prefetching patterns:
1. In event handlers
2. In components
3. Via router integration
4. During Server Rendering (another form of router integration)

A few notes about the functions `prefetchQuery` and `prefetchInfiniteQuery` first:
* They use the default `staleTime` to determine if cache is fresh
    * You can specify a specific `staleTime` if needed, which will be used ONLY for the prefetch
    * To ignore the `staleTime` you can use the `ensureQueryData` function
    * If you are prefetching on the server, set a default `staleTime` higher than 0 for that `queryClient` to avoid having to pass a specific `staleTime` to every prefetch call
* These functions always return `Promise<void>`, no data. And they never throw errors because they usualy try to fetch again in a `useQuery`. For data and/or error handling, you need to use `fetchQuery` or `fetchInfiniteQuery`

#### Prefetch in event handlers

Straightforward: Call it as a function on some event, such as onMouseEnter.

#### Prefetch in components

Technically, you can make a query and ignore the results, using for example `useQuery`. But this way won't work if you have to combine suspensful queries. In that case can use `usePrefetchQuery` or `usePrefetchInfiniteQuery`.

If you need to, you can also prefetch inside a query function. For example, if you know that you need to fetch comments of an article everytime an article is fetched, it makes sense.

You can also prefetch in an effect, but if you are using `useSuspenseQuery` in the same copmonent, this won't run until after the query finishes!

#### Router intergration

TODO

### Server Rendering & Hydration

> Reminder: Server Rendering is the act of generating the initial html on the server, so that the user has some content to look at as soon as the page loads. This can happen on demand when a page is requested (SSR) or ahead of time either because a request was cached or at build time (SSG).

With Server Rendering we can transform multiple waterfall requests into something like:

```
1. |-> Markup (with content AND initial data)
2.   |-> JS
```

So that the user can already see the content and, when 2. finishes, the page is interactive and clickable. This skips a third step of code running on the client to fetch some data.

From the server perspective, we need to:
1. **prefetch** that data before we generate the markup
2. **dehydrate** that data into a serializable format we can embed in the markup.

On the client, we need to:
1. **hydrate** that data into a React Query cache to avoid doing a new fetch.

With a little setup, you can:
- use `queryClient` to prefetch queries during a preload phase
- pass a srialized version of that `queryClient` to the rendering part of the app and reuse it there

After prefetching on the server, you can return a `dehydrate(queryClient)` and wrap your tree with `<HydrationBoundary state={dehydratedState}></HydrationBoundary>`

Note that `dehydrate(...)` only includes succesful queries, not failed ones, and that `queryClient.prefetchQuery(...)` never throws error. If you want to handle errors, you have to use `queryClient.fetchQuery(...)`.

If you want to include failed queries in the dehydrated state, you can use the option `shouldDehydrateQuery`.

Note that if we "optimaze" a page, like `/feed`, but we navigate to it within a link from another page, in SPA's server rendering only works for the initial page load, not for any subsequent navigation.

### Advanced Server Rendering

The first step of any React Query setup is always to create a `queryClient` and wrap your application in a `QueryClientProvider`.

Using React Query with Server Components (SC) makes most sense if:
* You already use React Query and want to migrate to SC without rewriting all the data fetching
* You want a familiar programming paradigm but with the benefits of SC
* Your framework of choice doesn't cover some use case that React Query does

> **Tips**: If you're just starting out with a new SC app, avoid bringing React Query until you actually need it. If you do use it, avoid using `queryClient.fetchQuery` unless you need to catch errors. If you don't use it, don't render its result on the server or pass the result to another component, even a Client Component. From the React Query perspective, treat SC as a place to prefetch data, nothing more.

#### Streaming with SC

Next.js app router automatically streams any part of the application that is ready to be displayed to the browser as soon as possible, so finished content can be displayed immediately without waiting for still pending content, and it does it along with `<Suspense>` boundary lines.

> Note that, if you create a `loading.tsx` file, this automatically creates a `<Suspense>`
 boundary behind the scenes.

React Query can also serialize ("dehydrate") queries that are still loading (`pending`) and send them to the client as part of the streamed response. So:
* You can start data fetching early on the server and send partial HTML immediately
* The browser shows the HTML while the remaining queries continue loading
* When those queries finish, the results stream in and React hydrates them without reloading the page

To make this work, we have to instruct the `queryClient`to also `dehydrate`pending queries (either globally or directly with an option), and we will want to use the `getQueryClient()` function in our server component and our client provider. (This works in Next.js and SC because React can serialize Promises over the wire when you pass them down to Client Components.)