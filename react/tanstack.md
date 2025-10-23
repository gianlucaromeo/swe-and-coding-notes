# TanStack

## ♥️ TanStack Query

### `QueryClient`

The central manager in TanStack Query:
* Holds cache of all queries.
* Provides API to fetch, refetch, remove queries.
* Keeps configuration for retries, garbage collection, defaults.

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
## 💙 TanStack Table


### Core objects and types

* **Column Defs**: Objects used to configure a column and its data model, display templates, and more.
    
    * **Accessor columns** have underlying model -> can be sorted, filtered, grouped, etc.
    * **Display columns** don't have a data model -> cannot be sorted, filtered, grouped, etc.
    * **Grouping columns** don't have a data model -> cannot be sorted, filtered, grouped, etc.

* **Table**: The core table object containing both state and API.

* **Table Data**: The core data array you provide the table.

* **Columns**: Each column mirrors its respective column def and also provides column-specific APIs.

* **Rows**: Each row mirrors its respective row data and provides row-specific APIs.

* **Header Groups**: Header groups are computed slices of nested header levels, each containing a group of headers.

* **Headers**: Each header is either directly associated with or derived from its column def and provides header-specific APIs.

* **Cells**: Each cell mirrors its respective row-column intersection and provides cell-specific APIs.

TanStack Table comes with many features, each with their own associated options and API:
* Column Ordering
* Column Pinning
* Column Sizing
* Column Visibility
* Expanding
* Column Faceting
* Column Filtering
* Global Faceting
* Global Filtering
* Grouping
* Pagination
* Row Pinning
* Row Selection
* Sorting
* Virtualization

