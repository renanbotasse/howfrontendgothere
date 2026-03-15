
In 2010, a web application could display content, handle user input, make HTTP requests, and store a small amount of data in cookies. That was largely it. The browser was a document viewer with some interactivity. If you needed to access the camera, the file system, Bluetooth, or a local database, you needed a native application.

The browser in 2024 can do most of what a native application can do. Not all of it — some APIs are still native-only, and native performance remains better for compute-intensive tasks. But the gap has narrowed in ways that have fundamentally changed what "building a web application" means.

---

## The capability expansion

**Web Workers** (2009) moved JavaScript computation off the main thread. A worker runs in a separate thread with no access to the DOM, communicating with the main thread via messages. This enabled CPU-intensive tasks — image processing, complex calculations, parsing large datasets — without blocking the UI.

**Service Workers** (2015) are a more powerful version: scripts that run in the background, intercept network requests, manage a cache, and enable offline functionality. A service worker can serve cached responses when the network is unavailable, enabling web applications that work offline. They're the foundation of Progressive Web Apps.

**IndexedDB** (2011, widely available by 2014) is a proper client-side database — structured, queryable, transactional, capable of storing large amounts of data. Not just cookies or localStorage strings. Real structured data, locally. Applications like Google Docs use IndexedDB to store document content locally, enabling offline editing.

**WebGL** (2011) exposed the GPU for 2D and 3D graphics. Browser-based games, 3D visualization tools, and graphics-intensive applications became possible. Figma is a notable example: a vector graphics editor that runs in the browser using WebGL for rendering.

**WebRTC** (2013) enabled real-time audio and video communication in the browser. Browser-to-browser video calls, screen sharing, and peer-to-peer data transfer without a server relay. Google Meet, Zoom's browser client, and Discord's browser interface all use WebRTC.

**WebAssembly** (2019) is a binary instruction format that runs in browsers at near-native speed. Languages like C, C++, Rust, and Go compile to WebAssembly and run in browsers with performance characteristics unachievable with JavaScript. Figma's rendering engine is in part WebAssembly. Google Earth uses it. AutoCAD runs in the browser via WebAssembly.

**File System Access API** (2019-2022) lets web applications read and write to the local file system with user permission. A browser-based text editor that opens and saves files directly on disk. A browser-based IDE. VS Code on the web uses this API.

---

## Progressive Web Apps

Service workers, the Web App Manifest, and push notifications together define what's called a Progressive Web App (PWA). A PWA can be "installed" — added to the home screen or taskbar — and behaves more like a native app: it launches without a browser chrome, works offline, receives push notifications.

The adoption of PWAs has been uneven. On Android, PWA installation and functionality is well-supported. On iOS, Apple limited PWA capabilities significantly — no persistent push notifications, storage limits, no access to certain hardware APIs — in ways that preserved the competitive moat of the App Store. This was partly reversed under regulatory pressure in Europe, and the situation continues to evolve.

For applications where the primary need is a web-native experience with offline support, PWAs are a serious option. For applications that need access to hardware features Apple restricts or need to monetize through app store purchases, native remains necessary.

---

## WebAssembly and the performance frontier

WebAssembly is the most significant expansion of the browser's capability ceiling. JavaScript is fast — V8 and SpiderMonkey are impressive JIT compilers — but it's not as predictable as native code, and garbage collection pauses create jitter that's unacceptable for real-time audio, games, and certain computation.

WebAssembly runs deterministically. There's no garbage collector. Memory management is explicit. For code that needs guaranteed performance characteristics, this matters.

The practical impact: Python can run in the browser (Pyodide). Full image editing pipelines can run in the browser. Physics engines run at 60fps. The line between what requires a download and installation and what runs in a URL is no longer "anything performance-sensitive."

---

## What browser capabilities mean for frontend development

The browser's capability expansion changed the category of applications you can build on the web. Figma — a professional design tool — runs in the browser. VS Code — a full IDE — runs in the browser. Google Docs — a word processor — runs in the browser. These would have been native applications in 2010.

This creates a different set of frontend skills. Building a web application that uses a database that stores data locally, processes images client-side in a Web Worker, syncs with a server in the background via a Service Worker, and works offline requires understanding APIs that look nothing like the DOM manipulation of the jQuery era.

It also changes the expectations on frontend engineers. "Web developer" increasingly means "engineer who builds software that runs in browsers that can do nearly everything." The skill set required is closer to the skill set of a native application developer than it was ten years ago.

---

*Next: How Internationalization Became Infrastructure. Translating a web application used to be an afterthought. Making it actually work — right-to-left text, date formats, plural rules, character encoding — turned out to require significant infrastructure.*
