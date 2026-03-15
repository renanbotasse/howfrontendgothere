
React shipped in 2013. By 2014 the community was skeptical, JSX looked like a step backward, the Virtual DOM seemed like overhead, and mixing HTML and JavaScript felt wrong to people who'd spent years separating concerns. By 2016, React had won. By 2020, it was so dominant that "frontend developer" and "React developer" were nearly synonymous.

That's a fast turnaround for a tool with a hostile first impression. Understanding why it happened requires understanding what React actually changed, not just what it added.

---

## The problem with direct DOM manipulation

Before React, the standard approach was to query DOM elements and modify them in response to events. jQuery made this fluent:

```javascript
$('#submit-button').on('click', function() {
  var name = $('#name-input').val();
  $('#greeting').text('Hello, ' + name);
  $('#error-message').hide();
  $('#greeting').show();
});
```

This works for small UIs. As the UI grows, you accumulate a long list of mutations. The problem is that there's no description of what the UI should look like in a given state, there's only a sequence of commands that got it there. When something looks wrong, you have to trace through all the mutations that might have run to understand why the DOM is in the state it's in.

Angular 1 introduced two-way data binding as an alternative: link a DOM element to a data property, and changes to either automatically propagated to the other. This seemed simpler but created a different problem, the data flow was bidirectional and non-obvious. When an input changed a value, what else changed? It could be hard to tell without tracing the binding graph.

---

## The declarative model

React's core idea is that your UI is a function of your state. Given a certain state, the UI should look a certain way. Always. No exceptions, no special cases for how you got to that state.

You describe what the UI should look like, not how to get there. React figures out how to get there.

```javascript
function Greeting({ name, isLoading }) {
  if (isLoading) return <Spinner />;
  return <h1>Hello, {name}</h1>;
}
```

This component is a function that maps state to UI. Call it with `{ name: 'Renan', isLoading: false }` and you always get the same output. No DOM state to worry about. No "what mutations ran before this?" The UI is derivable from the state.

When state changes, React re-runs the component function and produces a new description of what the UI should look like. It compares this to what's currently rendered, figures out the minimum set of DOM changes needed to make them match, and applies them.

---

## JSX was the right call, even though it looked wrong

JSX let you write HTML-like syntax in JavaScript. People's first reaction was usually negative, wasn't the whole point of web development to separate HTML, CSS, and JavaScript?

The separation of concerns argument assumed that HTML and JavaScript were naturally separate. But an interactive UI component doesn't have a natural separation between its structure and its behavior. A button that shows a spinner when clicked isn't separating cleanly into HTML and JavaScript, they're deeply coupled. Putting them in the same file, in JSX, is more honest about that coupling.

JSX is also just function calls. `<Button onClick={handleClick}>Submit</Button>` is `React.createElement(Button, { onClick: handleClick }, 'Submit')`. You're not getting a new language, you're getting syntax that compiles to function calls. And function calls are composable, testable, and analyzable by tools in ways that template strings are not.

---

## Components as the unit of composition

React popularized the component as the primary unit of UI composition. Not widgets, not controllers, not views, components. A component is a function (or class, but hooks displaced classes) that takes inputs and returns a UI description.

Components compose. A `<Form>` contains `<Input>` and `<Button>` components. A `<UserProfile>` contains `<Avatar>` and `<UserBio>` components. The composition is explicit in the code and visible in the tree structure.

This turned out to be a better design abstraction than what came before. The component model is why React's ecosystem is so large, it's easy to package a component and publish it to npm. `react-table`, `react-datepicker`, `react-virtual`, all of these are components that work by following React's composition model.

The model also translated. Vue adopted it. Angular moved toward it. Svelte, Solid, Qwik, everything after React uses some version of component-based composition. Even web components, the browser-native standard, follow the same pattern.

---

## Hooks changed how logic was shared

React's original class component API had a problem with code reuse. Logic that needed to share state had to be extracted into higher-order components or render props, both of which added layers to the component tree and made the code harder to follow.

Hooks, added in React 16.8 in 2019, let you extract stateful logic into functions that could be called from any component:

```javascript
function useWindowSize() {
  const [size, setSize] = useState({ width: window.innerWidth, height: window.innerHeight });

  useEffect(() => {
    const handler = () => setSize({ width: window.innerWidth, height: window.innerHeight });
    window.addEventListener('resize', handler);
    return () => window.removeEventListener('resize', handler);
  }, []);

  return size;
}
```

Any component that needs the window size calls `useWindowSize()`. The logic lives in one place. No higher-order components, no render props, no class inheritance.

Hooks changed the way the community wrote React more than any other change since the initial release. The entire ecosystem of React libraries updated to expose hook-based APIs. Custom hooks became the primary pattern for sharing logic.

---

## Why React is still dominant

There are frameworks with better performance characteristics than React. Svelte compiles to vanilla JavaScript with no runtime. Solid uses fine-grained reactivity to update only what changed. Both are measurably faster for certain benchmarks.

React's dominance isn't about benchmark performance. It's about ecosystem, hiring, and the investment made by tools and libraries. If you're building something with React, there's a library for almost anything you might need, documentation for almost any problem, and a large pool of developers who already know the mental model.

The mental model itself, UI as a function of state, components as composable units, hooks for logic reuse, proved durable. Even developers who don't use React use frameworks that adopted these ideas. React didn't just win the framework war. It changed what a UI framework is expected to do.

---

*Next: How CSS Grew Up as a Language. CSS is older than React and spent most of its life being dismissed as a toy. The journey from floats and hacks to Custom Properties, Grid, and Container Queries is a story of a language slowly getting the power it needed.*
