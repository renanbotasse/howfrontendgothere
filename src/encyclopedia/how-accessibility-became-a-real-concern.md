
For most of the web's history, accessibility was an afterthought at best. Alt text was sometimes added. Focus styles were often removed because they looked bad. Screen reader support was something companies checked a box about without verifying. The assumed user was sighted, using a mouse, with no motor or cognitive differences.

About 15% of the world's population has some form of disability. Many of them use the web. They use screen readers, keyboard navigation, voice control, switch access, and other assistive technologies to interact with pages built by developers who never tested with those tools. The result was, and in many cases still is, a web that's functionally inaccessible to a large number of people.

The shift toward accessibility as a baseline expectation has been driven by legal requirements, improved tooling, and — more recently — the recognition that accessible design is often just better design.

---

## What accessibility means technically

Web Content Accessibility Guidelines (WCAG) defines the technical standards for accessibility. The guidelines are organized around four principles: perceivable, operable, understandable, and robust. WCAG 2.1 Level AA is the most commonly required standard for legal compliance.

Concretely, the requirements include: alternative text for images, sufficient color contrast, keyboard navigation for all functionality, visible focus indicators, meaningful page structure through heading hierarchy, form inputs with associated labels, and ARIA attributes to communicate the role and state of dynamic content.

ARIA (Accessible Rich Internet Applications) is a set of HTML attributes that provide semantic information to assistive technologies. When you build a custom dropdown with `<div>` elements, the browser doesn't know it's a dropdown — it's just divs. ARIA lets you communicate the structure:

```html
<div role="combobox" aria-expanded="false" aria-haspopup="listbox">
  <input aria-autocomplete="list" aria-controls="options-list" />
</div>
<ul id="options-list" role="listbox">
  <li role="option">Option 1</li>
</ul>
```

The first rule of ARIA is to not use ARIA. If you can use a native HTML element — `<select>`, `<button>`, `<input>` — it comes with built-in accessibility behavior. ARIA is for when native elements don't provide the semantics you need, not for adding semantics to elements that shouldn't need them.

---

## Legal drivers

The Americans with Disabilities Act (ADA) in the US has been interpreted by courts as applying to websites. The wave of ADA accessibility lawsuits against retailers, healthcare providers, and financial services companies that began around 2017-2020 made accessibility a legal risk, not just a best practice.

European Accessibility Act (EAA) requirements apply to digital services and products sold in the EU. Web Content Accessibility Guidelines are referenced by law in many jurisdictions. Public sector websites in the US and EU are legally required to meet accessibility standards.

Legal risk has a way of converting "we should do this" into "we will do this, with budget." Accessibility teams at larger companies grew, and "is this accessible?" became a question asked during design review rather than during a post-launch audit that found 40 issues.

---

## The SPA accessibility regression

Screen readers work by reading the accessibility tree — a structured representation of the DOM that the browser builds and assistive technologies consume. When a server-rendered page loads, the screen reader reads the content. When the user navigates to a new page, the screen reader announces the new page.

SPAs broke this. Client-side navigation updates the URL and changes the DOM, but no actual page load happens. The screen reader doesn't know a navigation occurred. Users hear nothing and have no indication that the page changed.

Managing focus after client-side navigation — moving focus to the new page's main content or to a heading that announces the new section — is something SPA frameworks didn't handle by default and developers often didn't think to implement.

Next.js and Remix have addressed this by moving focus to the `<h1>` or announcing navigation changes on route changes. Third-party libraries like `react-aria-live` manage live region announcements. But many SPAs in production still handle navigation focus poorly.

---

## Design system accessibility

One of the most effective ways to scale accessibility is through accessible component libraries. A button component that handles focus management, keyboard events, ARIA attributes, and color contrast correctly, deployed across an application, provides accessibility without every developer having to implement it from scratch.

This is the argument for headless component libraries like Radix UI and React Aria. Both provide components that are correct by default for screen readers and keyboard navigation. Radix UI's `DropdownMenu` handles focus trapping, keyboard navigation, escape to close, and correct ARIA roles. You provide the styles; the behavior is correct.

The trade-off is that a headless component is only accessible if the styling you apply preserves accessibility. A Radix dropdown with a custom trigger that has no visible focus indicator is less accessible than the default. The library provides the mechanical foundation; the design has to not undo it.

---

## Accessibility and performance overlap

Semantic HTML — using the right elements for the right purposes — is both an accessibility requirement and a performance optimization. `<button>` gets keyboard focus for free. `<nav>` tells screen readers this is navigation. `<main>` marks the main content area. `<h1>` through `<h6>` create a navigable heading structure.

Search engines and screen readers both benefit from semantic markup. A page with a clear heading hierarchy and landmark elements is both more accessible and more searchable.

Image alt text is required for accessibility and improves image search. Form labels are required for accessibility and help users with cognitive differences fill out forms correctly. Color contrast requirements for accessibility also make content readable in bright light.

Accessible design is not usually a separate concern from good design. The constraints of accessibility — clear structure, meaningful text, keyboard operability, visual clarity — are constraints that produce better experiences for everyone.

---

*Next: How the Browser Grew Up. The capabilities available to web applications in 2024 — file system access, device cameras, Bluetooth, WebAssembly, service workers — are unrecognizable compared to 2010. The browser became an operating system.*
