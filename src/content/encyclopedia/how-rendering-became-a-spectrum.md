
Somewhere around 2019-2020, framework documentation started including diagrams with rendering modes on a scale. "Static" at one end. "Full server-side" at another. Options in between. The diagrams were trying to explain something the industry had taken too long to acknowledge: rendering isn't a binary choice between server and client, and picking the right approach depends on the content.

Understanding the points on the spectrum means understanding the tradeoffs each makes.

---

## Static Site Generation

A page built with static site generation (SSG) has its HTML produced at build time. You run a build command, a headless Node process renders every page, and you get a folder of HTML files. Deploying that folder to a CDN means every request returns a pre-built HTML file with zero server processing.

The performance characteristics are excellent. The HTML is already built. The CDN is geographically distributed. The time to first byte is as fast as it can be, because the response is a file read from an edge location.

The constraint is time-sensitivity. The HTML is as current as the last build. If your content changes and you don't rebuild, users see stale content. For content that changes infrequently, documentation, marketing pages, blog posts that aren't news, this is a non-issue. For content that changes every few minutes, product inventory, live event data, real-time scores, SSG either requires very frequent rebuilds or doesn't fit.

---

## Server-Side Rendering

Server-side rendering (SSR) generates HTML on each request. A user requests a page, the server runs the rendering logic, fetches data, assembles HTML, and sends it. Every request gets fresh HTML.

This is how PHP and Rails always worked. The modern SSR model adds the React component tree to the server, instead of a template, you render React components on the server and get HTML out.

The tradeoff is server resources and latency. Every page view requires server computation. Under high traffic, this means either paying for more server capacity or accepting slower response times. CDNs can cache server-rendered responses if the response is the same for all users (no personalization), but for user-specific content, each request must hit the server.

The user experience benefit is that the page always shows current data. There's no staleness concern. The HTML arrives with the right content.

---

## Client-Side Rendering

Client-side rendering (CSR) sends a minimal HTML shell and lets JavaScript build the UI in the browser. This is the original SPA model.

For authenticated applications where users are logged in, the definition of the content depends on who's asking, CSR is often appropriate. The initial HTML shell is the same for all users and can be cached on a CDN. The user-specific content loads after authentication is verified.

The cost is the time to first meaningful paint. The user sees a loading state while JavaScript downloads, executes, authenticates, fetches data, and renders. On a fast connection with a modern device, this is acceptable. On a slow connection or a low-end Android phone, it's a bad experience.

---

## Incremental Static Regeneration

Incremental Static Regeneration (ISR) is Next.js's answer to the staleness problem with SSG. Pages are built statically but can be revalidated in the background. You specify a revalidation interval: `{ revalidate: 60 }` means the page will be regenerated at most once per minute.

When a user requests a page: if it's fresh, they get the static version immediately. If it's stale, they still get the static version immediately, but a background regeneration starts. The next user gets the fresh version.

This is the "stale-while-revalidate" pattern applied to entire pages. Users always get a fast response. The content is eventually consistent. For content that updates regularly but doesn't need to be real-time, product pages, news articles, sports scores, ISR is often the best tradeoff.

---

## Streaming SSR

Traditional SSR renders the entire page before sending any of it. If fetching data takes 500ms, the user waits 500ms before seeing anything.

Streaming SSR sends the page in chunks. The parts that don't depend on slow data start arriving immediately. Parts that depend on slow data are streamed when they're ready. React's `<Suspense>` component marks the boundaries:

```jsx
<Suspense fallback={<ProductSkeleton />}>
  <SlowProductData productId={id} />
</Suspense>
```

The page shell and surrounding content arrive immediately. The product data slot shows a skeleton. When the data resolves, the real content streams in. Users see something faster without waiting for the slowest part of the page.

Next.js and Remix both support streaming. It changes the user experience on pages where some data is fast and some is slow, common for pages that aggregate multiple data sources.

---

## Partial Hydration and Islands

Hydration is the process of attaching JavaScript event handlers to server-rendered HTML. Traditional React hydration hydrates the entire page, all components get JavaScript, even the ones that never change after the initial render.

Partial hydration hydrates only the interactive components. A navigation menu that never changes needs no JavaScript. A search box that responds to user input needs JavaScript. Only send JavaScript for what needs it.

Astro implements this explicitly with `client:` directives. React Server Components implement it implicitly, server components produce HTML and nothing else, client components hydrate normally. The division of the component tree into server and client parts determines what JavaScript ships.

![Islands architecture: static HTML with interactive JavaScript islands](/images/islands-architecture.svg)

---

## Choosing points on the spectrum

The practical framework for choosing:

**Static**: content doesn't change per user, changes infrequently, maximum performance desired. Documentation, landing pages, blog posts.

**ISR**: content doesn't change per user, changes regularly but not in real time. Product pages, news articles, event listings.

**SSR**: content changes per request or per user, must be current. Search results, user dashboards with server-side data, anything requiring authentication before rendering.

**CSR**: content is highly user-specific and personalized, application is behind authentication, subsequent navigations matter more than initial load. Internal tools, SaaS apps, long-session user interfaces.

**Streaming**: any SSR page with data that varies in fetch time. Use Suspense boundaries around slow data, let the rest of the page arrive first.

**Islands/partial hydration**: content-heavy pages with limited interactivity. Marketing sites, documentation, blogs that want minimal JavaScript.

These modes can be mixed within one application. A Next.js project can have static pages, ISR pages, and server-rendered pages coexisting. The right choice is per-page, not per-application.

---

*Next: How APIs Evolved. REST, GraphQL, tRPC, server actions, the way frontend communicates with servers has changed significantly. Each approach reflects a different set of priorities about who owns the API contract.*
