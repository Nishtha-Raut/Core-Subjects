# 26: Common Networking Troubleshooting Scenarios

*(This final file focuses purely on practical, scenario-based questions — the kind interviewers use to test whether you can actually APPLY the concepts from all previous files, not just recite definitions.)*

## 1. The General Troubleshooting Approach (OSI Bottom-Up / Top-Down)
- A **structured, layer-by-layer approach** (using the OSI model as a mental checklist) is the hallmark of a strong troubleshooting answer in interviews.

```
Bottom-Up Approach (start at Physical, work up):
1. Physical:    Is the cable plugged in? Link lights on? WiFi signal present?
2. Data Link:   Is the MAC address resolving correctly (ARP)? VLAN correct?
3. Network:     Is the IP address/subnet configured correctly? Can you ping the gateway?
4. Transport:   Is the correct port open? Is a firewall blocking it?
5. Application: Is the specific service/application actually running and responding correctly?
```

- Alternatively, a **Top-Down** approach starts at the Application layer and works down — often faster if you have a strong hypothesis about where the issue likely is (e.g., "the app says 'connection refused,' so let's check if the service is even running first").

## 2. Common Diagnostic Tools & What They Reveal

| Tool | What It Tells You |
|---|---|
| `ping` | Basic reachability + round-trip latency (uses ICMP Echo) |
| `traceroute` / `tracert` | The path (hop-by-hop) packets take to a destination, and where delay/loss occurs along that path |
| `nslookup` / `dig` | DNS resolution results — what IP a domain resolves to, which DNS server answered |
| `netstat` / `ss` | Active network connections, listening ports on the local machine |
| `ipconfig` / `ifconfig` / `ip addr` | Local machine's IP configuration (address, subnet mask, gateway) |
| `arp -a` | Local ARP cache (IP-to-MAC mappings) |
| `telnet <host> <port>` / `nc -zv <host> <port>` | Tests whether a specific TCP port is open/reachable on a remote host |
| Wireshark / `tcpdump` | Packet-level capture and inspection — the deepest level of diagnostic detail |

## 3. Scenario-Based Questions & Answers

**Scenario 1: "A user reports they can't access a specific website, but other websites work fine. How would you troubleshoot this?"**

*Answer:* Start by checking if it's a DNS issue specific to that one site — try `nslookup problemsite.com` to see if it resolves correctly; if not, that's likely the culprit (DNS issue). If it DOES resolve, try pinging the resolved IP directly — if that fails, the issue may be with THAT specific server/service being down, or a firewall rule specifically blocking that destination. If ping succeeds but the website still doesn't load, try `telnet problemsite.com 443` (or 80) to check if the specific web port is actually reachable/open — if the port test fails while ping succeeds, this suggests a firewall is blocking that specific port, or the web service itself isn't running/listening on the server, even though the server itself is reachable at the network level.

**Scenario 2: "Your computer can access websites by IP address, but not by domain name. What's likely wrong?"**

*Answer:* This is a classic sign of a **DNS resolution problem**, since IP-based access working correctly confirms basic network connectivity (routing, physical layer, etc.) is fine — the issue is specifically isolated to translating domain names into IP addresses. Check the configured DNS server settings (`ipconfig /all` on Windows, or `cat /etc/resolv.conf` on Linux) — the configured DNS server might be unreachable, misconfigured, or down. Try manually querying a known-good public DNS server (`nslookup google.com 8.8.8.8`) — if that works, the issue is specifically with the originally configured (likely local/ISP) DNS server, not a broader network problem.

**Scenario 3: "Two computers are on the same physical LAN but can't ping each other, even though both have valid IP addresses. What could be wrong?"**

*Answer:* First, verify both devices are actually on the SAME subnet (matching Network ID, based on their IP addresses and subnet masks) — if they're on different subnets/VLANs, a router (or Layer 3 switch) would be needed to route between them, and simple Layer 2 connectivity wouldn't be sufficient. If they ARE on the same subnet, check if a local firewall (on either machine, like Windows Firewall) might be blocking ICMP Echo Requests specifically — some systems block `ping` by default even while allowing other traffic. Check the ARP cache (`arp -a`) on both machines — if one device isn't correctly resolving the other's MAC address (perhaps due to a stale/incorrect ARP entry), that would also prevent successful communication at the local network level.

**Scenario 4: "A web application works fine locally on the server (via `localhost`), but external users can't reach it. What's likely misconfigured?"**

