
For most of the web's history, design and engineering were separated by a handoff. A designer produced mockups in Photoshop. A developer received those mockups and interpreted them into code. The gap between what was designed and what was built was normal, part of the process, accepted and managed through rounds of "can you fix the spacing on this?" feedback.

The gap narrowed as tools, practices, and the expectations on both sides changed. The distance between a Figma file and production code is smaller in 2024 than it has ever been. That doesn't mean there's no gap, there's still a lot of work between design and deployment, but the nature of that work has changed.

---

## The Photoshop era

Photoshop was designed for photo editing. Web designers used it for UI mockups for years because it was what existed and eventually because it was what everyone knew. Static mockups of every screen. Sliced into image assets. Handed to developers with a spec sheet or a pixel-measuring conversation.

This produced specific problems. Photoshop didn't understand responsive layouts, you designed for one viewport size. It didn't understand component reuse, you copied and pasted elements. It couldn't represent interaction states, you made separate mockups for hover, active, and disabled states, or you skipped them and hoped the developer figured it out.

Sketch (2012) was the first design tool built specifically for UI design rather than repurposed from another use case. It introduced symbols, reusable components with overrides, that loosely corresponded to how developers thought about reusable components. It ran on Mac only, which limited adoption, but established what a dedicated UI design tool should look like.

---

## Figma and the collaborative model

Figma (2016) ran in the browser, which solved the platform exclusivity problem. More importantly, it was multiplayer, multiple people could work in the same file simultaneously. Design review moved into the file: you could comment on specific elements, make suggestions, and iterate without emailing files back and forth.

For engineering, Figma added developer handoff features: inspect mode shows exact CSS values for any element. Variables (design tokens) are defined once and referenced everywhere. Components show which properties are variable and which are fixed.

The shared file replaced the handoff in many workflows. Developers opened the Figma file, inspected the design, measured what they needed, and built it. The conversation became less "here is a mockup to implement" and more "let's look at this together."

---

## Design tokens

Design tokens are the bridge between design decisions and code values. A design token is a named value, `--color-brand-primary: #3b82f6`, that exists in both the design system and the codebase.

When a designer changes the brand color in Figma, if that change flows to the token and the token is exported to the codebase, the code change is automatic. The token is the shared language.

Style Dictionary is a common tool for this: a design team defines tokens in a format, Style Dictionary converts them to CSS custom properties, JavaScript constants, or whatever the codebase consumes.

![Design tokens: from Figma single source to CSS variables, JS, and native platforms](/images/design-tokens.svg)

The gap in practice: token synchronization is still mostly manual or requires custom pipelines. Tools like Token Studio for Figma can sync Figma variables to a GitHub repository, but this requires setup and maintenance. For teams that haven't built the pipeline, design tokens exist in two places that drift over time.

---

## Component-based design

The most significant change in the design/engineering relationship is the shared vocabulary of components. Both Figma and code now have components with properties that can be varied. A designer creates a `Button` component with `variant` (primary, secondary, danger) and `size` (small, medium, large) properties. A developer creates the same component with the same props.

When design and engineering share the component model, conversations become more precise. "Use the large primary button here" is an unambiguous instruction that maps directly to code. "Make this button look like the other buttons" is vaguer and more open to interpretation.

This alignment is imperfect. Figma components and React components are different things, with different constraints and different edge cases. A designer can easily create layouts in Figma that are difficult or impossible to implement as specified because the design doesn't account for variable content length, overflow, or dynamic states. An engineer implementing a design doesn't always know to ask about states the design didn't show.

---

## The engineer who can design, the designer who can code

The convergence of tools and practices has changed the expectations on both roles. Frontend engineers who can read and edit Figma files, who understand color systems and spacing scales, and who can push back on designs that are difficult to implement with semantic HTML, these are more effective collaborators.

Designers who can open a codebase, understand what a component prop does, and know why a flex container behaves differently than they expected in their mockup, these are more effective collaborators too.

This convergence has had an interesting downstream effect on tools. V0 (from Vercel) and similar AI tools can generate React components from Figma designs or natural language descriptions. The gap between the design and the implementation is being partially automated, which will change the nature of both roles further.

The fundamental tension, the designer thinks in visual space, the engineer thinks in code structure, isn't resolved by better tools. But the distance between the two has gotten shorter, and the collaboration has become more immediate.

---

*Next: How Deployment Changed Everything. When deploying a web application required FTPing files to a server, the pace of releases was measured in weeks or months. What changed when deployment became push-to-deploy?*
