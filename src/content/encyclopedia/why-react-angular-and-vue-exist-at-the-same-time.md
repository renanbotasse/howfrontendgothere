In 2024, three JavaScript frameworks dominate the frontend market: React, Angular, and Vue. None of the three shows signs of disappearing. This is unusual. In other ecosystems, one solution tends to win and consolidate. Python has Django as its dominant web framework. Ruby has Rails. Go has a handful of options but no clear single winner.

In JavaScript frontend, fragmentation has persisted for over a decade. Not for lack of attempts at consolidation. Each framework represents a genuinely different answer to the question of who should control the complexity of the UI.

---

## What each one actually represents

The difference between React, Angular, and Vue is not primarily about syntax. It is philosophical.

**Angular** came out of Google in 2010 as AngularJS and was completely rewritten in 2016. The premise is that frameworks should be complete and opinionated. Angular ships with routing, state management, dependency injection, forms, internationalization, and testing baked in. Everything has an official way of being done. TypeScript is mandatory. Folder structure follows established conventions.

Angular's bet is that large teams need enforced consistency. If the framework decides how everything should be done, two teams in two different cities produce code that any Angular developer can read without surprises. Freedom is sacrificed for predictability.

**React** came from Facebook in 2013 with the opposite premise: the framework should do as little as possible and leave most decisions to the developer. React is, at its core, a library for rendering UI. It does not ship with routing (you pick React Router, TanStack Router, or whatever the framework above it uses). It does not ship with state management (you pick Redux, Zustand, Jotai, or MobX). It does not ship with an official way to make HTTP requests.

React's bet is that the community produces better solutions than any central team could predict. Freedom enables innovation. The cost is that "how do you do X with React" has ten different answers depending on when you asked and who answered.

**Vue** came in 2014, created by Evan You after working at Google with AngularJS. Vue tried to find the middle ground: more structure than React, less rigidity than Angular. The template syntax resembles enriched HTML, more familiar to developers coming from jQuery or server-side template languages. Vue ships with official routing (Vue Router) and official state management (Pinia), but without mandatory TypeScript and without Angular's ceremony.

Vue's bet is that progressiveness matters. You can adopt Vue incrementally, starting with a script tag in an existing page and evolving into a full SPA. That made it popular in contexts where Angular felt too heavy and React felt too unstructured.

---

## Why they never converged

In theory, the market should select the best option and eliminate the others. Three factors keep the fragmentation alive.

Different companies have genuinely different needs. Angular makes sense for an enterprise consultancy that needs fifty developers across three countries to work on a system for ten years. React makes sense for a startup that needs iteration speed and access to the largest ecosystem possible. Vue makes sense for a mid-sized company that wants something both designers and backend developers can understand without a steep learning curve. The "best framework" does not exist outside of context.

Ecosystem inertia is real. Once an organization invests in Angular (training, internal component libraries, established patterns), the barrier to migrating to React or Vue is high. It is not just rewriting code. It is retraining people, rebuilding tooling, renegotiating conventions. Companies that adopted Angular in 2016 are still on Angular in 2024 not because Angular is superior, but because migration costs more than continuity.

Each framework kept improving. If Angular had stopped evolving in 2018, companies on Angular would have more pressure to migrate. Angular 17 (2023) delivered a significantly better developer experience than Angular 2. React continues evolving with Server Components and the new compiler. Vue 3 substantially improved the TypeScript story. Competition between them pushes each one to improve, and those improvements reduce the pressure to abandon what you already know.

---

## The layer above: Next, Nuxt, SvelteKit

The React vs. Angular vs. Vue debate got more complex with the arrival of meta-frameworks that add routing, server-side rendering, and other capabilities on top of UI frameworks.

Next.js is to React what Nuxt is to Vue and SvelteKit is to Svelte. They solve problems that UI frameworks leave open: where code runs, how pages are generated, how data comes from the server.

The meta-framework choice sometimes matters more than the UI framework choice underneath it, because it determines the deployment model, the rendering strategy, and the data fetching pattern.

---

## The next wave: Svelte, Solid, Qwik

While React, Angular, and Vue consolidated the market, a second generation of frameworks questioned assumptions all three share.

Svelte compiles to plain JavaScript with no runtime. No Virtual DOM, no reconciliation in the browser. The compiler transforms components into direct DOM manipulation. Smaller bundles and faster updates for simpler cases.

Solid uses fine-grained reactivity: only the DOM nodes that depend on a specific piece of data update when that data changes. No component re-renders. Surgical DOM updates. Benchmark-optimal performance for highly dynamic interfaces.

Qwik rethinks how JavaScript reaches the browser. Instead of downloading and executing all JavaScript before the page becomes interactive, Qwik serializes state into the HTML and loads JavaScript only when the user interacts with a specific element.

These frameworks still have smaller ecosystems. But they represent legitimate critiques of assumptions that React, Angular, and Vue never questioned. Some of those assumptions are being revisited by the larger frameworks in response.

---

## What this means in practice

If you are learning JavaScript frontend now, React maximizes job opportunities. The ecosystem is larger, demand is higher, and knowledge transfers more easily between companies.

Angular makes sense if you want to work in enterprise or consulting, or if a specific company you want to work at uses Angular.

Vue makes sense if your context values progressiveness and easier integration with non-JavaScript backends.

More important than picking the "right" one is understanding why each exists. Frameworks are not just implementation details. They are hypotheses about how UI complexity should be managed. When you understand the hypothesis, you understand the design decisions, the tradeoffs, and when the framework will start working against you.

---

*Next: How the Web Actually Works. With the frame established, we can go into the technical history that explains how we got here.*
