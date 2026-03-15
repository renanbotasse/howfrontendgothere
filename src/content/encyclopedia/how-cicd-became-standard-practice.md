
Continuous Integration and Continuous Deployment are two ideas that became the default over about 15 years. Not through a sudden shift, they spread gradually as the tooling got easier, the benefits became more obvious, and the cost of not doing them became harder to defend.

The result is that most professional frontend projects in 2024 have some form of CI/CD. Running tests on every PR, deploying to preview environments automatically, deploying to production on main, these are baseline expectations, not advanced practices.

---

## What CI actually means

Continuous Integration started as a response to a specific problem: developers working on separate branches for long periods, then trying to merge all the work together. "Integration hell" was the name for what happened when two branches diverged significantly and merging them was painful.

The solution was to integrate frequently, multiple times per day, so divergences stayed small. But integration without verification is just merging. The "continuous" part requires that every integration is automatically verified to not have broken anything.

CI, in practice, means: every push to a branch triggers a pipeline that builds the code and runs the tests. If the build fails or tests fail, the PR cannot merge (if you've configured it to block). The developer knows immediately that their change has a problem.

Before CI, teams discovered integration problems when the release candidate was being tested, after the work was done, after the branches were merged, often under time pressure. Finding a bug during the release cycle is significantly more expensive to fix than finding it when the code was written.

---

## The test pyramid as a CI cost problem

CI pipelines run tests automatically, which means you care about how long the tests take. A pipeline that takes 30 minutes means developers wait 30 minutes to know if their change is safe to merge. If the pipeline takes 10 minutes, developer flow is much less interrupted.

The test pyramid, many fast unit tests, fewer slower integration tests, few very slow end-to-end tests, is partly an economic argument about CI. Unit tests run in milliseconds. A suite of a thousand unit tests might finish in under a minute. End-to-end tests that launch a browser, navigate to pages, and interact with the UI take seconds each. A comprehensive E2E suite might take 15 minutes to an hour.

Teams optimize for CI speed: run the fastest checks first (linting, type checking, unit tests), run the slowest checks only when the fast ones pass, run different test suites in parallel. The goal is a pipeline that gives developers useful feedback quickly.

---

## GitHub Actions and the democratization of CI

Before GitHub Actions (2019), CI required external tools: CircleCI, Travis CI, Jenkins, TeamCity. These were good tools, but they required setup, configuration, and sometimes cost. Small projects often skipped CI because the setup overhead wasn't worth it for the scale.

GitHub Actions brought CI into the repository hosting service. Workflow files in `.github/workflows/` define pipelines that trigger on events, push, PR, merge. The configuration is YAML files in your repository. No external service to configure.

```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 20
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check
      - run: npm run test
```

The bar to having CI on a project dropped significantly. A 20-line YAML file is enough to get linting, type checking, and tests running on every PR. Reusable actions from the GitHub Marketplace handle common tasks, caching dependencies, deploying to cloud providers, running Lighthouse audits.

---

## The preview deployment as CI output

Vercel and Netlify treat deployment as a CI step. The pipeline isn't just "run tests, pass/fail", it's "run tests, build, deploy to a preview URL." Every PR has a unique preview deployment that represents exactly what that PR would look like in production.

This changed how review and QA work. Instead of checking out a branch locally to test it, you open the preview URL. A designer can check the visual implementation without development environment setup. A product manager can verify behavior on their phone. A QA engineer can run exploratory tests without coordinating with a developer.

The preview URL also enables automatic visual regression testing, tools like Percy or Chromatic can compare screenshots between the main branch and the PR, flagging visual changes for review. Intentional changes can be approved; unintended ones are caught.

---

## Continuous Deployment vs. Continuous Delivery

The distinction matters in practice. Continuous Delivery means every change is automatically ready to deploy, it's passed all checks and is theoretically safe, but a human still decides when to push the button. Continuous Deployment means every change that passes checks is automatically deployed to production with no human gate.

Most teams practice Continuous Delivery in production even if they practice Continuous Deployment to staging. A human decision to release a feature remains valuable when features involve user-facing changes, when coordinating with marketing or support, or when the changes have complex rollout considerations.

Feature flags make the distinction less binary: you can deploy code continuously (no human gate on the deploy) but control visibility of features (human decides when to turn on the flag). The deploy frequency is high; the feature release frequency is still managed.

---

## What CI/CD doesn't fix

CI/CD is infrastructure, not a quality guarantee. A pipeline that runs tests that don't cover your application's behavior will pass while the application breaks. A pipeline with no E2E tests will pass while the user-facing flows are broken.

The investment in CI/CD is proportional to the investment in testing. A project with no meaningful tests has a CI pipeline that runs quickly and catches nothing. The pipeline reveals the quality of the test suite, not a substitute for it.

Teams that have CI/CD but inconsistent test coverage often have a false sense of confidence: the pipeline is green, so everything must be okay. The discipline of maintaining meaningful test coverage alongside the CI infrastructure is what actually produces the guarantee that CI/CD promises.

---

*Next: How Monitoring Entered the Frontend. Error tracking, performance monitoring, session replay, the web application became observable infrastructure. Understanding what users are actually experiencing, not what you expect them to experience, changed how teams debug.*
