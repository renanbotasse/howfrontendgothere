
JavaScript was designed to live in browsers. Brendan Eich created it at Netscape in 1995 to let web pages respond to user interaction. For its first fifteen years, that's all it did. You couldn't run JavaScript anywhere except inside a browser tab.

Then Ryan Dahl spent a weekend in 2009 being annoyed by a progress bar.

---

## The problem Dahl was solving

The standard way to handle I/O in server languages at the time was blocking. Your program called a function to read a file, and execution stopped until the file was read. If you were handling web requests, each connection waiting for a database query or a file read was sitting there, consuming memory and a thread, doing nothing.

The solution was threads — run multiple things in parallel, one thread per connection. But threads are expensive, context-switching between them has overhead, and concurrent shared state is notoriously hard to reason about. Apache's thread-per-request model worked fine up to a point and then became a serious bottleneck under high load.

Dahl saw a different model in JavaScript. Because it ran in a browser with a single thread and an event loop, JavaScript had to be non-blocking by design. You couldn't block the thread waiting for a network request — you'd freeze the UI. Everything was asynchronous callbacks. And V8 was fast.

He took V8 out of Chrome, wrapped it with a system I/O library called libuv, and made the result available as Node.js. A JavaScript runtime that could run on a server, with non-blocking I/O built in.

---

## What Node.js actually changed

Node.js was not the first non-blocking server framework. Event-driven servers existed in other languages. What made Node different was the language.

For the first time, frontend developers could write server code in the same language they used for client code. Not just the same language in theory — the same files, the same npm packages, shared utilities between browser and server. The "full-stack JavaScript" developer became possible.

But the bigger change was what Node did to tooling. Once JavaScript could run outside the browser, you could write build tools in JavaScript. Grunt came in 2012, Gulp in 2013. Asset pipelines that previously required Ruby (Rails' asset pipeline) or specific server-side frameworks could now be written in the same language as the frontend code they processed.

npm — Node Package Manager — shipped with Node and became the distribution mechanism for JavaScript packages. This turned out to matter enormously.

---

## npm changes the dependency model

npm made publishing and consuming JavaScript libraries trivial. Before npm, sharing JavaScript code meant copy-pasting files, linking to CDN URLs, or vendor-specific package managers. npm introduced semantic versioning, a central registry, and a command-line tool that resolved and installed dependencies automatically.

The package count grew slowly at first, then explosively. By 2023, npm had over 2 million packages — more than any other language's registry. The speed of the ecosystem was partly due to npm's low friction to publish, and partly due to a cultural norm of small, focused packages.

`left-pad` — the module that checked whether a string needed padding and added spaces if so — had over 2 billion downloads before the incident in 2016 where its author unpublished it and briefly broke builds across the industry. The incident was widely mocked, but it illustrated something real: npm's model of many small packages with deep dependency trees created fragility. Removing one 11-line package could break thousands of projects because those projects didn't depend on it directly — they depended on something that depended on something that depended on it.

---

## The build step arrives

Node made it practical to have a build step for frontend code. Grunt and Gulp were task runners — you defined a pipeline that processed your JavaScript and CSS before serving it. CommonJS modules (`require()`) let you split JavaScript into files that imported each other, and tools like Browserify bundled those files into something the browser could load.

Webpack arrived in 2012 and became the dominant bundler for the next decade. It could handle JavaScript modules, CSS, images, fonts — everything as a module with explicit dependencies. It produced optimized bundles for production with code splitting, tree shaking, and aggressive caching strategies.

This is when frontend development got complicated in a new way. The code you wrote was no longer the code that ran. A build pipeline transformed it. Understanding what the build did — how modules were resolved, why something wasn't being tree-shaken, why a bundle was large — became a frontend skill that had nothing to do with building UIs.

> 📸 **IMAGEM AQUI**
> Abra: https://webpack.js.org/concepts/
> Role até o diagrama central do Webpack (mostra módulos de entrada sendo processados e saindo como bundle)
> Salve e faça upload.

---

## Deno and the ongoing evolution

Ryan Dahl revisited Node.js in 2018 and published a talk called "10 Things I Regret About Node.js." His criticisms: the callback-based design predated promises and was never updated, npm's security model was weak (any installed package had full filesystem access), and the module system was non-standard.

He built Deno as a response — a JavaScript runtime with TypeScript support built in, secure by default (explicit permissions required for filesystem and network access), using ES modules instead of CommonJS, and with a standard library instead of relying entirely on npm.

Deno hasn't displaced Node, but it pushed Node to improve. Node added native ESM support, improved its permission model, and added native TypeScript support through experimental flags.

Bun arrived in 2022 as a third runtime, focused on raw speed. It re-implemented the Node API for compatibility but used JavaScriptCore instead of V8 and optimized aggressively for startup time and throughput. It also ships as a bundler and test runner.

The browser's runtime left the browser and fractured into an ecosystem. The JavaScript that a frontend developer writes today might run in a browser, in Node.js, in Deno, in Bun, in a Cloudflare Worker, in an AWS Lambda, or in a build tool. Same language, very different environments, and the differences matter.

---

*Next: How the Client Became the Application. With a build step, npm, and Node.js providing infrastructure, the conditions were in place for single-page applications. What SPAs were trying to do, and what they got wrong, is the story of frontend's most ambitious decade.*
