
In 2005, deploying a web application meant connecting to an FTP client, uploading files to a server, and hoping nothing broke. If it did break, you uploaded the fixed files. Rollback meant uploading the old files, if you'd kept them.

The release was a discrete event. Teams planned for it, tested thoroughly before it, and treated it as a milestone. "We're shipping on Thursday" was meaningful.

The shift from that model to continuous deployment, where every merge to main triggers an automatic deploy, changed more than the technical process. It changed the entire rhythm of how frontend software is made.

---

## The FTP era and its limits

Early web deployment was literally file transfer. A developer wrote HTML, CSS, and JavaScript files. An FTP client uploaded them to a web server. Done.

This worked for simple, static sites. The problems emerged with complexity. When multiple developers worked on the same files, FTP didn't understand version conflicts. There was no record of what changed, when, or why. Rollback required manually restoring old files, if you'd thought to save them.

Shared hosting environments typically didn't allow server-side scripting other than PHP, and deploying a database-backed PHP application required also running database migrations, a step that had to be done carefully to avoid downtime.

---

## Capistrano and structured deployment

Ruby on Rails popularized Capistrano around 2006 as a deployment automation tool. It scripted the process: connect to the server over SSH, pull the latest code from the Git repository, install dependencies, run database migrations, restart the server. Rollback was a command that switched back to the previous deployment directory.

This turned deployment from a manual, error-prone process into a repeatable script. The script could be run by any team member. It behaved the same way every time. Rollback worked.

The pattern, automated deployment scripts that could be versioned and trusted, became standard. Capistrano itself was Rails-specific, but the concept spread.

---

## Heroku and the push-to-deploy model

Heroku (2007) made deployment a single command:

```
git push heroku main
```

That was it. Heroku detected the language, installed dependencies, and ran the application. No server management, no SSH, no configuration beyond a Procfile. Developers could have an application running publicly in minutes from a standing start.

For startups, Heroku dramatically reduced the infrastructure barrier. You didn't need to know how to configure Nginx or set up a Linux server. You wrote code, you pushed, it was deployed.

The tradeoff was cost and control, Heroku was expensive at scale, and certain configurations required workarounds. But it established the expectation: deployment should be push-to-deploy, managed by the platform, invisible to the developer unless something goes wrong.

---

## Netlify, Vercel, and the frontend CDN era

Netlify (2015) and Vercel (2016) applied the Heroku model specifically to frontend development. Connect a Git repository, configure the build command, and every push to main deploys the site. Every pull request gets a preview deployment, a unique URL where you can see the branch before it's merged.

Preview deployments changed how frontend development works. Sharing a link to a feature branch with a designer, a product manager, or a client, letting them interact with the actual build, became a normal part of the workflow. "Here's the link to the PR, can you check if this looks right?" replaced "let me schedule a screen share."

Netlify added edge functions, form handling, and Lambda functions. Vercel added Edge Middleware, image optimization, and the infrastructure for Next.js's advanced rendering modes. Both platforms moved from "static site hosting" to "full frontend platform."

---

## The effect on release cadence

When deployment takes one command and preview deployments are automatic, the concept of a discrete "release" becomes less meaningful. Changes can ship as soon as they're merged and pass CI. Teams measure deploy frequency in days or hours, not weeks.

This changes the risk profile of each change. Smaller, more frequent changes have smaller blast radii. If a change breaks something, it broke one small thing, not a month's worth of work. Rolling back means reverting one commit or one PR, not unwinding a sprint.

The condition for this to work is that CI catches regressions before they merge. A team that deploys continuously but has no automated tests is shipping unknown risk continuously. The investment in testing and CI is the prerequisite for continuous deployment to be a feature rather than a liability.

---

## Immutable deployments

Vercel and Netlify's deployment model is immutable: each deployment is its own atomic snapshot. Assets are content-addressed and cached permanently. A deploy doesn't update files in place, it creates a new version of the entire deployment.

This has performance benefits, assets with content-hash filenames can be cached forever at the CDN edge. It also has operational benefits, instant rollback means switching the traffic pointing to the previous immutable deployment. The old deployment still exists, fully functional.

The immutable deployment model changes how you think about deployments. Not as "updating the server," but as "creating a new snapshot and switching traffic." That mental model makes rollback less scary, blue-green deployments natural, and atomic releases the default.

---

*Next: How Cloud Changed Where Frontend Lives. S3, CloudFront, Lambda@Edge, Cloudflare Workers, the frontend moved from servers to CDN edges. Understanding what that means changes how you think about where code runs.*
