
State is data that changes over time and affects what the UI shows. When you're building a static page, there's no state — the HTML describes what to show and that's it. When you're building an interactive application, state is everything. A logged-in user's name, the contents of a shopping cart, whether a modal is open, what search results are currently visible, whether a request is in flight — all of it is state.

Managing state well is one of the harder problems in frontend development. The industry has produced many tools for it over the years, and the evolution of those tools is a record of what developers got wrong, what they learned, and what the problem actually is.

---

## Before there was a model

Early jQuery-era code managed state implicitly, through the DOM itself. An element had a CSS class? That was state. The text content of an element? State. Whether an element was visible? State.

This worked until it didn't. The DOM is a mutable tree. If you read state from it, you're coupling your logic to your presentation in ways that make both harder to change. Tracking down a bug required asking "what mutations ran to produce this current DOM state?" — and the answer could be anywhere in a codebase with no single source of truth.

The insight that changed things was simple: the DOM is the result, not the source. Keep state in JavaScript, derive the DOM from it.

---

## MVC frameworks and the first real models

Backbone.js in 2010 introduced a model layer to frontend code. A `Backbone.Model` held data and emitted events when it changed. Views listened to those events and updated the DOM. A `Backbone.Collection` was a list of models. Data and presentation were at least separate.

The problem with Backbone's approach was the two-way binding between models and views. When a model changed, the view updated. When a form field changed, it could update the model. In complex UIs, this created a web of listeners that was hard to trace. A change in one place could trigger changes in unexpected places.

Angular 1's two-way data binding made this more automatic but not more predictable. Binding a form input to a model property meant changes to either propagated to the other. Debugging required understanding the full binding graph, which in non-trivial applications was essentially impossible to hold in your head.

---

## Flux and the one-direction rule

Facebook developed Flux around 2013-2014 as an alternative to two-way binding. The key insight: state should flow in one direction.

An action describes something that happened. A dispatcher broadcasts the action. Stores hold state and update themselves in response to actions. Views read from stores and trigger new actions in response to user interaction.

> 📸 **IMAGEM AQUI**
> Abra: https://facebookarchive.github.io/flux/docs/in-depth-overview
> Capture o diagrama do fluxo unidirecional (Action → Dispatcher → Store → View → Action)
> Salve e faça upload.

The diagram is a loop, but it's a one-directional loop. Data doesn't go backward. This makes debugging tractable: if the UI is showing wrong data, you look at the store. If the store has wrong data, you look at which action caused it. The flow has a direction and you can follow it.

---

## Redux — principled but verbose

Redux, released in 2015 by Dan Abramov, simplified Flux into three principles: a single store for the entire application, immutable state that's never modified directly, and pure functions (reducers) that take the current state and an action and return new state.

```javascript
function counterReducer(state = 0, action) {
  switch (action.type) {
    case 'INCREMENT': return state + 1;
    case 'DECREMENT': return state - 1;
    default: return state;
  }
}
```

Redux made state changes completely predictable and traceable. The Redux DevTools extension let developers replay every action in sequence and inspect state at every step — genuinely useful for debugging.

The criticism that developed over time was about friction. Adding a simple feature to Redux-based code required writing an action type, an action creator, a reducer case, and wiring everything together. Teams wrote boilerplate generators. The Redux Toolkit (RTK) eventually codified the opinionated approach and reduced the boilerplate significantly.

But the deeper criticism was more interesting: Redux was being used for problems it wasn't designed for. Teams stored server data in Redux — user lists, product catalogs, API responses. Redux would fetch the data, put it in the store, and components would select from it. This worked but ignored caching, background synchronization, and staleness — all the problems that come with storing remote data locally.

---

## The distinction that changed everything

Tanner Linsley's React Query (2019) and the SWR library from Vercel made a distinction explicit that the industry had been missing: server state and client state are different problems.

Server state is data that lives on a server. It has a source of truth elsewhere. It can be stale. Multiple components might need the same data. Fetching it is asynchronous. It needs to be refreshed. When you update it, you want the UI to reflect the update immediately (optimistic update) before the server confirms.

Client state is UI state that has no server equivalent. Whether a dropdown is open. Which tab is selected. The current value of an unsubmitted form field. This data is truly client-owned.

Managing server state with Redux was over-engineering. Redux solved the problem of coordinating client state changes across many components. It doesn't inherently understand caching, deduplication, background refreshes, or revalidation — all the things that server state management actually needs.

React Query manages server state with an API centered around queries and mutations. You declare that a component needs certain data, and React Query handles fetching, caching, background synchronization, and error states. The component just uses the data:

```javascript
function UserProfile({ userId }) {
  const { data: user, isLoading } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId)
  });

  if (isLoading) return <Skeleton />;
  return <div>{user.name}</div>;
}
```

This pattern eliminated the need for Redux in many codebases that had only been using it to store server data.

---

## Where state management is now

The current landscape is more sensible than it was in 2018. For server state, React Query, SWR, and similar tools are standard. For client state, lighter-weight options like Zustand and Jotai replaced Redux for many teams — they provide reactivity without Redux's architectural overhead. Redux and Redux Toolkit remain valid choices for complex applications with sophisticated requirements.

React Server Components push the question in a new direction: components that render on the server can fetch data directly, without any client-side state management. The data is available during rendering and never has to cross the client-server boundary as a client-side state concern. For the data those components show, there's no state to manage on the client.

Each tool reflects an accurate model of some part of the problem. The lesson from the last decade is to use the right model for each category of state, rather than one unified solution for everything.

---

*Next: How Forms Were Never Really Solved. Forms are the oldest interactive element on the web and remain one of the most complicated. The fact that every major framework has generated at least three competing form libraries is not an accident.*
