
HTTP is the reason the web works the way it does. Not just "how requests and responses work" in the abstract — the specific quirks of how HTTP was designed, and then redesigned, and then redesigned again, left fingerprints on everything from how browsers load pages to why frontend tooling bundles files the way it does.

Most developers know HTTP exists. Fewer know why it evolved, and what each version was actually trying to fix.

---

## HTTP/0.9 — one line, one response, done

The original HTTP, from 1991, was barely a protocol. A client sent one line:

```
GET /index.html
```

The server responded with an HTML file and closed the connection. No headers. No status codes. No metadata of any kind. The assumption was that you were fetching a hypertext document and that was all you needed.

That assumption didn't survive contact with the real web for long.

---

## HTTP/1.0 — headers arrive, but connections don't

By 1996, HTTP/1.0 formalized what people were already doing in practice: headers. Now requests and responses could carry metadata. Content-Type let the browser know what kind of data it was receiving. Status codes let the server communicate success, failure, and redirection. User-Agent told the server what client was asking.

The problem was connection management. Every single request opened a new TCP connection, which meant a new handshake for every resource. An HTML page that referenced ten images made eleven TCP connections. Each one paid the handshake cost. On networks of the time, that was painful.

---

## HTTP/1.1 — persistent connections and a decade of workarounds

HTTP/1.1 arrived in 1997 and fixed the connection problem with persistent connections. The connection stayed open between requests. No new handshake for each resource.

But a new constraint emerged: head-of-line blocking. With HTTP/1.1, a connection could only process one request at a time. If a large file was downloading, everything else waited behind it. Browsers worked around this by opening multiple parallel connections to the same server — usually six. That's why six is a magic number in web performance discussions from the 2000s and early 2010s.

This workaround had its own consequences. With six connections per domain, having multiple domains for resources — domain sharding — became a performance technique. Developers split assets across `static1.example.com`, `static2.example.com`, and so on to get more parallel connections.

HTTP/1.1 also introduced chunked transfer encoding, which let servers start sending a response before they knew the total size. And it introduced the `Host` header, which made virtual hosting possible: one IP address could serve multiple domains because the browser told the server which one it wanted.

HTTP/1.1 lasted, essentially unchanged, for almost two decades. The workarounds became standard practice. Concatenating JavaScript files into one bundle, spriting images, inlining small images as base64 — all of it was optimizing for the constraints of HTTP/1.1.

---

## SPDY and the path to HTTP/2

Google built SPDY in 2009 as an experiment: what if you could multiplex multiple requests over a single connection? Instead of head-of-line blocking, you'd have parallel streams over one TCP connection.

SPDY also compressed headers. HTTP/1.1 sent headers as plain text on every request, and headers repeated a lot of the same information — the `Cookie` header alone could be kilobytes. Compressing them reduced overhead significantly.

The results were good enough that SPDY became the basis for HTTP/2.

---

## HTTP/2 — multiplexing, streams, and server push

HTTP/2 was standardized in 2015. The core change was multiplexing: multiple requests and responses in parallel over a single connection, without blocking each other. One connection per origin, but streams within that connection can interleave freely.

> 📸 **IMAGEM AQUI**
> Abra: https://web.dev/articles/performance-http2
> Role até o diagrama comparando HTTP/1.1 vs HTTP/2 (mostra múltiplas conexões vs. uma conexão com streams)
> Salve e faça upload.

HTTP/2 also made some of the HTTP/1.1 workarounds counterproductive. Domain sharding now hurts performance, because it means multiple connections instead of one multiplexed connection. Concatenating files into bundles is less important, because multiplexing lets multiple files download simultaneously without the per-request overhead.

Server push was HTTP/2's more ambitious feature: the server could proactively send resources the client hadn't asked for yet. The idea was to push CSS and JavaScript alongside the HTML, before the browser even parsed the HTML and discovered it needed them. In practice, server push was hard to implement correctly and was eventually removed from most implementations.

Header compression became HPACK, a specifically designed compression format for HTTP headers that was resistant to certain attacks that generic compression was vulnerable to.

---

## HTTP/3 — TCP was the real problem

HTTP/2 solved the HTTP-level head-of-line blocking, but left in place the TCP-level head-of-line blocking. If a TCP packet was lost, all streams on that connection had to wait for the retransmission, even streams that had no relation to the lost packet.

HTTP/3 replaced TCP with QUIC, a protocol built on UDP that implements its own reliability and multiplexing. Each stream is independent at the transport level, so a lost packet only blocks the stream it belongs to.

> 📸 **IMAGEM AQUI**
> Abra: https://www.cloudflare.com/learning/performance/what-is-http3/
> Role até o diagrama comparando HTTP/2 (TCP) vs HTTP/3 (QUIC)
> Salve e faça upload.

QUIC also integrates TLS negotiation into the connection setup, reducing round trips. Where HTTP/2 over TLS needed a TCP handshake plus a TLS handshake, HTTP/3 combines them.

HTTP/3 became an IETF standard in 2022. Most major CDNs support it. Support in browsers has been widespread since 2022 and 2023. It matters most on high-latency or lossy connections — mobile networks, satellite, situations where packet loss is common.

---

## What this means for frontend decisions

The version of HTTP you're using changes which optimizations help and which ones hurt.

If you're still on HTTP/1.1 — possible on internal tools, older servers, or certain hosting environments — concatenating files and reducing request count still matters. On HTTP/2 and HTTP/3, granular bundles are better than monolithic ones, because multiplexing handles the parallel requests efficiently. Route-based code splitting became practical largely because HTTP/2 made the per-request overhead negligible.

The `Cache-Control` header exists in all versions, but its interaction with CDNs and service workers depends on correctly understanding what gets cached where. `ETag` and `Last-Modified` are still how conditional requests work — the browser asks "has this changed since this timestamp?" and gets back either a 304 or a full response.

HTTP's evolution is a case study in constraint management. Each version was designed in response to specific bottlenecks that the previous version created or left unaddressed. The web you use today is shaped by decisions made to work around the limitations of 1990s connection management.

---

*Next: How Web Standards Are Made. HTTP evolves through the IETF. HTML and CSS evolve through the W3C and WHATWG. Understanding how standards bodies work explains why the web changes slowly, and why that's both frustrating and probably correct.*
