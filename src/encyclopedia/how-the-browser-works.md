
The HTTP response arrived. The browser has your HTML. Now what?

Most developers stop thinking here. The network stuff is handled, the framework is rendering, the page will appear. But between receiving bytes and showing pixels, the browser does a lot of work — and that work has constraints that determine what your UI can and cannot do, regardless of which framework you're using.

Understanding the browser as a machine, not just an environment, changes how you diagnose performance problems. It also explains why React makes the choices it makes, why certain CSS properties are faster than others, and why "the page feels slow" is often a browser problem, not a JavaScript problem.

---

## There's one thread doing almost everything

Inside the renderer process — the part of the browser that runs your page — there's a main thread. That thread handles HTML parsing, CSS calculation, JavaScript execution, layout, and paint. One thing at a time.

When JavaScript runs, layout waits. When layout runs, JavaScript waits. When a user taps a button, if the main thread is busy processing something else, the tap handler waits.

This isn't a limitation. It's the architecture.

At 60 frames per second, the browser has about 16 milliseconds per frame to do everything: run JavaScript, calculate styles, lay out the page, paint, and composite. Miss that window and a frame drops. Enough dropped frames and animations stutter, scrolling feels rough, interactions feel unresponsive.

The browser calls any task that takes longer than 50 milliseconds a long task. Long tasks block everything, including input. That's why a heavy React render makes a page feel frozen even when your CPU isn't maxed out — it's occupying the main thread.

Every performance optimization in frontend development is, at some level, a response to this constraint.

---

## What happens between HTML and pixels

When the browser receives HTML, it starts a pipeline. The steps matter because each one has a cost, and some of them cascade.

> 📸 **IMAGEM AQUI**
> Abra: https://web.dev/articles/critical-rendering-path
> Role até o diagrama que mostra: DOM → CSSOM → Render Tree → Layout → Paint
> Salve e faça upload.

**HTML parsing builds the DOM.** The browser doesn't wait for the entire document to arrive — it parses incrementally as bytes come in. This is why a `<script>` tag in the `<head>` without `defer` or `async` blocks everything: the browser can't continue parsing until that script downloads and executes, because the script might modify the DOM being built.

**CSS parsing builds the CSSOM.** Unlike HTML, CSS is not parsed incrementally. The browser needs the complete stylesheet before it can compute styles, because a later rule can override an earlier one. CSS is render-blocking for exactly this reason.

**Layout calculates positions and sizes.** Once the browser knows what elements exist and how they're styled, it calculates where everything goes. Layout is recursive — changing the width of a parent can affect every child. Changing a child's height can push siblings around.

**Paint records drawing instructions.** The browser figures out what to draw and in what order. Some visual properties are expensive to paint: shadows, filters, complex gradients.

**Compositing sends frames to the GPU.** The browser divides the page into layers, rasterizes each one, and sends them to the GPU to combine. This is the step that happens on a separate thread.

---

## Why some CSS properties are faster than others

Some CSS properties can be animated entirely on the compositor thread, without touching the main thread at all. The main ones are `transform` and `opacity`. Animating `transform: translateX(100px)` is smooth even when the main thread is busy with JavaScript. The compositor handles it independently.

Other properties force layout when they change. Anything that affects size or position — `width`, `height`, `margin`, `padding`, `top`, `left` — triggers a full recalculation of the layout tree. If that recalculation happens on every animation frame, you're spending your 16ms budget on layout instead of useful work.

Still others trigger paint but not layout. `background-color`, `color`, `box-shadow`. The browser skips the layout step but still has to repaint the affected elements.

The practical consequence: if you're animating anything, use `transform` and `opacity`. Not `left` and `top`. Not `width` and `height`. The difference between a smooth animation and a janky one is often just which CSS property you chose.

---

## Layout thrashing

Reading a layout property after writing to the DOM forces the browser to recalculate layout synchronously. Doing this repeatedly is called layout thrashing, and it's one of the most common causes of jank.

```javascript
// Layout runs three separate times
const w1 = el1.offsetWidth  // read — forces layout
el1.style.width = w1 + 'px' // write
const w2 = el2.offsetWidth  // read — forces layout again
el2.style.width = w2 + 'px' // write

// Layout runs once
const w1 = el1.offsetWidth  // read
const w2 = el2.offsetWidth  // read
el1.style.width = w1 + 'px' // write
el2.style.width = w2 + 'px' // write
```

Batch your reads before your writes. React's Virtual DOM exists partly to solve this — instead of applying DOM changes immediately as JavaScript runs, React batches changes and applies them together, reducing layout thrashing. The abstraction has a real mechanical benefit.

---

## The event loop

JavaScript runs in a single-threaded event loop. Understanding this loop is what makes async behavior make sense.

> 📸 **IMAGEM AQUI**
> Abra: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop
> Role até o diagrama do event loop (mostra stack, queue, e o loop)
> Salve e faça upload.

The loop works like this: run one task, then drain all microtasks, then render if it's time for a frame, then repeat.

Tasks include event handlers, `setTimeout` callbacks, and network response callbacks. Microtasks include Promise resolutions. After each task, the browser runs all pending microtasks before moving on.

```javascript
setTimeout(() => console.log('task'), 0)
Promise.resolve().then(() => console.log('microtask'))
console.log('sync')

// sync
// microtask
// task
```

Long synchronous tasks — anything that occupies the main thread for more than a few milliseconds — make the page feel unresponsive. The render step only happens between tasks. If your task takes 200ms, the page doesn't update for 200ms.

---

## What frameworks can and can't change

React, Vue, Svelte — they all run inside the same browser. They don't get a faster rendering path. They don't bypass the main thread.

What they change is how efficiently they use the browser's rendering model. React's concurrent scheduler breaks rendering work into small chunks that yield to the browser between iterations. Svelte compiles to direct DOM manipulation that minimizes work per change. Vue's reactive system updates only the components that depend on changed data.

What they can't change: layout still cascades, paint still costs time for complex properties, the compositor still has to do its work, and the main thread is still shared between JavaScript and everything else.

When a React app feels slow, it's almost always because it's doing too much on the main thread. The framework didn't create the constraint. The browser did. Knowing the browser is what lets you find the actual problem.

---

*Next: Your Framework Is Only as Smart as Its Build Pipeline. Before the browser sees your code, it goes through a build process that determines bundle size, code splitting, and tree shaking — and that process explains a lot about why frontend tooling is so complex.*
