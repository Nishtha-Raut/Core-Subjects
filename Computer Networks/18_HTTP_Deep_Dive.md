# 18: HTTP Deep Dive

## 1. What is HTTP?
- **HTTP (HyperText Transfer Protocol)** is the **Application Layer** protocol that underlies data communication on the World Wide Web — used for transferring web pages, APIs data, images, etc.
- **Stateless:** Each HTTP request is **independent** — the server doesn't inherently "remember" anything about previous requests from the same client (state is managed via other means, like Cookies/Sessions — see below).
- **Client-Server model:** A client (browser, app) sends a **Request**; the server sends back a **Response**.
- Runs over **TCP** (Port 80 for HTTP, Port 443 for HTTPS) traditionally — though **HTTP/3** changes this (see below).

## 2. HTTP Request/Response Structure

```
HTTP REQUEST:                          HTTP RESPONSE:
GET /index.html HTTP/1.1               HTTP/1.1 200 OK
Host: www.example.com                  Content-Type: text/html
User-Agent: Mozilla/5.0                Content-Length: 1256
Accept: text/html                      Server: nginx
(empty line)                           (empty line)
(optional body - for POST/PUT)         <html>...</html>  <- body
```

## 3. HTTP Methods

| Method | Purpose | Idempotent? | Safe? |
|---|---|---|---|
| **GET** | Retrieve a resource | Yes | Yes (no side effects) |
| **POST** | Create a new resource / submit data | No | No |
| **PUT** | Update/replace a resource entirely | Yes | No |
| **PATCH** | Partially update a resource | No (typically) | No |
| **DELETE** | Remove a resource | Yes | No |
| **HEAD** | Like GET, but returns only headers (no body) — used to check resource metadata without downloading it | Yes | Yes |
| **OPTIONS** | Asks the server what methods/options are supported for a resource (used heavily in CORS preflight requests) | Yes | Yes |

> **Extra — Idempotent vs Safe (a subtle but common interview distinction):**
> - **Safe:** The method doesn't change server state at all (read-only) — e.g., GET.
> - **Idempotent:** Making the SAME request multiple times has the SAME effect as making it once (though it CAN change state) — e.g., PUT (setting a resource to a specific value repeatedly still leaves it in that same final state). POST is neither safe nor idempotent, since submitting the same POST twice (e.g., "place an order") could create two separate orders.

## 4. HTTP Status Codes

| Range | Category | Common Examples |
|---|---|---|
| **1xx** | Informational | 100 Continue |
| **2xx** | Success | 200 OK, 201 Created, 204 No Content |
| **3xx** | Redirection | 301 Moved Permanently, 302 Found (temporary redirect), 304 Not Modified |
| **4xx** | Client Error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 429 Too Many Requests |
| **5xx** | Server Error | 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable |

> **Extra — 401 vs 403 (extremely common interview trick question):**

| Code | Meaning |
|---|---|
| **401 Unauthorized** | You are **not authenticated** (not logged in, or invalid/missing credentials) — "who are you?" |
| **403 Forbidden** | You **are authenticated**, but you don't have **permission** to access this resource — "I know who you are, but you can't do this." |

## 5. Cookies & Sessions
- Since HTTP is **stateless**, web applications need a way to maintain **state** (e.g., "is this user logged in?") across multiple requests.

### Cookies
- A small piece of data the **server** sends to the client (via the `Set-Cookie` response header), which the client's browser **stores** and automatically **sends back** with every subsequent request to that domain (via the `Cookie` request header).

### Sessions
- Instead of storing all user data directly in the cookie itself, the server typically stores a small, unique **Session ID** in the cookie — the actual session data (user info, cart contents, etc.) is stored **server-side**, keyed by that Session ID.

```
1. Client logs in ------> Server validates, creates session data server-side,
                            generates Session ID
2. Server -----Set-Cookie: session_id=abc123----> Client (browser stores this)

3. Client -----Cookie: session_id=abc123----> Server (sent automatically
                                                 with every future request)
4. Server looks up "abc123" in its session store, finds the user's data
```

> **Extra — Cookies vs Sessions vs Tokens (JWT) comparison:**

