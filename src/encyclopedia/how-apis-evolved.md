
In 2012, if you were building a web application that needed to fetch data, the path was clear: design REST endpoints, serialize data as JSON, fetch from the client with XMLHttpRequest or jQuery. That was how you did it.

By 2024, you could be using REST, GraphQL, tRPC, server functions, or some combination. Each option reflects different assumptions about who owns the API contract, how much control the client needs over the data shape, and how closely the frontend and backend are coupled.

---

## REST and the first client-server split

REST (Representational State Transfer) is an architectural style described by Roy Fielding in 2000. A RESTful API organizes resources as URLs and uses HTTP methods to express operations: `GET /users/123` to read a user, `PUT /users/123` to update, `DELETE /users/123` to delete.

The appeal was conceptual simplicity. URLs are human-readable. HTTP methods map to CRUD operations. JSON is the universal data format. Any client in any language can talk to a REST API.

The practical issues are well-documented. Over-fetching: the endpoint returns more fields than the client needs, wasting bandwidth. Under-fetching: the client needs to make multiple requests to assemble the data for one view — get the user, then get their orders, then get order details. The N+1 problem is endemic to REST when clients need related data.

REST also has no formal contract. A backend developer can change a field name or remove a field from a response and the client breaks at runtime, not at development time. The API contract is implicit — it lives in documentation that may or may not be current.

---

## GraphQL — the client defines the query

GraphQL, developed at Facebook and open-sourced in 2015, inverts the REST model. Instead of the server defining what each endpoint returns, the client sends a query that specifies exactly what data it needs:

```graphql
query {
  user(id: "123") {
    name
    email
    orders(last: 5) {
      id
      total
      status
    }
  }
}
```

The server returns exactly this shape. No over-fetching. No under-fetching. One request gets everything the component needs, regardless of how many different data sources the server has to aggregate.

GraphQL also provides a schema — a formal contract for all types and operations. Type generation tools read the schema and produce TypeScript types for every query and mutation. When the schema changes, the type errors surface at development time.

The adoption of GraphQL was significant. Companies at scale — GitHub, Shopify, Twitter — moved to GraphQL APIs partially or entirely. Apollo Client became the dominant client library.

The criticism that developed over time was about complexity. A GraphQL server requires schema design, resolver implementation, and often a dedicated gateway layer. The caching model is more complex than REST because the response shape varies per query. Performance analysis is harder — a single query might generate dozens of database calls depending on how resolvers are implemented. The N+1 problem moves from the client to the server.

GraphQL fits well for large teams where frontend and backend are separate and the frontend needs flexibility in what data it fetches. For smaller teams or applications with fewer distinct clients, the overhead can exceed the benefits.

---

## tRPC — full-stack TypeScript without a schema layer

tRPC (TypeScript Remote Procedure Call) emerged around 2021 for a specific use case: TypeScript codebases where the frontend and backend share a codebase or are tightly coupled. Instead of defining an API and generating types from it, tRPC infers types from the server-side function definitions:

```typescript
// Server
const router = t.router({
  getUser: t.procedure
    .input(z.object({ id: z.string() }))
    .query(({ input }) => db.user.findUnique({ where: { id: input.id } }))
});

// Client — fully typed, IDE autocomplete works
const user = await trpc.getUser.query({ id: '123' });
```

The client knows the exact return type of `getUser` without any code generation step. TypeScript's type inference flows across the client-server boundary.

This works well for full-stack frameworks like Next.js where the frontend and backend live in the same repository. It does not work for APIs consumed by multiple clients (mobile apps, third-party integrations) because tRPC is tightly coupled to the TypeScript runtime.

---

## Server Actions — the API layer disappears

React Server Actions, available in Next.js 14, take the coupling one step further: you can call server-side functions directly from client components without defining an API at all.

```typescript
// app/actions.ts
'use server';

export async function updateUser(id: string, data: UserData) {
  await db.user.update({ where: { id }, data });
  revalidatePath('/users');
}

// ClientComponent.tsx
import { updateUser } from './actions';

function EditForm({ userId }) {
  return (
    <form action={updateUser.bind(null, userId)}>
      ...
    </form>
  );
}
```

The function lives in the codebase, has TypeScript types, and runs on the server. The client calls it as a function. The framework handles the HTTP request under the hood.

For form submissions and mutations in Next.js applications, this model eliminates the need to define API routes entirely. The tradeoff is that it works only within the framework — there's no general API that a mobile app or external service can call.

---

## The right tool for the context

REST still makes sense for public APIs where clients are diverse and unknown. A public API that mobile apps, browser clients, and third-party integrations all consume is better as a stable REST API with good documentation.

GraphQL makes sense for large organizations with separate frontend teams who need control over the data they fetch. The overhead is justified when the flexibility and formal contract provide real value.

tRPC makes sense for full-stack TypeScript applications where the frontend and backend are in the same repository and external API consumers are not a concern.

Server Actions make sense for form submissions and mutations in Next.js applications where simplicity is more important than having a discrete API layer.

The evolution isn't toward one winner. It's toward a situation where the appropriate abstraction depends on the boundary between frontend and backend, who controls each side, and what other clients need access to the same data.

---

*Next: How Real-Time Came to the Web. WebSockets, Server-Sent Events, long polling — the web was designed for request/response and had to be extended to support push-based communication. Each extension made different tradeoffs.*
