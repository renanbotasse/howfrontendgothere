
Somewhere between 2010 and 2015, the web frontend stopped being a presentation layer and became the application. Not a view layer that displayed data the server assembled, an actual application, with routing, state management, data fetching, form handling, and business logic running entirely in the browser.

This was called the Single-Page Application, and it was both a genuine achievement and a very expensive mistake, depending on which parts of the outcome you focus on.

---

## What SPAs were reacting to

The frustration with traditional server-rendered pages was real. Full-page reloads felt janky. Navigation broke the URL in strange ways. Forms that failed validation lost everything the user had typed unless the developer was careful. The user experience of desktop software, instant feedback, no page reloads, persistent state, was the benchmark, and web pages were nowhere close.

Gmail in 2004 showed that a web application could feel like software. Google Maps in 2005 showed that complex interactions could happen without page reloads. The question for the next generation of web developers was: how do you build that for everything?

AJAX was the mechanism. JavaScript frameworks were the structure. Backbone.js in 2010, Ember.js and AngularJS in 2011, then React in 2013. Each offered a different model for organizing the code that ran entirely in the browser.

---

## The SPA architecture

A single-page application is literally that: one HTML page. The server returns an empty shell with a JavaScript bundle. The JavaScript runs, fetches data from an API, and builds the UI from scratch in the browser. Navigation between "pages" is simulated, JavaScript intercepts the click, updates the URL via the History API, unmounts the current view, and mounts the new one.

```
Server returns:
<html>
  <body>
    <div id="root"></div>
    <script src="bundle.js"></script>
  </body>
</html>

JavaScript then:
1. Download (100-500kb+ often)
2. Parse
3. Execute
4. Fetch data from API
5. Render UI
```

Everything the user sees is produced by JavaScript running in their browser. The server's job is to serve the static files and respond to API calls.

This architecture works well for applications where users are logged in, sessions are long, and subsequent navigation is much faster because the JavaScript is already loaded. Gmail, Figma, Notion, Linear, these are cases where the SPA model genuinely fits.

---

## What went wrong

The problem was treating this as the default model for everything.

A marketing page doesn't need a JavaScript runtime to display static content. A blog doesn't need client-side routing. A product catalog doesn't need to fetch its data after the page loads. But once teams adopted React or Angular as their standard stack, everything got built as an SPA regardless of whether the application's needs justified it.

The costs were concrete. The first load required downloading, parsing, and executing a JavaScript bundle before anything appeared. Typical React production bundles in 2018-2020 were 200-500kb minified and gzipped. On a slow mobile connection, that was seconds of blank screen. On a low-end device with limited CPU, parsing and executing the bundle added more seconds.

Core Web Vitals, Google's performance metrics that started affecting search rankings in 2021, made the cost visible in numbers. Largest Contentful Paint, how long until the main content appears, was terrible for SPAs that loaded an empty div and then fetched data. First Input Delay, how quickly the page responds to the first user interaction, was terrible while the main thread was busy executing JavaScript.

---

## State management becomes its own problem

When the server rendered HTML, state lived in the database. The browser was stateless between requests.

SPAs put state in the browser. Now you had to decide what lived where. UI state (is this dropdown open?) belongs in component state. Server data (what are this user's orders?) needs to be fetched, cached, synchronized, and kept fresh. User preferences might need to persist across sessions.

Redux arrived in 2015 and provided a predictable model: one global store, immutable state, actions that described what happened, reducers that produced new state. It was explicit, traceable, and testable.

It was also verbose and opinionated in ways that caused friction. A simple UI change required writing an action, a reducer, and connecting a component to the store. The boilerplate became a meme.

React Query and SWR arrived around 2019-2020 with a different observation: a lot of what developers were putting in Redux was actually server state, data that lived on the server and needed to be cached, synchronized, and refreshed. Managing it with client-state tools was the wrong abstraction. React Query handled caching, background refetching, pagination, and optimistic updates with a much simpler API.

The state management story is still evolving. Zustand replaced Redux for simple cases. Jotai and Recoil experimented with atomic state. Server Components in React moved data fetching to the server, eliminating the problem for those cases. Each tool reflects a lesson learned from the pain of treating server state as client state.

---

## The pendulum swings

By 2020, the frontend community was having a visible reckoning. "The costs of JavaScript frameworks" became a genre of blog post. "Just use less JavaScript" became a genuine recommendation from performance engineers. Astro shipped in 2021 with a model that rendered to static HTML by default and added JavaScript only for interactive components.

Next.js and Remix pushed toward server-side rendering with hydration. The idea: render the full HTML on the server for fast initial load, then hydrate with JavaScript to make it interactive. Best of both worlds in theory. In practice, hydration has its own costs, the browser has to download and execute the JavaScript anyway, match it against the server-rendered HTML, and attach event handlers. A component that renders on the server but needs full hydration doesn't save as much as it seems.

React Server Components represent a deeper rethink: some components render on the server and never ship JavaScript to the client at all. No hydration for those components. The client only receives the rendered output. It's a fundamental change to the component model that's still working its way through the ecosystem.

The SPA era taught the industry what the client could do. The post-SPA era is figuring out what the client should do.

---

*Next: How React Changed the Way We Think About UI. React didn't win because it was the best technically, several frameworks from the same era were competitive. It won because its mental model was better, and understanding that model explains why it's still dominant a decade later.*
