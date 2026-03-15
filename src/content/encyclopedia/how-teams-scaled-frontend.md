
One developer working on a frontend can make most architectural decisions instinctively. Three developers need some conventions. Ten developers need explicit conventions documented somewhere. Thirty developers need tooling that enforces conventions, because documentation that isn't enforced is documentation that drifts.

The problems of frontend at scale, inconsistent components, conflicting styles, duplicate code, merge conflicts in shared configuration, slow builds caused by one team's changes, have specific solutions that only make sense in the context of multiple teams working on the same product.

---

## Design systems

A design system is a shared library of UI components, design tokens, and patterns that multiple teams use as a foundation. At the basic level it's a component library. At the more complete level it includes documentation, usage guidelines, accessibility guidelines, and a governance process for adding and changing components.

Design systems solve a real problem: when 10 teams each build their own button component, you end up with 10 button components with slightly different styling, slightly different behavior, and slightly different accessibility properties. One button doesn't have the right focus ring. Another has a hover state that doesn't match the brand. A third doesn't have a loading state.

Centralizing shared components means centralizing the effort of doing them well. One team can spend the time making the button component fully accessible, fully styled to the design system, and well-documented. Every team that uses it benefits from that investment without duplicating it.

The challenges of design systems are organizational as much as technical. Who owns it? How do teams contribute? How are breaking changes handled? How do you keep the documentation in sync with the implementation? Teams that treat the design system as a product, with a roadmap, versioning, and explicit support, maintain it better than teams that treat it as a shared folder of components.

---

## Monorepos

As applications grew and organizations split into multiple teams, the codebase often split into multiple repositories. The frontend, the backend, the design system, shared utilities, separate repositories, separate CI pipelines, separate dependency management.

Multiple repositories create coordination overhead. Updating a shared library requires publishing a new version, updating the consuming repos, coordinating the timing of releases. A change that touches both the component library and the application that uses it requires a PR in each repository and careful sequencing.

Monorepos put multiple packages in one repository. Tools like Nx, Turborepo, and Yarn Workspaces manage monorepos with build caching, task pipelines, and affected-file detection, only rebuild the packages that changed.

The tradeoff: a large monorepo has a larger surface area and potentially slower operations at scale if the tooling isn't configured well. Teams that have optimized their monorepos with remote caching (Turbopack, Nx Cloud, Vercel Remote Cache) report significantly better build times. Teams that haven't can have CI pipelines that take ten or fifteen minutes even when they changed one file.

---

## Micro-frontends

Micro-frontends apply the microservice decomposition pattern to frontend code: instead of one large frontend application, multiple smaller frontend applications are composed into one user-facing product. Each team owns their piece, deploys independently, and is technically decoupled.

The composition can happen in different ways: server-side composition (include tags assemble the page on the server), iframe composition (each micro-frontend is an iframe), client-side composition (a shell application loads other applications at runtime using module federation).

Module Federation, a Webpack 5 feature, is the most common technical approach for client-side micro-frontends. Each application exposes components that other applications can load at runtime. A checkout experience built by the payment team can be loaded by the product team's application without a shared build.

The cases where micro-frontends make sense are specific: very large organizations where teams have fundamentally different release cadences, where technology heterogeneity between teams is real and acceptable, and where team autonomy is more valuable than the coordination overhead of a shared codebase.

The costs are real. Multiple JavaScript runtimes in one page. Potential style isolation issues. Difficulty sharing state across micro-frontend boundaries. Increased complexity in testing the composed application. For teams that don't genuinely need team-level deployment independence, the complexity is not worth it.

---

## Versioning and the component library problem

When multiple teams consume a shared component library, versioning matters. A breaking change in a major version of the library requires every consuming team to update, and those updates might not happen simultaneously. In a period where teams are on different versions, behavior inconsistency is expected.

Semantic versioning with clear release notes, migration guides, and deprecation warnings before removals reduces the pain. Codemods, automated code transforms that update usage of deprecated APIs, make major version upgrades less tedious.

The more fundamental question is whether to version at all. In a monorepo, the library and all its consumers are in the same repository, so breaking changes are immediately visible across all usages. The boundary between library and consumer is softer, and the conversation about changes is easier.

---

## Feature flags

As teams scale, coordinating feature releases across multiple teams becomes complicated. Feature flags let you deploy code that's not yet active for users, and activate features independently from deployment.

A feature flag system lets a team deploy code behind a flag, do a phased rollout (10% of users, then 50%, then 100%), quickly roll back a feature by turning the flag off, and test different variations for different user segments.

Tools like LaunchDarkly, Unleash, and Flagsmith provide feature flag infrastructure. The frontend reads the flag values and conditionally renders the new feature:

```typescript
const { flags } = useFeatureFlags();
return flags.newCheckoutFlow ? <NewCheckout /> : <OldCheckout />;
```

Feature flags are infrastructure, not a free pass for bad deployments. A flag system with hundreds of stale flags that nobody remembers the purpose of is technical debt that creates confusion and risk.

---

*Next: How the Mobile Web Changed Everything. The iPhone's launch in 2007 changed what "the web" meant. Responsive design, touch events, performance on low-end devices, mobile web constraints changed what good frontend work looked like.*
