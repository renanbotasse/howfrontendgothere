
CSS has a global namespace problem. A class named `.button` in one file applies to every element with that class in the entire document, regardless of where that CSS was written or which component it was intended for. In a small project, this is manageable. In a large codebase with many developers working simultaneously, it's a genuine source of bugs and a maintenance headache.

Once the frontend community adopted components as the primary UI abstraction, a question followed immediately: how do you style a component in a way that doesn't leak to other components, and doesn't get overridden by someone else's global styles?

The community has been arguing about the answer ever since.

---

## BEM and structured naming conventions

Before any tooling solution, developers tried solving the problem with naming discipline. BEM — Block Element Modifier — is a naming convention that makes class names unique by encoding the component context:

```css
.card { }
.card__header { }
.card__body { }
.card--featured { }
.card__header--large { }
```

Block, element (with double underscore), modifier (with double dash). A `.card__header` style doesn't conflict with a `.profile__header` style because the block context is part of the name.

BEM works. It's still used. The problem is that it's entirely disciplinary — nothing enforces it. One developer who doesn't know the convention or has a different opinion about it introduces conflicts. And the class names get long and ugly, which isn't a technical problem but is a real friction.

---

## CSS Modules

CSS Modules, popularized around 2015, solve the scoping problem with tooling. You write CSS in a regular `.module.css` file and import it into your component:

```javascript
import styles from './Card.module.css';

function Card() {
  return <div className={styles.header}>...</div>;
}
```

The build tool transforms the class name — `.header` becomes something like `.Card_header__k3j9s` — a unique identifier that's specific to this file. No naming convention required. No conflicts possible. The scoping is enforced mechanically.

The CSS itself is still regular CSS. You can use any CSS feature. The tradeoff is the import syntax and the loss of being able to reference classes directly in browser devtools without understanding the transformation.

CSS Modules are built into Next.js, supported by Vite, and widely used. They're probably the most boring solution — not exciting, just reliable.

---

## CSS-in-JS

Styled-components, released in 2016, took a different approach: write CSS inside your JavaScript files, attached to your components. The library generated a unique class name at runtime and injected the styles into the document:

```javascript
const Button = styled.button`
  background: ${props => props.primary ? '#3b82f6' : 'white'};
  color: ${props => props.primary ? 'white' : '#3b82f6'};
  padding: 0.5rem 1rem;
`;
```

The appeal was significant. Styles were co-located with the component code. Props flowed into styles — dynamic styling without toggling class names. The full power of JavaScript was available for styling logic. No class naming convention. No separate files.

Emotion provided a similar API. The CSS-in-JS approach became dominant in the React ecosystem around 2017-2019.

Then the performance costs became visible. Runtime CSS-in-JS generates and injects styles during rendering. With server-side rendering, styles had to be serialized and included in the HTML or there'd be a flash of unstyled content. The runtime processing added overhead to every render. With React concurrent features, some CSS-in-JS libraries had subtle correctness issues because they relied on render-order assumptions that concurrent rendering didn't guarantee.

Zero-runtime CSS-in-JS emerged as a response: libraries like Linaria and vanilla-extract extract the CSS at build time and generate static class names, giving you the co-location developer experience without the runtime cost.

---

## Utility classes and Tailwind

Tailwind CSS, released in 2019, is a different philosophy entirely. Instead of writing custom CSS, you compose utilities — single-purpose classes that apply one or a few CSS properties:

```html
<div class="flex items-center gap-4 p-4 bg-white rounded-lg shadow">
  <img class="w-12 h-12 rounded-full" src="avatar.jpg" />
  <span class="text-lg font-medium text-gray-900">Renan Botasse</span>
</div>
```

The class names are verbose. The HTML is dense. People who see Tailwind for the first time usually hate it. People who use it for a week usually don't want to go back.

The reason is that utility classes solve the scope problem from a completely different angle: there's nothing to scope. A `flex` class applies flex display. That's all it does. There are no component-specific styles that could conflict with each other, because you're not writing component-specific styles at all.

The "consistency" problem that design systems solve — making sure buttons look the same everywhere — gets addressed by using the same utility combinations everywhere, or by extracting component classes when a pattern repeats.

Tailwind's production build uses PurgeCSS (now built-in) to remove any utility class that doesn't appear in your HTML or JSX, keeping bundle sizes small even though the full Tailwind stylesheet is enormous.

The criticism is that the class names are opaque and the HTML gets hard to read. The counter is that the component is the unit of abstraction, and the component file is short enough to read in full, so the verbosity is manageable.

---

## Web Components and the Shadow DOM

The browser platform itself has a scoping mechanism: the Shadow DOM. Web Components — a set of browser-native APIs — can attach a shadow tree to an element that has its own scoped CSS. Styles inside the shadow root don't affect the rest of the page, and styles from outside don't affect what's inside.

Shadow DOM works, but it comes with constraints. You can't easily style shadow DOM internals from outside using regular CSS — you have to use CSS custom properties or specific shadow-piercing selectors. It's a hard boundary that's useful for widget components (like `<video>` or `<input>`) but creates friction for the fluid composition that most UI components need.

Web Components have existed in some form since 2011 and have never quite achieved mainstream adoption for application UI, despite being built into browsers. The composition model and the shadow DOM's strictness don't match how most frontend teams think about building UIs.

---

## The actual answer

None of these approaches won. CSS Modules are popular in projects that want simplicity. Tailwind is popular in new React projects and increasingly beyond them. CSS-in-JS still has significant adoption, especially zero-runtime variants. BEM is still used in non-component codebases.

The question of how to style components touches on fundamental disagreements about separation of concerns, readability, tooling complexity, and performance tradeoffs. Different projects have legitimately different right answers, which is why the ecosystem maintains multiple competing solutions that each have genuine communities.

What's consistent across all of them is the diagnosis: global CSS at scale creates problems. What's disputed is which solution's tradeoffs are worth accepting.

---

*Next: How the Industry Tried to Solve State. Styling was complicated by components. State management was complicated by components. The industry's attempt to solve client state — from jQuery's ad hoc approach to Redux to React Query — is a story of increasingly accurate models of what the actual problem was.*
