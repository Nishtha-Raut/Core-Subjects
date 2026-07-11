# 21: NAT, Proxy & Load Balancing

## 1. What is NAT (Network Address Translation)?
- **NAT** allows multiple devices on a **private network** (using private IP addresses) to share a **single public IP address** when communicating with the outside Internet.
- Solves two problems: **IPv4 address scarcity** (many private devices, one public IP) and provides a basic layer of **security/obscurity** (internal device IPs aren't directly exposed to the outside world).
- Typically performed by a **Router/Gateway** device at the boundary between the private network and the public Internet.

```
Private Network (using private IPs)         Public Internet
   PC-A (192.168.1.10) --\
   PC-B (192.168.1.11) ---+--> [NAT Router] --> Public IP: 203.0.113.5
   PC-C (192.168.1.12) --/       |
                            (translates private IP:port <-> public IP:port)
```

## 2. How NAT Works (NAT Translation Table)
- The NAT router maintains a **translation table**, mapping each internal `(Private IP, Private Port)` combination to a unique `(Public IP, Public Port)` combination, for outgoing traffic — and does the reverse mapping for the return traffic.

| Internal (Private) | External (Public) |
|---|---|
| 192.168.1.10 : 5000 | 203.0.113.5 : 40001 |
| 192.168.1.11 : 5000 | 203.0.113.5 : 40002 |

- When a response comes back to `203.0.113.5:40001`, the router looks up its table and knows to forward it internally to `192.168.1.10:5000`.

## 3. Types of NAT

| Type | Description |
|---|---|
| **Static NAT** | A **fixed, one-to-one** mapping between one private IP and one public IP — always the same. Used when a specific internal server needs to be consistently reachable from outside. |
| **Dynamic NAT** | Maps a private IP to any available public IP from a **pool** of public IPs — the specific mapping isn't fixed, and depends on which public IP was free at the time. |
| **PAT (Port Address Translation)** / **NAT Overload** | The most common type — maps **MANY private IPs to a SINGLE public IP**, distinguishing between them using different PORT numbers (as shown in the table above). This is what most home/office routers use. |

> **Extra — PAT is the type used by almost every home router.** It's specifically why an entire household (with potentially dozens of devices) can share just one public IP address from their ISP — PAT keeps track of which internal device+port corresponds to which external port, using this port-based distinction as the "many-to-one" translation mechanism.

## 4. Forward Proxy vs Reverse Proxy

### Forward Proxy
- Sits **in front of CLIENTS**, forwarding their requests to external servers on their behalf — the **destination server** typically doesn't know the original client's real identity (only sees the proxy).
- **Use cases:** Bypassing content restrictions/geo-blocking, corporate content filtering/monitoring, client anonymity, caching frequently-accessed external content for a group of internal users.

```
Client A --\
Client B ---+--> [Forward Proxy] --> Internet (external servers)
Client C --/       (hides clients FROM the destination server)
```

### Reverse Proxy
- Sits **in front of SERVERS**, receiving requests from external clients and forwarding them to the appropriate internal (backend) server — the **client** typically doesn't know which specific backend server actually handled their request (only sees the reverse proxy).
- **Use cases:** Load balancing across multiple backend servers, SSL/TLS termination (handling encryption/decryption centrally), caching, hiding/protecting internal server infrastructure details, providing a single unified entry point for multiple backend services.

```
Internet (external clients) --> [Reverse Proxy] --\
                                                     +--> Backend Server 1
                                                     +--> Backend Server 2
                                                     +--> Backend Server 3
                          (hides backend servers FROM the client)
```

> **Extra — Forward vs Reverse Proxy comparison table (very common interview question):**

| Aspect | Forward Proxy | Reverse Proxy |
|---|---|---|
| Positioned in front of | Clients | Servers |
| Hides identity of | Clients (from the destination server) | Servers (from the client) |
| Typical use case | Client anonymity, content filtering, bypassing restrictions | Load balancing, SSL termination, protecting backend infrastructure |
| Who configures it | Usually the CLIENT side's organization/network | Usually the SERVER side's organization |

## 5. Load Balancing
- Distributes incoming network traffic **across multiple backend servers**, ensuring no single server becomes overwhelmed, improving overall **availability, reliability, and performance**.

### Common Load Balancing Algorithms

| Algorithm | How it Works |
|---|---|
| **Round Robin** | Distributes requests to servers in strict, sequential rotating order (Server1, Server2, Server3, Server1, ...) |
| **Least Connections** | Sends each new request to whichever server currently has the FEWEST active connections — better accounts for servers that might be handling longer-running requests |
| **IP Hash** | Uses a hash of the CLIENT's IP address to consistently determine which server handles that client's requests — ensures the SAME client is (usually) always routed to the SAME server (useful for maintaining session state without needing shared session storage) |
| **Weighted Round Robin** | Like Round Robin, but servers with a higher assigned "weight" (e.g., more powerful hardware) receive a proportionally larger share of the requests |

```
                        [Load Balancer]
                       /      |       \
                      /       |        \
              Server 1    Server 2    Server 3
             (33% traffic)(33% traffic)(33% traffic)   <- e.g., Round Robin distribution
```

## 6. CDN (Content Delivery Network)
- A **globally distributed network of servers** that cache and serve content (images, videos, static files) from a location **geographically close to the end user**, rather than always fetching from a single, potentially distant, origin server.
- **Benefits:** Dramatically reduced latency (content served from nearby "edge" servers), reduced load on the origin server, improved resilience against traffic spikes/DDoS (traffic is absorbed and distributed across many CDN edge locations).

---

## Interview Questions & Answers

**Q1. What problem does NAT solve, and why was it so critical for the growth of the Internet?**
NAT allows multiple devices on a private network to share a single public IP address when communicating externally, which directly addressed IPv4 address scarcity — since IPv4 only provides about 4.3 billion addresses, NAT allowed the massive proliferation of devices (far exceeding available public IPv4 addresses) to still access the Internet by sharing a comparatively small number of public IPs among many private devices.

**Q2. What is the difference between Static NAT, Dynamic NAT, and PAT?**
Static NAT creates a fixed, permanent one-to-one mapping between a specific private IP and a specific public IP. Dynamic NAT maps private IPs to available public IPs from a shared POOL, without a fixed, permanent assignment. PAT (Port Address Translation), the most commonly used type, maps MANY private IPs to a SINGLE public IP simultaneously, distinguishing between different internal devices' traffic using different port numbers on the shared public IP.

**Q3. How does a NAT router know which internal device to forward a returning response to, since many devices might share the same public IP?**
The NAT router maintains a translation table that records the mapping between each internal device's (Private IP, Private Port) and the corresponding (Public IP, Public Port) it was assigned for that specific outgoing connection. When a response arrives at that specific public IP:Port combination, the router looks up this table to determine exactly which internal device/port the response should be forwarded back to.

**Q4. What is the fundamental difference between a Forward Proxy and a Reverse Proxy?**
A Forward Proxy sits in front of CLIENTS, forwarding their outgoing requests to external servers and hiding the clients' identity FROM those destination servers — commonly used for client anonymity or content filtering. A Reverse Proxy sits in front of SERVERS, receiving incoming client requests and forwarding them to the appropriate backend server, hiding the backend servers' identity/details FROM the clients — commonly used for load balancing and protecting internal infrastructure.

**Q5. Give three practical use cases for a Reverse Proxy.**
Load balancing (distributing incoming requests across multiple backend servers), SSL/TLS termination (handling encryption/decryption centrally at the proxy, so backend servers don't each need to manage certificates individually), and hiding/protecting internal server infrastructure details (clients only ever interact with the reverse proxy, never directly with the actual backend servers, reducing the exposed attack surface).

**Q6. What is Load Balancing, and why is it important for scalable web applications?**
Load Balancing distributes incoming network traffic across multiple backend servers, rather than directing all traffic to a single server. It's important for scalable applications because it prevents any single server from becoming a bottleneck or single point of failure, allowing an application to handle much higher traffic volumes by horizontally adding more backend servers, while also improving overall availability (if one server fails, the load balancer can redirect traffic to the remaining healthy servers).

**Q7. What is the difference between the Round Robin and Least Connections load balancing algorithms?**
Round Robin distributes incoming requests to backend servers in a strict, sequential rotating order, regardless of each server's current actual load. Least Connections instead sends each new request to whichever server currently has the FEWEST active connections at that moment — this can be more effective when requests vary significantly in how long they take to process, since Round Robin alone might send new requests to an already-busy server just because it's "next in line," even if that server is handling several long-running requests already.

**Q8. Why might IP Hash be used as a load balancing algorithm instead of Round Robin?**
IP Hash uses a hash of the CLIENT's IP address to consistently determine which specific backend server handles that client's requests, ensuring the SAME client is (usually) always routed to the SAME server across multiple requests. This "session affinity" or "sticky sessions" behavior is useful when a backend server maintains some client-specific state locally (like an in-memory session), since it ensures a given client's subsequent requests consistently reach the server that already has their relevant state, rather than potentially being routed elsewhere by Round Robin's indifferent rotation.

**Q9. What is a CDN, and how does it improve website performance for users in different geographic locations?**
A CDN (Content Delivery Network) is a globally distributed network of "edge" servers that cache and serve website content from locations geographically close to each individual end user, rather than every user having to fetch content from one single, potentially distant, origin server. This dramatically reduces latency for users far from the origin server (since data now only has to travel a much shorter distance to a nearby edge server), while also reducing overall load on the origin server itself, since much of the traffic is now served directly from the distributed CDN edge locations instead.

**Q10. How does a Reverse Proxy performing SSL/TLS termination simplify backend server management?**
Without SSL termination at the proxy, EVERY individual backend server would need to independently manage its own SSL/TLS certificates and handle the computational overhead of encryption/decryption for every request. By having the Reverse Proxy handle SSL/TLS termination centrally (decrypting incoming HTTPS traffic once, then forwarding plain HTTP internally to backend servers, often over a trusted internal network), certificate management and encryption overhead is consolidated in ONE place, significantly simplifying configuration/maintenance and reducing computational burden across potentially many individual backend servers.

**Q11. Why might a company use BOTH a Forward Proxy for their internal employees AND a Reverse Proxy in front of their public-facing servers?**
These serve entirely different purposes at different points in the network — the Forward Proxy would be used to control/monitor/filter OUTGOING traffic from internal employees browsing the external Internet (e.g., blocking inappropriate sites, logging usage for compliance). The Reverse Proxy, completely separately, would be used to manage INCOMING traffic from external clients/customers trying to reach the company's own public-facing web services (handling load balancing, SSL termination, and hiding internal server details) — both proxies work together but address distinct traffic flows (outbound employee traffic vs. inbound customer traffic).

**Q12. If a load-balanced web application experiences an issue where users randomly get logged out or lose their shopping cart contents, how might understanding load balancing algorithms help diagnose this?**
This symptom often suggests the application relies on SERVER-SIDE session state stored locally on each individual backend server (rather than a centralized/shared session store), COMBINED with a load balancing algorithm like Round Robin that doesn't guarantee a given user's requests always reach the SAME backend server. If a user's subsequent request gets routed to a DIFFERENT server than the one that originally created their session, that new server wouldn't have their session data, causing them to appear logged out. Solutions could include switching to an IP Hash (sticky session) load balancing algorithm to ensure session consistency, or better yet, refactoring the application to use a centralized, shared session store (like Redis) that ALL backend servers can access, regardless of which one handles a given request.
