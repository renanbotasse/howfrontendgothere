
Every time you open GitHub, your browser does a series of things before a single byte of your code arrives on screen. Most developers have a rough idea of what happens, server, response, done. That's usually enough.

Until it isn't.

Your CDN is serving stale content two hours after a deploy. Your app loads in two seconds locally and eight seconds on a real user's phone. You added HTTPS and something in your pipeline broke. These aren't React problems. They're not Next.js problems. They happen below the framework layer, in the network, and the only way to debug them is to understand what's actually there.

So let's walk through it.

---

## The internet runs in layers

TCP/IP is the foundation. It's a set of protocols organized so that each layer handles one concern and hands off to the layer above it.

![TCP/IP four-layer model: Application, Transport, Internet, Network Access](/images/tcp-ip-layers.svg)

The **Network Access** layer moves bits through the physical world. Cables, WiFi, fiber. It doesn't know what those bits mean.

The **Internet (IP)** layer handles addressing. Every device has an IP address, and this layer figures out how to route a packet from one address to another, even through dozens of intermediate machines.

The **Transport (TCP)** layer handles reliability. The internet drops packets, reorders them, corrupts them. TCP detects all of that and asks for retransmission, so the layers above can pretend they're working with a reliable connection.

The **Application** layer is where you actually live. HTTP, DNS, WebSockets.

Why does this matter? Because failures happen at specific layers. Slow DNS lookup is an application layer problem. Packet loss on a mobile network is a transport layer problem. CDN serving stale content is an application layer problem. Once you know where to look, diagnosis stops being guesswork.

---

## What happens before your browser sends a request?

When you type `github.com`, your browser has no idea where that is. It knows IP addresses like `140.82.121.4`, not names. DNS is the system that translates one into the other.

![DNS resolution flow: browser cache → resolver → root → TLD → authoritative](/images/dns-resolution.svg)

The browser checks its own cache first. If it resolved `github.com` recently, it reuses the result. If not, it asks the OS, which has its own cache and a `hosts` file. If nothing there, the query goes to a DNS resolver, usually from your ISP or a public one like Cloudflare (1.1.1.1).

That resolver does the actual work. It asks a root nameserver where to find `.com` domains, then asks the `.com` nameserver where to find `github.com`, then asks GitHub's own nameserver for the IP. The answer comes back and gets cached for however long the DNS record specifies. That duration is the TTL, Time to Live.

The whole thing takes 20 to 120 milliseconds on a cold cache.

A few places this becomes practical: when you migrate to a new host and update your DNS, users keep seeing the old site until their cached TTL expires. When you set up a CDN, you point a DNS record at it and the CDN proxies to your origin. When you add a custom domain to Vercel, you're creating a CNAME record. Same mechanics, every time.

---

## Why does every connection cost a round trip before anything moves?

Once the browser has the IP, it needs to open a connection. TCP does this with a three-way handshake.

```
Client → Server:  SYN
Server → Client:  SYN-ACK
Client → Server:  ACK
```

One round trip before any data moves. On a connection with 50ms latency, that's 50ms just to say hello.

After the handshake, TCP maintains the connection by requiring acknowledgment of every chunk of data sent. If something isn't acknowledged in time, it resends. A bad network doesn't break the connection, it just makes everything slow as TCP keeps retrying and waiting.

Every HTTPS connection needs a TCP handshake plus a TLS handshake on top. HTTP/1.1 opened multiple TCP connections per domain to parallelize requests, and each one paid this full cost. HTTP/2 changed that by multiplexing everything over a single connection, which is one of the actual reasons upgrading to it was worth doing.

CDN proximity matters more than most people realize. A TCP handshake to a server 20ms away costs 20ms. To a server 150ms away, it costs 150ms. Moving the server physically closer to the user is as valuable as caching content on it.

---

## HTTPS isn't just encryption

After the TCP handshake, a TLS handshake runs on top.

The browser sends the TLS versions and cipher suites it supports. The server picks one and responds with its SSL certificate. The browser verifies that certificate against a list of trusted Certificate Authorities. Both sides derive encryption keys, and everything from that point is encrypted.

That certificate step is what makes HTTPS meaningful. Without it, you'd be encrypting a connection to whoever is on the other end, which could easily be someone impersonating the real server. The certificate is the verification that you're talking to who you think you're talking to.

TLS 1.3 reduced the handshake to one round trip instead of two. So a full HTTPS connection now costs one TCP round trip plus one TLS round trip before your first HTTP request even goes out.

For most frontend work, TLS is mostly invisible. But it explains why mixed content warnings appear when an HTTPS page loads HTTP resources, why an expired certificate kills the site entirely, and why HSTS headers tell browsers to always use HTTPS even if someone types `http://`.

---

## The request and response

With the connection open, the browser sends an HTTP request: a method and path, headers with metadata, and sometimes a body.

![HTTP request and response between client and server](/images/http-request-response.svg)

The response comes back with a status code, headers, and a body.

Status codes worth knowing: `200` means it worked. `301` means the URL changed permanently. `304` means your cached version is still valid and you don't need to download anything. `401` means not authenticated. `403` means authenticated but not allowed. `404` means the resource doesn't exist. `500` means the server broke.

`304` is the one developers most often misread as an error. It isn't. It means the browser checked with the server, was told "what you have is still current," and used the cached version. It saved you a full download.

---

## Cache headers are where most of the performance lives

HTTP has a caching system built in. Used correctly, it's the most effective performance tool you have. Used incorrectly, users see outdated content after every deploy.

`Cache-Control: max-age=31536000, immutable` tells browsers to cache the response for one year and never revalidate. Use this on JavaScript and CSS files that have content hashes in their filenames. The hash changes when the content changes, which changes the filename, which means the old cached file is never served again.

`Cache-Control: no-cache` tells browsers to always check with the server before using the cached version. Despite the name, it doesn't prevent caching, it forces revalidation. If nothing changed, the server returns `304` and the browser uses what it already has. If something changed, it gets the new version.

The pattern that works: HTML files get `no-cache`, assets get long-lived cache with content hashes. Your HTML always arrives fresh and points to the current asset filenames. Users with cached old assets ignore them because the filenames changed.

Vercel and Netlify implement this automatically for static deployments. Understanding why they do it is more useful than just knowing that they do.

---

## From pressing Enter to the first pixel

Put it all together:

```
1. DNS resolution
   browser cache → OS cache → resolver → root → TLD → authoritative

2. TCP handshake (1 round trip)

3. TLS handshake (1 round trip with TLS 1.3)

4. HTTP request

5. Server processes

6. HTTP response

7. Browser parses HTML, fetches sub-resources

8. Render pipeline

9. Pixel on screen
```

Every performance technique in frontend targets one of these steps. CDNs shorten steps 1 through 3 by moving servers closer to users. HTTP/2 and HTTP/3 improve step 4. Server-side rendering helps step 7 by sending complete HTML. Caching can skip steps 1 through 6 entirely for repeat visits.

When something is slow, the Network panel in DevTools breaks down each request into DNS lookup, connection, TLS, waiting, and downloading. Those labels map directly to the steps above. Once you can read that timing breakdown, you know exactly where the time is going.

---

*Next: How the Browser Works. Once the HTTP response arrives, the browser has to turn HTML, CSS, and JavaScript into pixels. That process has its own constraints, and they explain a lot about why every UI framework makes the choices it makes.*
