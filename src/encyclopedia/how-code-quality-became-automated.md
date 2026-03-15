
Code review used to catch everything: logic bugs, typos, inconsistent formatting, unused variables, missing semicolons, functions that were too long, variable names that were too short. Reviewers did the work of both a computer and a human, and the distinction between those two categories of feedback wasn't obvious.

Linters and formatters automate the mechanical part. They can't tell you if the logic is wrong, but they can tell you that `let` should be `const`, that this variable was declared but never used, that this function call has the wrong number of arguments, and that this file uses spaces where the rest of the project uses tabs.

Automating the mechanical part has had more consequences than it initially seems.

---

## JSHint and the first generation

JSLint, created by Douglas Crockford in 2002, was the first widely-used JavaScript linter. It was opinionated — Crockford had strong views about JavaScript best practices and the tool enforced them. If your code didn't follow Crockford's style, JSLint said so.

JSHint forked from JSLint in 2010 to be more configurable. Not every team agreed with every Crockford opinion, and a tool that could be configured to match your team's conventions was more widely adoptable.

Both tools analyzed JavaScript for common errors and style issues. They caught things like using `==` instead of `===` (which behaves unexpectedly with type coercion), declaring variables that were never used, and calling functions before they were declared.

---

## ESLint — the plugin ecosystem

ESLint (2013) replaced JSHint as the dominant linter by being extensible. Instead of a fixed set of rules, ESLint provided an AST-based plugin API. Rules were plugins. Configurations were shared packages. A team could install `eslint-config-airbnb` and get Airbnb's JavaScript style guide enforced automatically.

The plugin ecosystem meant that framework-specific rules could exist. `eslint-plugin-react` adds rules like "all hooks must be called at the top level" (not inside conditionals), which ESLint itself has no concept of because it doesn't know about React. `eslint-plugin-a11y` adds accessibility rules. `eslint-plugin-security` adds rules for common security mistakes.

The practical effect was that code review comments like "you called a hook inside a conditional" or "this image doesn't have an alt attribute" stopped being written by humans. ESLint wrote them instead, before the code even reached review. The reviewer's attention was freed for things linters can't catch.

---

## Prettier and the formatting wars end

Before Prettier (2017), code formatting was a thing teams argued about. Tabs vs. spaces. Single quotes vs. double quotes. Where line breaks go in long function calls. When to use trailing commas. These arguments consumed real time and created real friction.

Prettier is an opinionated formatter with almost no configuration. It takes your code, parses it to an AST, and reprints it according to its rules. The output is always the same for the same input. You don't argue about Prettier's style — you either use it or you don't.

The key insight is that the value of a formatter is consistency, not aesthetics. Whether Prettier's choices are optimal matters much less than whether they're consistent across the codebase. Code that's consistently formatted is easier to read, easier to review, and produces smaller diffs when changes are made (because no developer accidentally reformats a block when editing it).

Prettier integrates with most editors — format on save is a common configuration. It also integrates with CI, so a pull request with unformatted code fails the check.

The formatting wars largely ended. There are still opinions about Prettier's specific choices, but the value of consistency outweighs most stylistic disagreements.

---

## Husky and the commit gate

Pre-commit hooks let you run checks when a developer runs `git commit`. If the checks fail, the commit is rejected. Husky makes pre-commit hooks easy to configure in a JavaScript project.

A common setup: run ESLint and Prettier on changed files before commit. If any file has lint errors or is unformatted, the commit fails. Developers see the issues before they ever push code.

lint-staged pairs with Husky to run checks only on staged files, not the entire codebase. Checking only what you're about to commit is faster and avoids failing on pre-existing issues in untouched files.

```json
{
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{css,md}": "prettier --write"
  }
}
```

---

## CI as the enforcement layer

Pre-commit hooks can be bypassed with `git commit --no-verify`. For teams that need reliable enforcement, CI is the real gate.

Running ESLint, TypeScript type checking, and tests in CI means that code that passes all checks can merge, and code that fails is blocked regardless of whether the developer ran the checks locally. This removes the "works on my machine" failure mode where local tooling was configured differently.

GitHub Actions, GitLab CI, and similar platforms make this straightforward. A workflow that runs on pull requests, checks linting and types and tests, and reports the results is standard configuration for most professional teams.

---

## What automation changed about code review

The shift is measurable in what review comments look like. Pre-automation, a review on a JavaScript file might include: three formatting comments, two unused variable comments, one missing semicolon, one logic question, and two architectural observations.

Post-automation, the formatter already handled formatting. ESLint already caught the unused variables. TypeScript already caught the type errors that would have been runtime bugs. The review now contains the logic question and the architectural observations.

That's not a small change. Architectural questions and logic correctness are harder to evaluate and more valuable to get right than formatting consistency. Shifting review attention to those categories makes review more effective.

The limitation is that automated tools can only check what they can mechanically verify. The question "is this the right abstraction?" can't be linted. "Would this be simpler if redesigned?" can't be type-checked. Code review remains valuable for the things automation can't do. Automation just removes the noise that was competing for attention with those things.

---

*Next: How SEO Broke the SPA. Single-page applications rendered in the browser became a problem for search engines that expected server-generated HTML. The resulting tension changed how the industry thinks about rendering.*