| Aspect | Cookie (basic) | Session (server-side) | JWT (Token-based) |
|---|---|---|---|
| Where state lives | Can be stored client-side directly (or just hold a session ID) | Server-side (Cookie just holds a reference/ID) | Entirely client-side (self-contained, signed token) |
| Server storage needed? | Depends | Yes (needs a session store) | No (server just verifies the token's signature) |
| Scalability | — | Harder to scale (session store must be shared/replicated across servers) | Easier to scale (stateless — any server can verify the token independently) |

## 6. HTTP/1.1 vs HTTP/2 vs HTTP/3

### HTTP/1.1 (1997)
- **Persistent connections:** Reuses a single TCP connection for multiple requests (avoiding the overhead of a new TCP handshake per request, an improvement over HTTP/1.0).
- **Problem — Head-of-Line (HOL) Blocking:** Requests on the same connection are processed **one at a time, in order** — a slow response blocks all subsequent responses queued behind it on that connection. Browsers work around this by opening multiple parallel TCP connections (typically 6 per domain), but this has its own overhead.

### HTTP/2 (2015)
- **Multiplexing:** Allows **multiple requests and responses to be in flight SIMULTANEOUSLY over a single TCP connection**, interleaved as small "frames" — solving HTTP/1.1's application-layer Head-of-Line blocking.
- **Header Compression (HPACK):** Reduces overhead from repetitive header data sent with every request.
- **Server Push:** Server can proactively send resources it anticipates the client will need (e.g., CSS/JS files alongside the HTML), without waiting for an explicit request (though this feature saw limited real-world adoption and has since been deprecated in most browsers).
- **Still has a subtler problem:** Since HTTP/2 still runs over a single TCP connection, if a single **TCP packet** is lost, TCP's own in-order delivery guarantee blocks ALL the multiplexed streams within that connection until the lost packet is retransmitted — this is **TCP-level Head-of-Line blocking**, which HTTP/2 doesn't fully solve.

### HTTP/3 (2022, standardized)
- **Runs over QUIC** (built on **UDP**, not TCP!) instead of TCP.
- **QUIC solves TCP-level HOL blocking** by handling multiple streams independently within its own protocol — a lost packet affecting one stream doesn't block the other, unrelated streams (unlike TCP, where loss recovery blocks the ENTIRE connection).
- **Faster connection establishment:** QUIC combines the transport handshake and TLS encryption handshake into a single, combined step (0-RTT or 1-RTT connection setup, versus TCP+TLS's separate multi-step handshakes).
- **Built-in encryption:** QUIC mandates encryption (TLS 1.3) as a core part of the protocol itself, not an optional add-on.

> **Extra — HTTP/1.1 vs HTTP/2 vs HTTP/3 comparison table (very common interview question):**

| Aspect | HTTP/1.1 | HTTP/2 | HTTP/3 |
|---|---|---|---|
| Transport protocol | TCP | TCP | QUIC (over UDP) |
| Multiplexing | No (HOL blocking at app layer) | Yes (single TCP connection) | Yes (independent streams, no TCP-level HOL blocking) |
| Header compression | No | Yes (HPACK) | Yes (QPACK) |
| Connection setup speed | Slower (separate TCP + TLS handshakes) | Slower (separate TCP + TLS handshakes) | Faster (combined transport + TLS handshake) |
| Encryption | Optional (HTTP vs HTTPS) | Typically used with TLS (though not mandated by spec) | Mandatory (built into QUIC) |

## 7. WebSockets
- HTTP is fundamentally **request-response** — the server can't send data to the client unless the client first asks for it.
- **WebSockets** provide a **full-duplex, persistent connection** — after an initial HTTP "upgrade" handshake, the connection switches protocols, allowing **both client and server to send data to each other at any time**, without needing a new request for every message.
- Ideal for: **real-time applications** — live chat, notifications, collaborative editing, live sports scores, stock tickers.

```
Client                                  Server
   |----- HTTP GET (Upgrade: websocket) -->|
   |<---- HTTP 101 Switching Protocols ----|
   |                                       |
   |<========= WebSocket Connection =====>|
   |  (both sides can send messages        |
   |   to each other at ANY time now)      |
```

---

## Interview Questions & Answers

**Q1. Why is HTTP described as "stateless," and how do applications maintain state despite this?**
HTTP is stateless because each request is treated as completely independent by the server — the protocol itself has no built-in mechanism for a server to "remember" information from a client's previous request. Applications work around this using Cookies and Sessions (or tokens like JWT) — the client stores a small piece of identifying data (like a session ID) and sends it back with every subsequent request, allowing the server to look up and associate that request with the correct stored state.

**Q2. What is the difference between a "Safe" and an "Idempotent" HTTP method?**
A Safe method doesn't change server state at all — it's purely read-only (e.g., GET). An Idempotent method CAN change server state, but making the identical request multiple times produces the same end result as making it just once (e.g., PUT — setting a resource's value repeatedly still leaves it in that same final state). Notably, POST is neither safe nor idempotent, since resubmitting the same POST request could create duplicate resources/side-effects each time.

**Q3. What is the difference between a 401 and a 403 HTTP status code?**
401 Unauthorized means the client is NOT authenticated — the server doesn't know who's making the request (missing or invalid credentials). 403 Forbidden means the client IS authenticated (the server knows who they are), but that authenticated user simply doesn't have PERMISSION to access the specific requested resource.

**Q4. What is the difference between a Cookie and a Session?**
A Cookie is the actual small piece of data stored on the client's browser and automatically sent back with subsequent requests. A Session refers to the associated server-side storage of user-specific state/data — typically, the Cookie itself just holds a small, unique Session ID, while the actual meaningful data (user details, cart contents, etc.) is stored on the SERVER, keyed by that Session ID.

**Q5. What is Head-of-Line (HOL) Blocking, and how does it manifest differently in HTTP/1.1 versus HTTP/2?**
HOL Blocking is when one slow/blocked request or packet prevents other, independent requests/data from being processed, even though they have nothing to do with the actual delay. In HTTP/1.1, this happens at the APPLICATION layer — requests on the same TCP connection are processed strictly in order, so a slow response blocks all subsequent queued responses on that same connection. HTTP/2 solves this specific application-layer blocking through multiplexing, but still suffers from a subtler TRANSPORT-layer (TCP) HOL blocking — since it still runs over a single TCP connection, a single lost TCP packet blocks ALL the multiplexed HTTP/2 streams within that connection until it's retransmitted, even if those streams' own data arrived successfully.

**Q6. How does HTTP/3 solve the TCP-level Head-of-Line blocking problem that persists in HTTP/2?**
HTTP/3 runs over QUIC (built on UDP) instead of TCP. QUIC implements its own reliability and multiplexing mechanisms where multiple streams are handled INDEPENDENTLY — a lost packet affecting one particular stream only blocks/delays THAT specific stream, without blocking the other, unrelated streams sharing the same overall connection, unlike TCP's strict, connection-wide in-order delivery guarantee that HTTP/2 remains subject to.

**Q7. Why does HTTP/3 use UDP as its underlying transport, despite UDP traditionally being "unreliable"?**
HTTP/3 uses QUIC, which is built ON TOP of UDP but implements its OWN reliability, ordering, and congestion control mechanisms at the application/QUIC layer, rather than relying on UDP's inherent unreliability directly. This gives QUIC (and thus HTTP/3) the FLEXIBILITY to design reliability in a smarter, more granular way (with independent per-stream handling) than TCP's rigid, connection-wide approach allows — essentially reimplementing reliable, ordered delivery, but doing so in a way specifically designed to avoid TCP's Head-of-Line blocking limitation.

**Q8. What are WebSockets, and why are they needed alongside regular HTTP?**
WebSockets provide a full-duplex, persistent connection between client and server, established via an initial HTTP "upgrade" handshake, after which either side can send messages to the other AT ANY TIME, without needing to wait for a new request-response cycle. They're needed because regular HTTP is fundamentally request-response — a server can never proactively push data to a client without the client first explicitly asking — making WebSockets essential for real-time applications like live chat, notifications, or collaborative editing, where the server frequently needs to push updates to the client spontaneously.

**Q9. What is HTTP/2's Server Push feature, and why has it seen limited real-world adoption?**
Server Push allows a server to proactively send resources (like CSS or JS files) to the client alongside an initial HTML response, WITHOUT the client having to explicitly request those additional resources separately — the server anticipates what the client will need next. It's seen limited adoption partly because it's difficult for servers to reliably know exactly what a client actually needs (versus what it might already have cached), and pushing unnecessary resources can actually waste bandwidth — as a result, most major browsers have deprecated or removed support for HTTP/2 Server Push.

**Q10. Explain the resolution process (using a CNAME example) if www.example.com is a CNAME pointing to example.com, which has an A record.**
When resolving www.example.com, the DNS resolver first finds its CNAME record, which indicates that www.example.com is actually an alias for example.com — so the resolver must then perform an ADDITIONAL lookup for example.com itself. This second lookup finds example.com's A record, which contains the actual target IP address — this final IP address is what's ultimately returned to satisfy the original query for www.example.com.

**Q11. Why is header compression (HPACK in HTTP/2, QPACK in HTTP/3) an important improvement over HTTP/1.1?**
In HTTP/1.1, full, often very repetitive headers (like User-Agent, Cookies, Accept-Language) are sent in their entirety with EVERY single request, adding significant overhead, especially for websites making many small requests (like loading dozens of small images/assets). HPACK/QPACK use compression techniques (including maintaining a shared "dictionary" of previously-seen header values between client and server) to send only the DIFFERENCES or references to previously-transmitted headers, dramatically reducing this redundant overhead across many requests on the same connection.

**Q12. If you were debugging why a web page loads slowly despite the server responding quickly to each individual request, how might understanding HTTP/1.1 vs HTTP/2 vs HTTP/3 help diagnose the issue?**
If the site is still using HTTP/1.1, you might investigate whether Head-of-Line blocking (due to the strict in-order processing of requests on each connection) is causing later requests to wait unnecessarily behind earlier, slower ones — and check if the browser is being forced to open many parallel connections (with their own overhead) as a workaround. If it's already using HTTP/2, you might specifically investigate whether packet loss is triggering TCP-level Head-of-Line blocking, stalling ALL multiplexed streams on that connection — in which case, migrating to HTTP/3 (with QUIC's independent stream handling) could be a meaningful solution to explore, since it specifically targets and resolves that exact scenario.
