
CSS has a reputation problem that it mostly doesn't deserve anymore. For a long time, it earned that reputation, the early versions were limited enough that basic layouts required hacks, and those hacks were so ubiquitous that they looked like how CSS was supposed to work. But the language that exists in 2024 is not the language that existed in 2004 or even 2014.

Understanding how CSS evolved is understanding why certain patterns persisted long after better options existed, and why the modern features are as powerful as they are.

---

## The early years: styling documents

CSS was designed to style documents, not build layouts. The initial specification, published in 1996, covered fonts, colors, text spacing, and basic positioning. It assumed you were formatting a text document with some visual hierarchy, which was accurate, because that's what the web was.

The `display` property controlled whether an element was block or inline. `float` was for wrapping text around images, borrowed from print layout concepts. `position` let you place things at absolute coordinates relative to their containing block.

Browsers implemented CSS inconsistently. The IE box model computed widths differently from the CSS spec. Support for properties varied across browsers. This wasn't hypothetical incompatibility, it was real work to make a page look the same in IE, Firefox, and Opera.

---

## Hacks as the foundation

When a layout tool doesn't exist, developers find workarounds. The workarounds of early CSS became standard practice because they worked reliably enough and because there was nothing else.

`float` was repurposed for multi-column layouts. You floated elements left and right to create columns. Then the parent element collapsed because floated children were taken out of normal document flow. The fix was the "clearfix" hack, adding a pseudo-element after the floated content that forced the parent to recognize the floated children's height. Every CSS framework included a clearfix utility class.

`display: table` was used to achieve vertical centering, something CSS had no direct mechanism for in inline contexts. You set an element to display as a table cell and used `vertical-align: middle`. It worked. It was semantically wrong in ways that could affect screen readers and SEO. Developers used it anyway.

Negative margins, `overflow: hidden` on containers, 1px transparent gifs as spacers, these were solutions to real problems. The problems existed because the layout tools didn't.

---

## Flexbox arrives

The Flexible Box Layout Module was in development for years before it became usable. The spec went through several iterations, and browser implementations tracked different draft versions, meaning the same property names meant different things in different browsers at different times. Developers who tried to use flex in 2011 had to write three different versions of the same layout rules.

By 2015, Flexbox was stable and supported across all major browsers. The experience of using it was immediately different from float-based layouts.

Flexbox handles one axis at a time: row or column. Children of a flex container can be aligned, distributed, and ordered with a small set of properties. Vertical centering became a one-liner. Equal-height columns stopped requiring hacks. Navigation menus that previously required floats and careful margin management worked cleanly.

The mental model is a container that controls how its children arrange themselves. You stop thinking about individual elements pushing each other around and start thinking about the container's distribution strategy.

---

## Grid completes the layout picture

CSS Grid was designed for two-dimensional layouts: rows and columns together. It's fundamentally different from Flexbox, not better or worse for most cases, but differently suited.

Grid defines a layout template with explicit row and column tracks, and places items into that grid. This is how print layout has worked for centuries, and bringing it to CSS unlocked designs that previously required JavaScript or elaborate HTML structures:

```css
.layout {
  display: grid;
  grid-template-columns: 250px 1fr;
  grid-template-rows: auto 1fr auto;
}

.sidebar { grid-area: 1 / 1 / 3 / 2; }
.main { grid-area: 1 / 2 / 2 / 3; }
```

Grid shipped in all major browsers in 2017. The adoption was faster than Flexbox because the spec was more stable before browser vendors shipped it.

![CSS Grid: container with 1fr/2fr/1fr column tracks and item placement](/images/css-grid-layout.svg)

---

## Custom Properties, CSS finally has variables

CSS Custom Properties (usually called CSS variables) arrived in major browsers around 2016-2017. You declare a custom property with a double-dash prefix and use it with `var()`:

```css
:root {
  --brand-color: #3b82f6;
  --spacing-md: 1rem;
}

.button {
  background: var(--brand-color);
  padding: var(--spacing-md);
}
```

This sounds basic. Sass and Less had variables for years before this. What makes custom properties different is that they're part of the DOM. They inherit. They can be changed at runtime with JavaScript. A child element can override a custom property without changing the parent. A dark mode implementation can change `--brand-color` on the `:root` by toggling a class, and every element that uses it updates automatically.

This is more powerful than preprocessor variables, which are resolved at compile time and produce fixed values in the output CSS.

---

## Container Queries, the missing piece

Media queries let you change styles based on the viewport size. Container Queries, which reached broad browser support in 2023, let you change styles based on the size of a containing element.

This distinction matters because components are reused in different contexts. A card component might appear in a wide sidebar on desktop and in a narrow column on mobile. With media queries, you'd have to know where the card is placed to write the right breakpoints. With container queries, the card responds to its own available space:

```css
.card {
  container-type: inline-size;
}

@container (min-width: 400px) {
  .card-content {
    display: flex;
    gap: 1rem;
  }
}
```

The card doesn't care about the viewport. It cares about its container. This is the responsive design primitive that component-based development actually needed.

---

## Where CSS is now

The gap between what CSS can do and what developers reach for CSS preprocessors or frameworks to do has narrowed significantly. Custom Properties handle variables. Nesting (now native) handles what Sass nesting handled. Grid and Flexbox handle what frameworks like Bootstrap handled with float-based grids.

The legacy patterns persist because CSS is additive and backwards compatible, old code doesn't break when new features ship. Teams that learned float-based layouts in 2010 might still be writing them in 2024, because the hacks work and there's no forcing function to learn the new tools.

But the new tools are genuinely better. The layouts that required three files and a JavaScript polyfill in 2015 require three lines of CSS in 2024. That's not marginal improvement.

---

*Next: How We Tried to Style Components and Never Agreed. Once components became the unit of UI, CSS-in-JS, CSS Modules, utility classes, and scoped styles all competed to be the right way to style them. The disagreement continues.*
