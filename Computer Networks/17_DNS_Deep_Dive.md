# 17: DNS Deep Dive

## 1. What is DNS?
- **DNS (Domain Name System)** translates **human-readable domain names** (e.g., `www.google.com`) into **IP addresses** (e.g., `142.250.190.78`) that computers use to actually locate and communicate with each other.
- Often called the **"phonebook of the Internet."**
- Operates primarily on **Port 53** — uses **UDP** for typical fast lookups (small queries/responses), but falls back to **TCP** for larger responses (e.g., zone transfers between DNS servers, or responses exceeding UDP's size limits).

## 2. DNS Hierarchy (Domain Name Structure)

```
                         "." (Root)
                        /    |    \
                    .com   .org   .net   ... (Top-Level Domains / TLDs)
                     |
              google.com  (Second-Level Domain)
                     |
              www.google.com  (Subdomain / Fully Qualified Domain Name)
```

- **Root:** The topmost level (represented by an implicit trailing dot, e.g., `www.google.com.`), managed by **Root Servers** (13 logical root server clusters worldwide).
- **TLD (Top-Level Domain):** `.com`, `.org`, `.net`, country-code TLDs like `.in`, `.uk`, managed by **TLD Servers**.
- **Second-Level Domain:** The actual organization's registered name, e.g., `google` in `google.com`.
- **Subdomain:** Further subdivisions, e.g., `www`, `mail`, `maps` in `www.google.com`, `mail.google.com`.

## 3. DNS Record Types

| Record Type | Purpose |
|---|---|
| **A** | Maps a domain name to an **IPv4** address |
| **AAAA** | Maps a domain name to an **IPv6** address |
| **CNAME** | **Canonical Name** — an alias, pointing one domain name to another domain name (not directly to an IP) |
| **MX** | **Mail Exchange** — specifies the mail server(s) responsible for receiving email for the domain, along with a priority value |
| **NS** | **Name Server** — specifies which DNS server(s) are authoritative for this domain |
| **TXT** | Holds arbitrary text data — often used for domain verification, SPF/email security records |
| **PTR** | **Pointer** — used for **Reverse DNS lookup** (IP address → domain name, the opposite of a standard A record lookup) |
| **SOA** | **Start of Authority** — contains administrative info about the domain's DNS zone (primary name server, admin contact, refresh timers) |

> **Extra — Worked example (common interview scenario):**
> `www.example.com` has a **CNAME** record pointing to `example.com`.
> `example.com` has an **A** record pointing to `93.184.216.34`.
> **Resolution process:** looking up `www.example.com` → resolver finds the CNAME, which says "actually look up `example.com` instead" → resolver then looks up `example.com`'s A record → gets `93.184.216.34` → this final IP is returned to the original requester.

## 4. Types of DNS Servers

| Server Type | Role |
|---|---|
| **DNS Resolver (Recursive Resolver)** | The "middleman" — typically run by your ISP or a public provider (e.g., Google's `8.8.8.8`, Cloudflare's `1.1.1.1`); receives your query and does all the work of tracking down the answer on your behalf. |
| **Root Server** | Knows where to find the TLD servers for a given TLD (`.com`, `.org`, etc.) |
| **TLD Server** | Knows where to find the Authoritative Name Server for domains under that specific TLD |
| **Authoritative Name Server** | Holds the ACTUAL DNS records for a specific domain — the final source of truth. |

## 5. DNS Resolution Process (Step by Step)

```
Your Computer                Recursive         Root         TLD          Authoritative
(wants www.example.com)      Resolver         Server      Server (.com)  Server (example.com)
      |                          |               |            |               |
      |--- "www.example.com?" -->|               |            |               |
      |                          |--- "who handles .com?" --->|               |
      |                          |<-- "ask TLD server X" ------|               |
      |                          |                             |              |
      |                          |--- "who handles example.com?" ------------>|
      |                          |<-- "ask TLD server for .com's info" -------|
      |                          |                                            |
      |                          |--- "what's the IP for www.example.com?" -->|
      |                          |<-- "93.184.216.34" -------------------------|
      |                          |
      |<--- "93.184.216.34" -----|
      |
   [Caches this result locally for future use, based on the record's TTL]
```

1. Your computer sends the query to a **Recursive Resolver** (usually configured via your ISP or manually, e.g., to `8.8.8.8`).
2. The Resolver checks its **cache** first — if it already knows the answer (from a recent previous lookup), it returns it **immediately**, skipping the entire process below.
3. If not cached, the Resolver queries a **Root Server**, which responds with a referral to the appropriate **TLD Server** (e.g., for `.com`).
4. The Resolver queries that **TLD Server**, which responds with a referral to the domain's **Authoritative Name Server**.
5. The Resolver queries the **Authoritative Name Server**, which finally returns the actual **DNS record** (e.g., the A record's IP address).
6. The Resolver returns this answer to your computer, and **caches** it locally for future queries (until the record's **TTL — Time To Live** expires).

> **Extra — Iterative vs Recursive DNS queries (a common interview distinction):**

| Aspect | Recursive Query | Iterative Query |
|---|---|---|
| Who does the work | The QUERIED server does ALL the follow-up work itself, and returns only the FINAL answer | The QUERIED server returns a REFERRAL (pointing to the next server to ask), and the CLIENT must follow up itself |
| Typical usage | Your computer → Recursive Resolver (resolver does the full work on your behalf) | Recursive Resolver → Root/TLD/Authoritative servers (resolver iterates through referrals itself) |

## 6. Why DNS Caching Matters
- DNS lookups (especially the full multi-step resolution process) add **latency** — caching at multiple levels (browser cache, OS cache, Resolver cache) dramatically speeds up repeated lookups for the same domain.
- **TTL (Time To Live):** Each DNS record specifies how long it can be safely cached before it should be re-fetched — a **balance** between reducing DNS server load/latency (longer TTL) and ensuring changes propagate quickly if the underlying IP changes (shorter TTL).

---

## Interview Questions & Answers

**Q1. What is DNS, and why is it needed?**
DNS (Domain Name System) translates human-readable domain names (like www.google.com) into the numerical IP addresses that computers actually use to locate and communicate with each other on a network. It's needed because domain names are far easier for humans to remember and use than raw IP addresses, while computers still require IP addresses to actually route and deliver network traffic.

**Q2. Explain the DNS hierarchy, from Root down to a specific subdomain.**
At the top is the Root (an implicit, unnamed level, managed by Root Servers). Below that are Top-Level Domains (TLDs) like .com, .org, or country codes like .in, managed by TLD Servers. Below that is the Second-Level Domain — the organization's actual registered name (like "google" in google.com). Below that can be further Subdomains (like "www" or "mail" in www.google.com or mail.google.com), which the organization itself manages.

**Q3. What is the difference between an A record and a CNAME record?**
An A record directly maps a domain name to an IPv4 address. A CNAME record, instead of pointing directly to an IP address, points to ANOTHER domain name (an alias) — when a CNAME is encountered during resolution, the resolver must then look up THAT target domain name's own record (which could itself be another CNAME, or finally an A record) to ultimately find the actual IP address.

**Q4. What is a PTR record, and how does it differ from a standard A record lookup?**
A PTR (Pointer) record is used for Reverse DNS lookup — going from an IP ADDRESS to find the associated DOMAIN NAME, which is the exact opposite direction of a standard A record lookup (which goes from domain name to IP address). Reverse lookups are often used for verification purposes, such as checking that an incoming mail server's IP address actually resolves back to its claimed domain name, as a basic anti-spam/anti-spoofing measure.

**Q5. What is an MX record used for?**
An MX (Mail Exchange) record specifies which mail server(s) are responsible for receiving email on behalf of a domain, along with a priority value (lower values indicate higher priority) — when another mail server needs to deliver an email to someone@example.com, it looks up example.com's MX record to determine exactly which server to actually connect to for delivering that mail.

**Q6. What is the role of a Recursive Resolver in the DNS resolution process?**
The Recursive Resolver acts as an intermediary that receives a DNS query from a client (like your computer) and takes on the responsibility of performing all the necessary follow-up queries (to Root, TLD, and Authoritative servers) on the client's behalf, ultimately returning just the final answer to the original client — sparing the client from needing to perform this entire multi-step lookup process itself.

**Q7. What is the difference between a Recursive query and an Iterative query in DNS?**
In a Recursive query, the server that receives the query does ALL the necessary follow-up work itself (querying other servers as needed) and returns only the final, complete answer to the original requester. In an Iterative query, the server that receives the query simply returns a REFERRAL (pointing to the next server that might have the answer), and it's up to the ORIGINAL REQUESTER to follow up and query that next server itself — this is typically how a Recursive Resolver interacts with Root, TLD, and Authoritative servers.

**Q8. What is an Authoritative Name Server, and how is it different from a Recursive Resolver?**
An Authoritative Name Server holds the ACTUAL, definitive DNS records for a specific domain — it's the final source of truth for that domain's information. A Recursive Resolver, by contrast, doesn't hold any domain's actual authoritative records itself — it acts purely as an intermediary/facilitator, tracking down and caching the answer by querying the appropriate chain of Root, TLD, and ultimately Authoritative servers on behalf of clients.

**Q9. What is TTL in the context of DNS records, and why does it matter?**
TTL (Time To Live) specifies how long a DNS record can be safely cached by resolvers/clients before it should be considered stale and re-fetched from the authoritative source. It matters because it represents a trade-off: a LONGER TTL reduces DNS query load and lookup latency (since cached answers can be reused for longer), while a SHORTER TTL ensures that if the underlying record actually changes (e.g., a website migrates to a new server IP), that change propagates to users more quickly, rather than users continuing to use a now-outdated cached answer.

**Q10. Why does DNS primarily use UDP instead of TCP, and when does it use TCP instead?**
DNS primarily uses UDP because most DNS queries and responses are small, and UDP's lower overhead (no connection setup/handshake) makes typical DNS lookups faster — an important consideration since DNS resolution often needs to happen quickly, before the "real" communication (like an HTTP request) can even begin. DNS falls back to using TCP specifically for larger responses that exceed UDP's practical size limitations (such as DNSSEC-signed responses, or zone transfers between DNS servers, which involve much larger amounts of data).

**Q11. Walk through what happens if a DNS record's IP address changes, from the perspective of caching and TTL.**
When a domain's IP address changes at the authoritative server, any client or resolver that has ALREADY cached the OLD record (based on its previous TTL) will continue using that old, now-incorrect IP address until that cached entry's TTL expires. Only after the TTL expires will the resolver perform a fresh lookup, retrieving the NEW, updated IP address from the authoritative server — this is why administrators planning a server migration will often deliberately LOWER a record's TTL in advance, to ensure any cached old records expire quickly once the change is actually made, minimizing the "propagation delay" users experience before seeing the new server.

**Q12. Why might a large organization run its own Authoritative Name Servers, rather than relying entirely on a third-party DNS provider?**
Running their own Authoritative Name Servers gives the organization direct, immediate control over their DNS records — allowing instant updates without depending on a third party's systems or processes, potentially better reliability/redundancy through their own infrastructure design, and can be important for very large organizations with complex, rapidly-changing internal DNS needs (like massive cloud service providers), where tight integration between their DNS infrastructure and their other internal systems (like automated server provisioning) provides significant operational advantages.
