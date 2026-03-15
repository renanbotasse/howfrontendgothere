
Every feature you use in CSS, every HTML element, every JavaScript API went through a process before it landed in a browser. That process is slow, messy, and sometimes contentious — and understanding it explains a lot about why the web evolves at the pace it does, and why some things that seem like obvious improvements take years to ship.

---

## Who actually decides

There's no single body that controls the web. Three organizations do most of the work, and they handle different parts of the stack.

The **W3C** (World Wide Web Consortium) was founded by Tim Berners-Lee in 1994 to coordinate web standards. For years, it was the main venue for HTML and CSS specifications. Today it still handles a wide range of specs: accessibility guidelines (WCAG), CSS, SVG, web components, and more.

The **WHATWG** (Web Hypertext Application Technology Working Group) was created in 2004 by Apple, Mozilla, and Opera as a reaction to W3C's direction. The W3C had abandoned HTML in favor of XHTML 2.0, a stricter XML-based format that was largely incompatible with the existing web. The browser makers disagreed with that direction and started their own group to work on what eventually became HTML5.

The tension between W3C and WHATWG lasted for years. In 2019, they signed an agreement: WHATWG would maintain the "living standard" for HTML, and W3C would publish snapshots of it. HTML is now effectively controlled by WHATWG.

The **IETF** (Internet Engineering Task Force) handles network protocols — HTTP, TLS, DNS. This is where HTTP/2 and HTTP/3 were standardized. The IETF has a famously open, engineer-driven process: anyone can participate, and rough consensus among working group members drives decisions.

JavaScript itself is standardized by **TC39**, a committee within Ecma International. TC39 includes representatives from browser vendors, major companies, and individual invited experts.

---

## How a feature moves from idea to browser

TC39's process is the most visible to frontend developers, so it's worth understanding in detail.

A proposal starts at Stage 0 — essentially "someone had an idea." There's no formal requirements at this stage. Stage 1 means a TC39 champion has taken responsibility for the proposal and identified the problem it solves. Stage 2 means the committee expects the feature to be standardized in some form. Stage 3 means the spec is complete and browsers can start implementing. Stage 4 means at least two independent implementations exist and the feature will be in the next ECMAScript edition.

> 📸 **IMAGEM AQUI**
> Abra: https://tc39.es/process-document/
> Capture a tabela de stages (Stage 0 a Stage 4 com critérios)
> Salve e faça upload.

The important thing about Stage 3 is that browsers don't wait for Stage 4 to ship features. Once something is at Stage 3 with stable enough spec text, Chrome or Firefox might ship it behind a flag, then unflag it when they're confident in the implementation. This is how optional chaining (`?.`), nullish coalescing (`??`), and `Promise.allSettled` all landed in browsers before they were officially part of a published ECMAScript edition.

CSS works differently. The CSS Working Group at W3C maintains separate specs for different parts of CSS — Flexbox is one spec, Grid is another, Custom Properties is another. Each moves through levels independently. "CSS Level 3" is not a thing the way "HTML5" was marketed as a thing; it's shorthand for a collection of separate modules, each at its own maturity level.

---

## Why some features take years

Browser makers have to actually implement whatever the spec says. Implementation is hard. A CSS property that seems simple might interact in complex ways with existing layout algorithms. A JavaScript API that makes sense in isolation might create security problems in combination with other features.

Browser vendors are also conservative because of backwards compatibility. The web has a rule that nothing breaks. If a site built in 2005 still works in 2025, that's by design. Every new feature has to coexist with everything that came before it. This constraint rules out entire categories of changes that would be straightforward in a context without legacy.

And sometimes implementers just disagree. A feature can be stuck in spec discussions for years because WebKit has one opinion and Firefox has another. `gap` for flexbox was in the CSS spec for years before all major browsers supported it, because the property existed earlier for Grid and the cross-context semantics needed to be worked out.

---

## The browser vendors are the real power

In theory, standards bodies are neutral. In practice, the browser vendors are the ones who implement specs, so their opinions carry enormous weight. A feature that Google, Apple, and Mozilla all oppose is not going to ship, regardless of how many web developers want it. A feature that Chrome implements and ships to 65% of the web has effectively won, even if the spec isn't finished.

This dynamic produced the current situation with Interop, an initiative where all major browser vendors agree on a set of features to prioritize in a given year. Interop 2022 included Container Queries, which had been technically possible for years but lacked aligned implementation momentum. Coordinating agreement among vendors was what unlocked it.

It also produced the situation with WebKit. Apple's policies prevented third-party browser engines on iOS for years, which meant all iOS browsers used WebKit. Safari's pace of implementation historically lagged Chrome and Firefox, which meant iOS users couldn't use certain web features even when the feature was fully standardized and available elsewhere. This created sustained developer frustration, and regulatory pressure in Europe eventually forced Apple to allow alternative engines on iOS.

---

## What "baseline" means

Baseline is a framework from the Web Platform Interop project for communicating when a feature is safe to use. A feature achieves "Baseline Newly Available" when it's supported in Chrome, Edge, Firefox, and Safari. After 30 months of that availability, it becomes "Baseline Widely Available," meaning it can be used without worrying about support for older browser versions.

> 📸 **IMAGEM AQUI**
> Abra: https://web.dev/baseline
> Capture o diagrama que explica o que é Baseline (Newly Available vs. Widely Available)
> Salve e faça upload.

MDN and web.dev now show Baseline status on feature documentation pages. It's a practical heuristic — not perfect, but much more useful than manually checking caniuse for every feature and deciding what percentage of unsupported users you're comfortable ignoring.

---

## The web is a negotiation

Standards are not handed down from a committee. They're negotiated between organizations with different priorities, different user bases, and different economic interests. Google has reasons to push certain features. Apple has reasons to resist others. Mozilla advocates for privacy. Developers want things to ship faster.

The result is a platform that moves slowly but almost never breaks backward compatibility, that accumulates features over decades, and that runs on devices from phones to refrigerators with no installation step required.

Whether that's the right tradeoff depends on what you're comparing it to. But it's not an accident, and it's not dysfunction. It's the predictable output of a system designed to serve a lot of competing interests at once.

---

*Next: How the Static Web Worked. Before JavaScript owned the UI, pages were documents. The server generated HTML and sent it down. That model had real advantages, and understanding it explains why server-side rendering came back.*
