
Backend services have been monitored since servers existed. CPU usage, memory consumption, request error rates, database query times — the infrastructure had metrics and alerts before most developers gave the frontend a second thought.

Browsers are different from servers. You can't SSH into a user's browser and check the logs. You can't watch a real-time metrics dashboard for client-side JavaScript errors. When a user encounters a bug on your frontend, the default is that you don't know it happened unless they tell you.

Most users don't tell you.

The shift toward frontend observability — knowing what's actually happening in real users' browsers — changed how frontend bugs are discovered and debugged.

---

## The JavaScript error problem

JavaScript errors in production were invisible for most of web history. The browser's developer tools showed them, but only if you had the console open. Users saw a broken page and either left or didn't notice the specific element that stopped working.

Sentry (2012) solved this by capturing unhandled JavaScript exceptions in the browser and sending them to a collection service. A few lines of initialization code, and every unhandled error in your application is logged with the full stack trace, the user's browser, operating system, the URL they were on, and the code that caused it.

```javascript
import * as Sentry from '@sentry/react';

Sentry.init({ dsn: 'https://...' });

// Every unhandled error is now captured automatically
```

This was a significant shift. Bugs that would have gone undiscovered until a user reported them were now visible in a dashboard with occurrence counts, affected user counts, and stack traces. Teams could prioritize bugs by impact — how many users are hitting this? — rather than discovery order.

Source maps let Sentry display the original TypeScript or JSX code in stack traces, not the minified bundle. Without source maps, a production error points to a line in a minified bundle: line 1, column 47823, in `a.b.c.d`. Undebuggable. With source maps, it points to the original file and line.

---

## Real User Monitoring

Error tracking captures failures. Real User Monitoring (RUM) captures performance. Tools like Datadog RUM, New Relic Browser, and Vercel Analytics inject scripts that measure Core Web Vitals, resource loading times, API call latencies, and JavaScript execution times in real users' browsers.

The data answers questions that synthetic testing can't: which pages are slow for real users? Which API calls are taking too long in production? What's the distribution of LCP scores across devices and geographies?

The distribution is usually the most important insight. Average LCP might be 2 seconds — technically acceptable. P95 LCP (the slowest 5% of users) might be 6 seconds. Those users have a bad experience that averages don't surface. Understanding the distribution of performance metrics leads to different optimization priorities than optimizing the median.

---

## Session replay

Tools like LogRocket, FullStory, and Hotjar record user sessions — not video, but DOM snapshots and events that can be replayed to show exactly what a user did and saw. When a user encounters an error, you can watch the replay of their session to see what they clicked, what data was loaded, and what the UI looked like when the error occurred.

Session replay is how you diagnose bugs that are hard to reproduce. A user reports "the checkout button didn't work." You open the session replay, watch their session, and see that they had an unusual combination of items in their cart that triggered a state management edge case. You find the bug in 10 minutes instead of 2 hours of speculation.

Privacy concerns are real. Session replay captures what users type and click, which can include passwords, credit card numbers, and personal information if not handled carefully. Good tools provide automatic masking of sensitive inputs. Good implementations verify that masking is working correctly. Some organizations and jurisdictions require explicit user consent for session recording.

---

## Feature flags and controlled rollouts

Monitoring made gradual rollouts more tractable. If you deploy a new feature to 10% of users and monitor error rates, performance metrics, and conversion rates for that cohort versus the 90% on the old experience, you have data before committing the rollout.

A feature that looks fine in staging breaks for 0.5% of users in production because of a specific data combination you didn't test. Monitoring it in a controlled rollout gives you visibility into that 0.5% before it becomes 100%.

This turns deployment from a binary — ship it or don't — into a gradient. The risk of shipping something bad is managed by controlling exposure and having instrumentation that would surface the problem quickly.

---

## Alerting and response

Monitoring is only useful if someone sees the problems it surfaces. Sentry can alert on new issues or sudden spikes in existing ones. RUM tools can alert when p95 LCP exceeds a threshold. Synthetics (simulated user journeys that run on a schedule) can alert when a critical user flow breaks.

Effective alerting is calibrated: enough alerts to know when something is actually wrong, not so many that the team learns to ignore them. Alert fatigue — where the system generates so many notifications that real problems are buried in noise — is a common failure mode.

The discipline of setting meaningful alert thresholds, assigning clear ownership, and maintaining runbooks for known alert types is backend ops discipline applied to frontend. It reflects that frontend applications are production systems, not just delivery vehicles for HTML.

---

*Next: How AI Entered the Development Workflow. Copilot, Cursor, Claude — AI-assisted coding changed the experience of writing code faster than most industry shifts. What it actually changed, and what it didn't, is worth being precise about.*
