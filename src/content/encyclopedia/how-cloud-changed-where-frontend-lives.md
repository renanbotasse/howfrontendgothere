
A web application in 2003 lived on a web server. One machine, one location, one network connection to the internet. You rented or owned the machine, you managed it, and if it went down, the site went down.

The shift to cloud infrastructure changed not just how servers are managed but where code runs and what "running frontend code" even means. Static files on a CDN, server-rendered responses from Lambda functions, edge middleware that runs 50ms from the user, the deployment topology of a modern frontend is nothing like a single machine in a datacenter.

---

## S3 and the static hosting model

Amazon S3 launched in 2006 as object storage. You upload files and they're accessible via URL. Static websites could be served directly from S3, HTML, CSS, JavaScript, images, with no server required.

Combined with CloudFront, Amazon's CDN, static files were distributed to edge locations globally. A user in Japan requesting a file would get it from Tokyo instead of a datacenter in Virginia. The latency improvement was significant and the cost per request was tiny.

This established the modern static hosting model. Netlify, Vercel, and others built their platforms on similar principles, your built output (a directory of files) gets distributed to CDN edges globally, and the CDN handles serving requests from the nearest point.

For sites that are genuinely static, no server-side rendering, no dynamic content, this is as good as deployment gets. The server is the CDN, and CDN servers are everywhere.

---

## Lambda and serverless

AWS Lambda (2014) let you run code without managing a server. Upload a function, define a trigger, and it runs when that trigger fires. A Lambda function that handles an API request runs when the request arrives, and stops when the request is complete. You pay per invocation, not for idle server time.

For frontend backends, small API functions that handle form submissions, authentication, data fetching, Lambda is a natural fit. Netlify Functions, Vercel Serverless Functions, and Cloudflare Workers are all variations on this model, abstracting the cloud provider's specific API.

A Next.js API route deployed on Vercel becomes a Lambda function. You write a function, it deploys automatically. The infrastructure management, scaling, load balancing, failover, is handled by the platform. The developer's concern is the function logic.

The tradeoff is cold starts. A Lambda function that hasn't been invoked recently takes time to start up, sometimes 100-500ms for simple JavaScript functions, potentially seconds for larger ones. For API endpoints that need consistent low latency, cold starts are a problem.

---

## Edge computing

Edge computing moves computation from centralized servers to points of presence near users. Cloudflare has over 300 edge locations globally. Vercel's edge infrastructure is similarly distributed. Code running at the edge runs 20-50ms from most users rather than 100-200ms from a central datacenter.

Edge Middleware, Edge Functions, Cloudflare Workers, these run JavaScript code at the CDN edge. Common uses: redirect logic, A/B testing, authentication checks, header manipulation, generating dynamic responses for static-style deployments.

The constraint is the runtime. Edge environments use a restricted JavaScript runtime, no Node.js APIs, no `fs`, limited capabilities compared to a full server. The V8 isolate model runs code in an extremely lightweight container (much lighter than a Lambda function) that starts in microseconds rather than milliseconds.

For frontend use cases that need low latency but don't need full server capabilities, edge functions fit well. Next.js Middleware runs at the edge, checking authentication, managing redirects, rewriting URLs, before the request reaches your origin server or even your serverless functions.

---

## The mental model shift

The traditional mental model: a server has a fixed location, runs continuously, and handles requests that are routed to it. Scaling means adding more servers. Deployment means updating those servers.

The cloud mental model: functions run when triggered. Code is replicated to many edge locations. Scale is automatic. Deployment is atomic, a new version of the code is prepared and traffic is shifted to it without stopping the old version.

Frontend developers increasingly need to understand where their code runs. React Server Components run on a server (which might be a Lambda function or an edge function). Client components run in the browser. Middleware runs at the edge. API routes run on a server. The "frontend" is distributed across this topology.

This is not more complex than it used to be, it's complex in different ways. Instead of "configure Nginx correctly," the complexity is "understand which runtime environment applies to this code and what it has access to." Understanding the deployment topology is becoming as important as understanding the component model.

---

*Next: How CI/CD Became Standard Practice. From manual deployments to GitHub Actions and automated pipelines, the transformation of how code goes from a developer's machine to production changed what "shipping" means.*
