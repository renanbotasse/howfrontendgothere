
Rails was not wrong. Neither was PHP, or Django, or any of the server-side frameworks that rendered HTML and sent it down to browsers. What they were was incomplete, they didn't have a good answer for rich client-side interactivity. And when that became the main thing developers wanted to build, the industry moved away from them.

The server-rendered web that returned around 2020-2024 is not a reversal. It's a synthesis. The new tools, Next.js, Remix, Astro, kept what worked about server rendering and added what the SPA era discovered about building interactive UIs. Understanding the synthesis shows what the industry actually learned from the detour.

---

## What the SPA era was right about

Rich client-side interactivity is genuinely better for certain things. A form that validates inline, provides autocomplete, and gives instant feedback on every input, that's a better experience than submitting a form, waiting for a page reload, and reading the error at the top of the returned page.

State that persists across navigation without a server round trip is genuinely faster. When you're in a Spotify-like UI and you click between tabs, not reloading the entire page for each tab is the right experience.

Component-based UI development, hooks, the declarative model, these are real improvements to how frontend code is organized and maintained. The SPA era produced them, and they're worth keeping.

---

## What the SPA era was wrong about

The default mode of a React application circa 2018-2020 was: send an empty HTML shell, download JavaScript, execute JavaScript, fetch data, render. Every page had this flow, whether or not the page had any client-specific interactivity.

A marketing page was a React SPA. A blog post was a React SPA. A documentation site was a React SPA. These are fundamentally documents, their content doesn't change based on user interaction after initial load. Building them as SPAs was importing all the costs of the SPA model, JavaScript bundle size, time to first contentful paint, SEO complexity, for no benefit.

The error was treating the SPA as an architectural default rather than as a tool for a specific class of problems.

---

## Next.js and the hybrid model

Next.js 9 (2019) introduced file-system routing with per-page rendering strategies. The same application could have static pages, server-rendered pages, and client-rendered pages, each handled correctly. This was the first major framework to make hybrid rendering the default model rather than an add-on.

The App Router, introduced in Next.js 13 (2022) and stabilized in 2023, went further. React Server Components, components that render on the server and never ship JavaScript to the client, became the default. A page component is a server component by default. You opt into client-side interactivity with `'use client'` at the top of a file.

This inverts the SPA default. Instead of "everything is client-side unless you opt into server rendering," it's "everything is server-side unless you opt into client-side." The result is that most of the application, data fetching, layout, non-interactive content, stays on the server, and only the interactive parts send JavaScript to the client.

The data fetching model changed accordingly. Instead of `useEffect` + fetch in a client component, a server component can be async and `await` data directly:

```typescript
// Server Component, no useEffect, no loading state in JS
async function ProductPage({ id }: { id: string }) {
  const product = await db.product.findUnique({ where: { id } });
  return <ProductView product={product} />;
}
```

The database call happens on the server. No API route needed. No client-side loading state. The HTML arrives with the data already in it.

---

## Remix and progressive enhancement

Remix (2021) took a different angle. Rather than React Server Components, Remix centered its model around the web platform's native request/response primitives. Loader functions handle data fetching for a route. Action functions handle form submissions. Both run on the server.

The key design decision was progressive enhancement: Remix applications work without JavaScript. Forms submit natively. Navigation works natively. When JavaScript loads, Remix enhances these interactions, preventing full-page navigates, adding optimistic UI, prefetching data on hover. But the baseline is a functional server-rendered application.

This is closer to the original Rails model than Next.js is, the server handles everything, and JavaScript enhances rather than replaces. It's not the nostalgia play it might look like; the underlying technology is modern, the ergonomics are modern, and the React integration is first-class.

---

## Astro and islands

Astro (2021) makes the most aggressive bet: default to no JavaScript at all. A page built with Astro renders to static HTML. Components can be written in React, Vue, Svelte, or plain HTML. By default, they render to HTML and ship nothing to the browser.

When you need a component to be interactive, a search box, a carousel, a modal, you add a `client:load` or `client:visible` directive:

```astro
---
import SearchBox from './SearchBox.tsx';
---

<SearchBox client:load />
```

That component loads JavaScript and hydrates. Everything else on the page is static HTML with no JavaScript overhead.

This is the "islands architecture", islands of interactivity in a sea of static content. For content-heavy sites, documentation, marketing, blogs, Astro's approach produces the best performance characteristics of any framework because it ships the least JavaScript by default.

---

## What the synthesis looks like

The current best practice, reflected across these frameworks, is roughly: use server rendering for the outer structure and data fetching, add client-side interactivity only where users actually interact. Don't default to client rendering. Don't default to server rendering that ignores interactivity. Choose the right rendering mode for each part of the application based on what that part needs to do.

This is harder to describe as a simple rule than "use React" was. It requires thinking about each page's requirements and choosing accordingly. But that complexity reflects the actual diversity of what web applications do, some parts are documents, some parts are interactive, and treating both the same way was the mistake the SPA era made.

The server didn't come back because the SPA was wrong. It came back because the SPA was right about the parts it was right about, and the industry had a decade to figure out which parts those were.

---

*Next: How Rendering Became a Spectrum. Static generation, server-side rendering, client-side rendering, incremental static regeneration, streaming, partial hydration, these aren't different tools, they're points on a spectrum. Understanding the spectrum means understanding the tradeoffs.*
