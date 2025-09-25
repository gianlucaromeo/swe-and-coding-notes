# tRPC (TypeScript Remote Procedure Call)

A way of calling functions on a server from a client.

```typescript
// HTTP/REST
const res = await fetch('/api/users/1');
const user = await res.json();

// RPC
const user = await api.users.getById({ id: 1 });
```

## Vocabulary

* **Procedure**: API endpoint (query, mutation, or subscription).
    * **Query**: A procedure that gets some data.
    * **Mutation**: A procedure that creates, updates, or deletes some data.
    * **Subscription**: A procedure that creates a persistent connection and listens to changes.
* **Router**: A collection of procedures and/or routers under a shared namespace.
* **Context**: Stuff that every procedure can access.
* **Middleware**: A function that can run code before and after a procedure. Can modify **context**.
* **Validation**: You can check that a procedure call is correct and return validation error if not.

## Quickstart

Install tRPC:
```shell
npm install @trpc/server @trpc/client
```

### For backend

1. Create a router instance, initialize it (commonly called `appRouter`), and export its type (not the router itself).
2. Add procedures to the `appRouter`, with input parser to validate procedure inputs if needed.
3. Serve the API with an *adapter*.

### For client

1. Setup the client (`createTRPCClient`) passing the exported type of the router, and export it.
2. Access the API procedures on the exported tRPC object.

## Note

* tRPC is only a **typesafe API layer**, not an ORM. It defines procedures and transports inputs/output between cliend and server with full TypeScript inference.