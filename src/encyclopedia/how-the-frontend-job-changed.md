
In 2005, a frontend developer was someone who wrote HTML and CSS and maybe a little JavaScript. The job was visual: make the designs from the designer look correct in the browser. The most common interview question was "can you make a three-column layout in CSS?" — not easy at the time, given that grid and flexbox didn't exist and floats were the mechanism.

In 2024, a frontend developer might be managing deployment infrastructure, writing server-side code in Next.js, optimizing database queries through server components, implementing authentication flows, configuring CI pipelines, and working with WebAssembly for performance-critical features. The three-column CSS layout is a one-liner.

The job changed more than almost any technical role in the same period.

---

## The specialization that never fully happened

For a while in the early 2010s, the frontend role split into "UX engineer" (close to design, mostly CSS and HTML) and "JavaScript engineer" (close to software engineering, mostly application architecture). Some organizations hired both. Some job postings made the distinction.

The split didn't stick. The SPA era made the JavaScript engineering part dominant, but design still had to work somewhere, and someone had to own the component that was both a technical unit and a visual one. The role expanded to contain both rather than splitting into two sustainable sub-disciplines.

What emerged is a spectrum. Some frontend developers work closest to design — they care deeply about CSS, typography, animation, and visual detail. Others work closest to architecture — they care about state management, performance, build pipelines, and API design. Many are somewhere in the middle. The job title covers all of them, which creates confusion about what any given company means when they post a frontend position.

---

## The full-stack pressure

The popularity of TypeScript, Node.js, and full-stack frameworks like Next.js blurred the line between frontend and backend in a direction that put pressure on frontend developers to know more.

Next.js API routes put server-side code in the same project as client-side code. Server Components run on the server but are written in the same React component model as client components. Prisma or Drizzle can be called from a server component directly. Database queries are frontend code, in a structural sense if not in a conceptual one.

Some teams embraced this fully and called everyone who touched the Next.js codebase "full-stack." Others maintained a clearer division: frontend developers wrote components and client-side logic, backend developers wrote API services and owned the database. The frameworks supported both models.

The skill expectations drifted upward. Knowing how to configure a CDN, understanding cold start behavior in serverless functions, knowing when server rendering is more appropriate than client rendering — these were infrastructure concerns that became frontend concerns.

---

## Accessibility and performance as core skills

For most of the job's history, accessibility was optional extra credit and performance optimization was a senior skill. Both have moved toward being baseline expectations.

Core Web Vitals affecting search rankings gave performance a business justification that hadn't existed as concretely before. Teams that had deprioritized performance found themselves with a budget to address it. The skills required — understanding the rendering pipeline, identifying main thread bottlenecks, optimizing bundle sizes, implementing lazy loading — became things hiring managers asked about.

Accessibility requirements driven by legal risk and platform policies moved similarly. "Does your team write accessible code?" went from a question that prompted vague affirmatives to one that required specific technical answers about how focus management works, how ARIA is applied, and how designs are reviewed for accessibility before implementation.

Neither shift is complete. Plenty of teams still ship inaccessible code and poorly performing applications. But the expectations changed.

---

## The tools explosion

The number of tools a frontend developer is expected to know increased significantly. In 2010, "knows HTML, CSS, and JavaScript" covered most of the job. Knowing jQuery was a bonus.

By 2024, a frontend job posting might list: React, TypeScript, Next.js, Tailwind, React Query, Zustand, Playwright or Cypress, Jest and Testing Library, ESLint and Prettier, Git and GitHub Actions, Figma (reading designs), and basic AWS or Vercel. That's a minimal list.

Each tool has its own learning curve, its own configuration, and its own ecosystem of patterns. The time available to go deep on any one thing shrinks as the surface area of expected knowledge grows. Developers who specialize deeply in one area — someone who knows the React rendering model extremely well, or someone who's obsessive about CSS layout — are valuable precisely because that depth becomes rare.

---

## What the job is now

The frontend job in 2024 is building user interfaces for software that runs in browsers, managing the infrastructure that delivers those interfaces, ensuring they perform well and are accessible, and doing it collaboratively with designers, backend developers, and product managers through tools and processes that have grown significantly more sophisticated than they were twenty years ago.

That's a broader job than "write HTML and CSS." It's also a more interesting one, with more to learn and more ways to specialize. The expansion of scope comes with more complexity, more to keep up with, and higher expectations.

Whether that's a good thing depends on what you're optimizing for. The job became harder to do well and better-compensated as a result. The barrier to entry stayed lower than most engineering roles because the tooling remained accessible. The ceiling for depth of expertise rose higher as the problems got more sophisticated.

---

*Next: Why the Frontend Ecosystem Is So Complex. The last article in the encyclopedia asks the big question: why is this all so complicated? The answer is not "bad decisions" or "JavaScript developers being chaos agents." It's a predictable outcome of real pressures.*
