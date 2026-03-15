
HTTP is a pull protocol. The client asks, the server answers. The server cannot push data to the client without being asked. This is fine for fetching a product page or submitting a form. It's fundamentally incompatible with features like live chat, collaborative editing, real-time notifications, or any experience where the server needs to say "something changed" without waiting to be asked.

Making the web real-time required building on top of HTTP's request-response model, and then eventually replacing that model for certain connection types.

---

## Polling — asking repeatedly

The first approach was polling: the client sends a request every few seconds and asks "anything new?" The server responds with updates or an empty response if nothing changed.

This works. It's also wasteful. Most requests return nothing, because most of the time nothing has changed. On an application with many concurrent users polling frequently, this generates significant server load for little value.

Adaptive polling helped: if a request returns nothing, wait longer before the next one. If a request returns data, poll more frequently for a while. But it was still fundamentally the client pulling, not the server pushing.

---

## Long polling — holding the connection

Long polling is a refinement. The client sends a request. The server holds the connection open instead of responding immediately. When data becomes available, the server responds. The client immediately sends another request. The connection is never idle for long, but the client doesn't send a new request until the previous one resolves.

This reduced the number of empty responses and improved the latency for receiving updates. Comet, a technique from around 2006-2007, described this pattern and variations on it. Gmail's chat feature was one of the high-profile early uses.

Long polling has overhead from HTTP connection management — each "held" connection occupies a thread or connection slot on the server, depending on the server architecture. In Node.js's event-driven model, holding connections is cheaper than in thread-per-request models, which is part of why Node.js was well-suited for real-time applications.

---

## Server-Sent Events — one-way streaming

Server-Sent Events (SSE) is an HTTP-based protocol for server-to-client streaming. The client makes one HTTP request. The server responds with `Content-Type: text/event-stream` and keeps the connection open, sending events formatted as text:

```
data: {"message": "Hello", "userId": "123"}

data: {"message": "New order placed", "orderId": "456"}
```

The `EventSource` API in the browser handles reconnection automatically if the connection drops. SSE is simple and works over standard HTTP, including through proxies and CDNs that are often problematic for WebSockets.

The limitation is directionality — SSE is one-way, server to client. The client can't send messages over the same connection. For notifications, live feeds, and streaming AI responses, this is fine. For bidirectional communication, it's not enough.

SSE has seen renewed interest as AI-generated streaming responses became common. When Claude or GPT streams text tokens one at a time to the browser, SSE is often the protocol underneath — the server sends a new event for each token, and the browser appends it to the displayed text.

---

## WebSockets — the full-duplex connection

WebSockets, standardized in 2011, establish a full-duplex communication channel between browser and server over a single TCP connection. The connection starts as an HTTP request that upgrades:

```
Client: Upgrade to WebSocket
Server: 101 Switching Protocols
```

After the handshake, either side can send messages at any time. The overhead per message is minimal — a small header, not a full HTTP request. This makes WebSockets efficient for high-frequency updates.

WebSockets are the right choice for bidirectional real-time communication: chat applications, multiplayer games, collaborative document editing, live dashboards with user interactions. They require server infrastructure that can maintain long-lived connections — traditional load balancers and servers need to be configured to handle this correctly.

> 📸 **IMAGEM AQUI**
> Abra: https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API
> Role até o diagrama ou ilustração que mostra o WebSocket handshake e comunicação bidirecional
> Salve e faça upload.

---

## Libraries and services that abstract the protocol

Building real-time features from raw WebSockets or SSE requires handling reconnection, message serialization, room management, presence, and scaling across multiple server instances. A user connected to server A can't receive messages from a user connected to server B without a shared message broker.

Socket.io (2010) provided a higher-level API on top of WebSockets with automatic fallback to long polling for environments where WebSockets weren't available. It handled reconnection and events out of the box and became the standard for Node.js real-time applications.

Services like Pusher (2010), Ably, and later Supabase Realtime abstract the infrastructure entirely. You publish an event to a channel; all subscribers receive it. The scaling, connection management, and delivery guarantees are the service's problem.

Firebase Realtime Database and Firestore offered a model where the data store itself was the real-time layer — subscribe to a data path, receive updates whenever that data changes. This made real-time features accessible to developers who hadn't built real-time systems before.

---

## WebTransport and the future

WebTransport is a newer API built on HTTP/3's QUIC protocol. Like WebSockets, it provides full-duplex communication. Unlike WebSockets, it supports multiple independent streams within one connection, so a slow message on one stream doesn't block other streams. It's better suited for real-time applications where some messages are more urgent than others — games, live video, high-frequency data.

WebTransport is in early adoption. Browser support exists but deployment is limited. It represents the direction of real-time communication for applications that need the lowest possible latency.

---

## Choosing the mechanism

For simple server-to-client updates with no need for client messages, SSE is often the right choice. It works over standard HTTP, reconnects automatically, and is simpler to implement than WebSockets.

For bidirectional communication or high-frequency updates, WebSockets are appropriate.

For applications that want to avoid infrastructure complexity, a managed real-time service handles the hard parts at the cost of a dependency and per-message pricing.

The underlying protocol matters less to most developers than to the infrastructure teams managing it. What matters is that real-time features work reliably, reconnect gracefully, and scale with the application's needs.

---

*Next: How Identity Became a Frontend Problem. Authentication used to live entirely on the server. Sessions, cookies, and login forms were server concerns. The SPA era moved the browser into the authentication flow in ways that created new security challenges.*