*Answer:* This strongly suggests either a **Firewall** rule blocking the relevant port from external access (while allowing local `localhost` traffic, which often bypasses firewall rules entirely since it doesn't traverse the actual network interface), or the application itself might be configured to only LISTEN on the loopback address (`127.0.0.1`) rather than on `0.0.0.0` (all available network interfaces) — meaning it's only accepting connections that originate from the SAME machine, not from the actual external network interface that outside users would need to connect through. Checking with `netstat`/`ss` to see exactly which address/interface the application is actually bound to and listening on would help confirm this second possibility.

**Scenario 5: "Users are experiencing intermittent, seemingly random slowness when accessing a web application, though it's fast most of the time. How would you investigate?"**

*Answer:* This pattern often points toward either network CONGESTION (check for high Queuing Delay along the path using repeated `traceroute` runs at different times, correlating with the slow periods) or an overloaded backend SERVER (check server-side metrics like CPU/memory usage, and database query performance, during the reported slow periods) rather than a simple, consistently-broken configuration issue. It's also worth checking if the slowness correlates with specific times (suggesting a load/traffic pattern issue, potentially requiring investigation into Load Balancer distribution and whether backend servers are being evenly utilized) or specific geographic user locations (suggesting a routing/latency issue for certain regions, potentially benefiting from a CDN or additional server deployment closer to those users).

**Scenario 6: "A `traceroute` to a destination shows several hops with high latency in the middle of the path, but the final destination still responds relatively quickly overall. Should you be concerned?"**

*Answer:* Not necessarily overly concerned — some intermediate routers deliberately DEPRIORITIZE responding to the ICMP messages that `traceroute` relies on (since generating these diagnostic responses isn't their primary job, and some administrators intentionally rate-limit or deprioritize ICMP traffic for security/performance reasons), which can make specific INTERMEDIATE hops appear artificially slow in traceroute results, even though they're actually forwarding the ACTUAL data traffic (non-ICMP) perfectly fine and quickly. The key indicator to focus on is the FINAL destination's actual overall response time/behavior — if that remains fast and consistent, occasional high-latency-looking intermediate hops in a traceroute are often a red herring rather than a genuine problem requiring action.

**Scenario 7: "A network engineer reports a specific server is unreachable, but `ping` to it succeeds. How is this possible, and what would you check next?"**

*Answer:* This is possible because `ping` only tests basic Network-layer (ICMP) reachability — it doesn't verify that any specific APPLICATION/SERVICE on that server is actually running and accepting connections at the Transport layer. The next step would be to test the SPECIFIC PORT the application should be listening on, using something like `telnet <server_ip> <port>` or `nc -zv <server_ip> <port>` — if this port test fails while ping succeeds, it strongly suggests the specific application/service itself isn't running, has crashed, or a firewall is specifically blocking that particular port, even though the underlying server/network path itself is confirmed reachable.

**Scenario 8: "After a company relocates its web server to a new IP address and updates its DNS A record, some users still reach the OLD server for a while. Why, and how could this have been minimized in advance?"**

*Answer:* This happens due to DNS CACHING — various DNS resolvers (ISP resolvers, OS caches, browser caches) along the path may have already cached the OLD IP address based on the record's previous TTL value, and will continue serving that stale, cached answer until their respective cache entries naturally expire. This could have been minimized by proactively LOWERING the DNS record's TTL value WELL IN ADVANCE of the planned migration (giving existing cached entries time to expire on their normal, short schedule before the actual change), then performing the migration, and only restoring a longer, more typical TTL value AFTER the propagation period has passed and the migration is confirmed stable.

---

## Final Summary — Key Interview Takeaways Across All 26 Files

1. **Always structure answers by OSI layer** when discussing troubleshooting — it demonstrates systematic thinking.
2. **Know the "why," not just the "what"** — e.g., not just "TCP uses a 3-way handshake," but WHY it needs exactly 3 steps.
3. **Comparison tables are gold** — interviewers love clean, direct comparisons (TCP vs UDP, Symmetric vs Asymmetric, etc.) — you now have dozens of these memorized across this note set.
4. **Practice explaining "What happens when you type a URL"** out loud — it's the single most effective way to rehearse tying everything together coherently.
5. **Numericals matter** — subnetting, CRC/Hamming code, latency calculations, and Shannon/Nyquist problems are common in technical rounds; the worked examples throughout this note set are exactly this type of practice.

Good luck with your interviews! 🚀
