
Before React, before Angular, before jQuery, before AJAX — before any of the frontend infrastructure that defines modern web development — the web worked on a simple model. A user requested a URL. The server generated HTML. The browser rendered it. Done.

That model is older than most of the frameworks developers use today, and it has come back into fashion in forms like Next.js, Remix, and Astro. Understanding what it was, and why it worked, makes the return make more sense.

---

## Documents, not applications

The early web was designed to share documents. Tim Berners-Lee built it at CERN to let physicists share research. The primitives — links, headers, paragraphs, forms — reflect that origin. A page was a document. Navigation meant going to a different document.

This framing mattered technically. HTML was markup: you described the structure of a document, and the browser decided how to render it. The server's job was to find or generate the document and send it. There was no concept of client-side state, because state lived in the document or in the server's database.

The first generation of websites were literally static — the same `.html` file was returned to every user who requested a given URL. No personalization, no session state, no dynamic content. Your blog post was a file on disk, and visiting `/about.html` meant the server opened that file and sent it.

---

## Server-side rendering — the original kind

As soon as developers wanted personalization — logged-in user names, shopping carts, search results — the static file approach stopped working. The solution was server-side rendering, though nobody called it that yet. It was just how you built websites.

A user requested `/profile`. The server received the request, read the session cookie, queried the database for that user's data, assembled an HTML string with that data interpolated, and returned the complete document. The browser got fully-formed HTML and rendered it immediately.

The technologies varied: CGI scripts in Perl, then PHP, then ASP, then JSP, then Ruby on Rails. The model was the same. The server owned all the logic. HTML was the output. The browser was a dumb renderer.

> 📸 **IMAGEM AQUI**
> Abra: https://developer.mozilla.org/en-US/docs/Learn/Server-side/First_steps/Client-Server_overview
> Role até o diagrama "A simple web server" (mostra o fluxo: browser request → server processa → HTML response → browser renderiza)
> Salve e faça upload.

PHP became dominant partly because it was cheap to host and required no compilation step. A `.php` file on a server was enough. The file mixed HTML and PHP code directly, which was criticized as bad practice but was enormously productive for developers who just wanted to ship a page.

Rails brought the model-view-controller pattern to web development in a way that was broadly adopted. Templates (views) generated HTML, controllers handled logic, models talked to databases. The request came in, the controller ran, the view rendered, the HTML went out. Clean, reproducible, fast to build.

---

## What worked well

A server-rendered page loads in a predictable way. The browser receives HTML. It parses it and displays content. No JavaScript needs to download, parse, and execute before the user sees anything.

This is not a small advantage. On a slow connection or a low-end device, the difference between receiving complete HTML and receiving an empty shell that fetches data before rendering anything is measured in seconds. Web performance research has consistently shown that perceived load time is one of the strongest predictors of user engagement and conversion.

Search engine crawlers also understood server-rendered HTML trivially. The page arrived complete. The crawler parsed it. No JavaScript execution required. SEO was not a concern in the same way it became once SPAs took over.

Caching worked naturally. An HTML response with appropriate `Cache-Control` headers could be cached at every level — browser, CDN, reverse proxy. For pages with the same content for all users, caching the HTML itself dramatically reduced server load.

And there was no client-server state synchronization problem. The server was the source of truth. The browser displayed what the server said to display. When the user submitted a form, the server processed it and returned a new document. No questions about what the client's local state should be.

---

## The limitations that led to JavaScript

The server-rendered model has a real constraint: every interaction that requires new data requires a round trip to the server and a full page reload. Clicking a tab on a dashboard. Updating a shopping cart quantity. Searching incrementally as you type.

Full page reloads meant a brief blank screen. The entire document had to be re-fetched and re-rendered, even if only a small part of it changed. For forms, a failed submission meant losing everything the user had typed if the server didn't carefully preserve state.

User expectations started shifting around the early 2000s as people experienced desktop applications with instant feedback. Gmail launched in 2004 with an interface that felt more like a desktop email client than a web page. Google Maps in 2005 let you drag the map without reloading the page. These experiences made traditional page reloads feel slow and primitive by comparison.

AJAX — XMLHttpRequest — was the technical mechanism that made those experiences possible. It let JavaScript make HTTP requests without reloading the page, fetch new data, and update the DOM. Once developers understood what AJAX could do, the trajectory toward client-side rendering was largely set.

---

## Why the model came back

Server-rendering went from "how you build websites" to "legacy approach" to "best practice" in the span of about 20 years. The SPA era (2010-2020, roughly) treated client-side rendering as the default. Then the pendulum swung back.

The reasons are the same ones that made server-rendering work in the first place. Initial page load performance matters a lot. SEO requires either server-rendered HTML or complex client-side hydration setups. For most applications, the complexity of managing client-side state for things the server could just render is not worth it.

Next.js popularized the idea that server-rendering and client-side interactivity aren't mutually exclusive. You can render the shell on the server, hydrate it on the client, and have both fast initial loads and interactive components. Remix pushed this further with a design that treats server rendering and progressive enhancement as the baseline, not the fallback.

The static web model didn't win or lose. It turns out it was right about a lot of things, and the industry spent fifteen years learning that lesson the hard way.

---

*Next: How the Web Learned to Move. AJAX changed everything. Understanding how asynchronous requests worked — and how messy the first implementations were — shows why promises and async/await were such significant improvements.*
