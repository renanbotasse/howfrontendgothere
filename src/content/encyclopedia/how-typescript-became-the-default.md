
TypeScript launched in 2012. For about three years, the reaction was mostly skepticism. JavaScript is dynamic, why add a type system? The compile step adds friction. The type annotations are noise. Angular 2 announcing TypeScript as its default language in 2015 was controversial. Some developers took it as a sign that Angular had lost the plot.

By 2020, TypeScript was the de facto standard for new JavaScript projects. The State of JS survey for 2022 showed TypeScript at over 80% adoption among respondents. What changed wasn't TypeScript. What changed was the codebase size and the cost of the problems TypeScript solves.

---

## What TypeScript actually is

TypeScript is a superset of JavaScript. Every valid JavaScript file is valid TypeScript. TypeScript adds optional type annotations, a type checker, and a compiler that strips those annotations to produce plain JavaScript.

The type checker is the valuable part. It reads your code and verifies that you're using values in ways consistent with their types. If a function expects a string and you pass a number, TypeScript tells you at compile time, not when a user triggers that code path in production.

```typescript
function greet(user: { name: string; age: number }) {
  return `Hello, ${user.name}`;
}

greet({ name: 'Renan', age: 30 }); // fine
greet({ name: 'Renan' });          // error: missing 'age'
greet('Renan');                     // error: expected object, got string
```

This looks like a contrived example. In a codebase where functions are called across many files, where APIs return data that gets passed between layers, and where multiple developers are touching the same code, the errors TypeScript catches before they run are real.

---

## Why the friction was worth it

The JavaScript community in 2012-2015 was suspicious of type systems because the experience most developers had with types came from Java and C++. Verbose declarations. Rigid hierarchies. Boilerplate interfaces that seemed to exist just to satisfy the compiler.

TypeScript's type inference reduced the verbosity significantly. You don't need to annotate everything:

```typescript
const user = { name: 'Renan', age: 30 }; // TypeScript infers the type
const name = user.name;                    // TypeScript knows this is a string
```

TypeScript infers types from usage, so you get type checking without annotating every variable. The annotation burden is mostly at API boundaries, function parameters and return types, which are the places where being explicit about types is actually valuable.

The editor experience was the other selling point. VS Code and TypeScript are both Microsoft products and their integration was tight from early on. Autocomplete that works across files and libraries. Inline errors before you run the code. Refactoring that updates all usages of a renamed symbol. These features existed in other languages and IDEs but weren't available in JavaScript development. TypeScript brought them.

---

## The inflection point: Angular 2

Google announced that Angular 2 (2016) would use TypeScript as the primary language. At the time, Angular 1 was one of the dominant frontend frameworks. The announcement was polarizing, TypeScript as the official language of Angular was, for some, a reasonable choice for enterprise teams, and for others, a sign of over-engineering.

What it actually did was establish TypeScript's legitimacy for serious frontend development. If a framework with Angular's adoption was built on TypeScript, TypeScript was clearly not a niche tool for developers who missed Java.

React's relationship with TypeScript took longer to formalize. React was written in JavaScript and Facebook used Flow (their own type checker) internally. But the community's TypeScript adoption in React projects grew steadily from 2017 onward, driven by DefinitelyTyped, a community-maintained repository of TypeScript type definitions for JavaScript packages.

---

## DefinitelyTyped and the ecosystem problem

TypeScript's adoption was blocked for a while by the ecosystem. If half the packages you use don't have type definitions, TypeScript's value is limited, the type checker gives up on anything that comes from an untyped package.

DefinitelyTyped solved this with community-maintained type definitions. When you install a package without built-in types, you often install a `@types/package-name` package that provides them. `@types/react`, `@types/lodash`, `@types/node`, thousands of packages have type definitions maintained by the community.

The model has limitations, community-maintained definitions can be wrong or out of date. But it unlocked TypeScript adoption across the JavaScript ecosystem before library authors added first-party type support, which most large libraries now provide.

---

## TypeScript's influence on JavaScript

TC39, the committee that standardizes JavaScript, has adopted features that TypeScript pioneered or popularized. Optional chaining (`?.`), nullish coalescing (`??`), and class field syntax were all in TypeScript before they were in JavaScript. TypeScript served as a proving ground.

The relationship is interesting: TypeScript adds features to JavaScript to test their utility, and successful features often get standardized into JavaScript proper. When they do, TypeScript supports them natively, TypeScript's goal is to remain a typed superset, not to diverge.

TypeScript also changed how developers think about data shapes. Defining a type for the shape of an API response, a component's props, or a function's return value is now standard practice in TypeScript projects. That discipline of naming and documenting data shapes carries over to how teams think about their systems, beyond just catching runtime errors.

---

## The cost

TypeScript is not free. The compile step adds time to the build, though SWC and esbuild have reduced the transform cost significantly. Large TypeScript codebases with complex generic types can have slow type checking that developers wait for. Some types are genuinely hard to express, especially when working with highly dynamic JavaScript patterns.

The `any` type is TypeScript's escape hatch, it disables type checking for a value. A codebase with many `any` annotations doesn't get much value from TypeScript. Maintaining good type coverage requires discipline and code review attention.

And TypeScript's type system is complex enough that some developers spend time fighting the type checker rather than writing application code. Conditional types, infer, mapped types, template literal types, these are powerful but have a learning curve, and it's possible to over-engineer types in ways that make code harder to understand.

None of those costs has slowed adoption, because the alternative, large JavaScript codebases without type safety, has costs that accumulate more slowly but hit harder. TypeScript became the default because at enough scale, the problems it solves are more expensive than the friction it adds.

---

*Next: How Code Quality Became Automated. Linting, formatting, and code review conventions used to be enforced by humans. Automating them changed what developers spend their review attention on and what teams disagree about.*
