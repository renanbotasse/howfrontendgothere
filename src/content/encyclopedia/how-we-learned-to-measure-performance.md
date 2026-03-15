
"My site is fast" used to mean "it loads quickly on my laptop, on my home WiFi, with nothing else open." That's not a measurement. It's a feeling.

Web performance has a measurement problem that goes deeper than just having the wrong tools. The experience of loading a page varies with network speed, device CPU, geographic distance to the server, what else is loaded in the browser, and hundreds of other variables. A single number for "page load time" obscures all of that variation.

The industry's gradual shift toward user-centric performance metrics, measuring what users experience, not what servers send, changed how frontend developers think about performance work.

---

## The old metrics and why they weren't enough

The early web performance metrics were server-side: time to first byte (TTFB), the time from when a request was made to when the first byte of the response arrived. This measured the server's response time, not the user experience.

DOMContentLoaded and the `load` event were the browser-side metrics most developers tracked. DOMContentLoaded fires when the HTML is parsed; `load` fires when all resources (images, scripts, stylesheets) have finished downloading. Optimizing for `load` meant the page was technically "done", all assets had arrived, but users might still be looking at a blank page because JavaScript hadn't rendered any content yet.

The problem these metrics share: they measure technical events, not perceptual ones. A user doesn't care when the DOM is loaded. They care when they can see something useful. They care when they can click a button and have it respond. They care when the page stops moving around while they're trying to read it.

---

## Core Web Vitals

Google introduced Core Web Vitals in 2020 as a standardized set of metrics tied to user perception. Three metrics became the initial standard, directly targeting the perceptual experience.

**Largest Contentful Paint (LCP)** measures when the largest visible content element, usually a hero image or the main text block, finishes rendering. A fast LCP means the user sees the main content quickly. The threshold is 2.5 seconds on the "good" range.

LCP addresses the biggest frustration with SPA blank screens. An SPA that shows a spinner for three seconds while JavaScript loads has a bad LCP. A server-rendered page that arrives with content has a good LCP if the network is fast.

**First Input Delay (FID)** measured the time from when a user first interacts with the page (clicks, taps, key presses) to when the browser is able to respond to that interaction. A high FID means the main thread is busy with JavaScript when the user tries to interact, common during the initial JavaScript parse and execution phase.

**Cumulative Layout Shift (CLS)** measures unexpected visual movement of page content. Elements that jump around as the page loads, an image that pushes text down when it finishes loading, a font that changes the text reflow when it renders, produce a high CLS score. The threshold is below 0.1 for "good."

![Core Web Vitals: LCP, INP, and CLS with Good/Needs Improvement/Poor thresholds](/images/core-web-vitals.svg)

---

## INP replaces FID

In 2024, Google replaced FID with Interaction to Next Paint (INP). FID measured only the first interaction; INP measures the worst-case interaction latency throughout the entire page session. A page that responds well to the first click but becomes sluggish after some JavaScript has run shows a worse INP than FID.

INP is a more realistic measure of interactivity because real users interact with pages more than once. An interface that freezes after a few interactions fails the user even if its FID was excellent.

The 200ms threshold for "good" INP reflects what human perception research shows about interaction responsiveness, delays under 200ms feel instantaneous, delays over 500ms feel sluggish.

---

## Why these metrics became ranking signals

Google announced in 2020 that Core Web Vitals would become ranking signals for search results starting in 2021. This was the moment that made frontend performance an engineering priority for businesses that cared about organic search traffic.

Before the ranking signal announcement, performance was a best practice that many teams deprioritized when it competed with shipping features. After, it had a measurable business consequence. Companies that hadn't invested in performance suddenly had justification for that investment.

The effect on the industry was significant. Performance audits became standard parts of frontend work. Largest Contentful Paint was added to developer conversations in the same way bundle size had been years before. Frameworks that optimized for Core Web Vitals by default gained adoption.

---

## Real User Monitoring vs. Synthetic Testing

Lighthouse and PageSpeed Insights simulate page loading in controlled conditions. Useful for development, but not representative of real users who have different devices, networks, and browsing histories.

Real User Monitoring (RUM) collects performance data from actual page loads on actual devices. Google's Chrome User Experience Report (CrUX) aggregates Core Web Vitals data from users who opt into sharing, and this is what Google uses for search ranking, your lab test score is less important than what real users experience on your site.

Tools like Vercel Analytics, Datadog RUM, and web-vitals.js enable collecting this data from your own users. The distribution matters: p75 (the 75th percentile) is the standard threshold. If 75% of users have a good LCP, you're meeting the standard. The remaining 25% might be on slow networks or low-end devices and receive a degraded experience that still affects your ranking.

---

## What the metrics miss

Core Web Vitals are good proxies for user experience. They're not perfect.

CLS penalizes layout shifts but doesn't distinguish between small movements that go unnoticed and large ones that cause users to click the wrong element. INP measures worst-case interaction latency but doesn't capture whether the user noticed the delay.

LCP doesn't capture time to interactive, a page might have a fast LCP from a server-rendered skeleton but not be usable for several seconds while hydration runs. For applications with complex interactivity, the perceived readiness and the LCP can diverge significantly.

The metrics are also averages and percentiles over populations. A specific user on a specific device on a specific network connection has an experience that may be very different from the p75. Performance work that improves the median doesn't necessarily help the users having the worst experience.

The right response isn't to dismiss the metrics, they're the most useful proxies available. It's to understand what they measure, what they don't, and to complement them with direct user feedback when the metrics alone don't tell the full story.

---

*Next: How Security Became a Frontend Problem. XSS, CSRF, clickjacking, supply chain attacks, the browser is an execution environment for untrusted code, and building web applications responsibly means understanding the threats.*
