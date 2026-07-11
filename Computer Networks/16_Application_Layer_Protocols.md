# 16: Application Layer Protocols

*(HTTP and DNS get their own dedicated deep-dive files — LEC-17 and LEC-18 — since they're asked about so extensively. This file covers the other essential application-layer protocols.)*

## 1. FTP (File Transfer Protocol)
- Used to **transfer files** between a client and a server over a network.
- **Unusual design — uses TWO separate connections:**
  - **Control Connection (Port 21):** Stays open for the **duration of the session**, used to send commands (LOGIN, LIST, GET, PUT, etc.) and receive responses.
  - **Data Connection (Port 20, or dynamically negotiated):** Opened **separately, only when actual data** (a file, or a directory listing) needs to be transferred, then closed once that transfer completes.

```
Client                                    FTP Server
   |------ Control Connection (Port 21) ----->|  (stays open entire session)
   |         "LOGIN", "LIST", "GET file.txt"   |
   |                                            |
   |<===== Data Connection (Port 20) =========>|  (opened only for actual data transfer,
   |         (actual file bytes transferred)    |   closed after each transfer)
```

- **Active vs Passive Mode:** Differ in which side (client or server) initiates the Data Connection — Passive Mode is more commonly used today, since it works better through client-side firewalls/NAT (the client initiates both connections outward, rather than the server trying to connect back INTO the client).

## 2. SMTP (Simple Mail Transfer Protocol)
- Used to **send** email from a client to a mail server, and between mail servers, during relay/delivery.
- Operates on **Port 25** (standard), or **Port 587** (with authentication, commonly used by mail clients submitting outgoing mail).
- **Push protocol** — the sender actively pushes the email to the recipient's mail server.

> **Extra — SMTP vs POP3 vs IMAP (a very common interview trio):**

| Protocol | Purpose | Key Behavior |
|---|---|---|
| **SMTP** | SENDING email (client-to-server, and server-to-server relay) | Push protocol; "sends mail out" |
| **POP3** (Post Office Protocol v3) | RECEIVING/retrieving email | Downloads emails to the local device and (by default) DELETES them from the server — designed for single-device access |
| **IMAP** (Internet Message Access Protocol) | RECEIVING/retrieving email | Keeps emails synced ON the server; supports accessing/managing the SAME mailbox from MULTIPLE devices, with changes (read/unread, folders) reflected everywhere |

```
   Sender's           SMTP           Recipient's        POP3/IMAP        Recipient's
   Email Client -------------------> Mail Server -------------------->    Email Client
   (Outlook, Gmail app, etc.)    (receives & stores mail)          (retrieves the mail)
```

## 3. Telnet
- Provides **remote terminal access** to another device over a network — allows a user to log in and execute commands on a remote machine as if sitting directly at it.
- Operates on **Port 23**.
- **Critical security flaw:** Transmits ALL data, including **usernames and passwords, in plaintext** (completely unencrypted) — anyone able to intercept the traffic can read everything.
- **Effectively obsolete today**, almost entirely replaced by **SSH (Secure Shell)**, which provides the same remote-access functionality but with full encryption.

> **Extra — Telnet vs SSH comparison:**

| Aspect | Telnet | SSH |
|---|---|---|
| Port | 23 | 22 |
| Encryption | None (plaintext) | Fully encrypted |
| Security | Very insecure — credentials/data visible to anyone sniffing traffic | Secure — resistant to eavesdropping/interception |
| Modern usage | Rare, legacy/testing only | Standard for all secure remote administration |

## 4. DHCP (Dynamic Host Configuration Protocol)
- Automatically assigns **IP addresses** (and other network configuration — subnet mask, default gateway, DNS servers) to devices joining a network, eliminating the need for manual configuration.
- Operates using **Port 67 (server)** and **Port 68 (client)**.

### DHCP DORA Process (4-step handshake)

```
Client                                       DHCP Server
   |------ DHCP DISCOVER (broadcast) --------->|   "Is there a DHCP server? I need an IP!"
   |                                            |
   |<----- DHCP OFFER ------------------------ |   "Here's an available IP you can use: X.X.X.X"
   |                                            |
   |------ DHCP REQUEST (broadcast) ---------->|   "OK, I'd like to use that IP, please confirm"
   |                                            |
   |<----- DHCP ACK --------------------------- |   "Confirmed — that IP is now yours (with a lease time)"
```

1. **DISCOVER:** Client broadcasts, looking for any available DHCP server on the network.
2. **OFFER:** A DHCP server responds, offering an available IP address (and other config).
3. **REQUEST:** Client broadcasts a request, explicitly asking to use the OFFERED address (broadcasting this, rather than unicasting, also lets any OTHER DHCP servers that made offers know their offer wasn't accepted).
4. **ACK (Acknowledge):** The server confirms, formally assigning the IP address to the client for a specific **lease duration** (after which the client must renew).

> **Extra — DORA mnemonic:** **D**iscover, **O**ffer, **R**equest, **A**cknowledge.

## 5. SNMP (Simple Network Management Protocol)
- Used by network administrators to **monitor and manage** network devices (routers, switches, servers) — collecting statistics (bandwidth usage, error rates, uptime) and allowing remote configuration changes.
- Operates on **Port 161** (agent, for queries) and **Port 162** (manager, for unsolicited "trap" alert messages).
- **Key components:**
  - **Managed Devices:** The actual network devices being monitored.
  - **Agent:** Software running ON each managed device, that collects and reports data.
  - **Network Management Station (NMS)/Manager:** The central system that queries agents and collects/displays this monitoring data.
  - **MIB (Management Information Base):** A structured database defining exactly what information can be queried/monitored on a given device.

---

## Interview Questions & Answers

**Q1. Why does FTP use two separate connections (Control and Data), instead of just one?**
FTP separates the Control Connection (which stays open for the whole session, handling commands/responses) from the Data Connection (which is opened only when actual file data needs to be transferred, then closed afterward) to allow commands and control messages to be exchanged independently and continuously, without being blocked or delayed by large, potentially slow file transfers happening on a separate channel.

**Q2. What is the key difference between Active and Passive FTP modes?**
The modes differ in which side (client or server) initiates the separate Data Connection. In Active mode, the SERVER initiates a connection back to the client for data transfer — which often fails due to client-side firewalls/NAT blocking unsolicited incoming connections. In Passive mode, the CLIENT initiates both the control AND the data connections outward to the server, which works much more reliably through typical client-side firewall/NAT configurations, making Passive mode the more commonly used approach today.

**Q3. What is the difference between SMTP, POP3, and IMAP?**
SMTP is used for SENDING email (from a client to a server, and between mail servers during relay) — it's a "push" protocol. POP3 is used for RECEIVING/retrieving email, and typically downloads messages to a single local device, deleting them from the server by default — designed for single-device access. IMAP is also used for RECEIVING/retrieving email, but keeps messages synchronized ON the server, allowing the same mailbox to be accessed and consistently managed (read status, folders, etc.) across MULTIPLE devices simultaneously.

**Q4. Why would someone choose IMAP over POP3 for their email client configuration?**
IMAP is preferred when a user needs to access their email from multiple devices (e.g., phone, laptop, tablet) and wants the mailbox state (read/unread status, folder organization) to stay consistently synchronized across all of them, since IMAP keeps everything managed on the server. POP3 is more suitable only for simpler, single-device setups, since it typically downloads and removes messages from the server, meaning other devices wouldn't see that same mail.

**Q5. Why is Telnet considered insecure, and what has largely replaced it?**
Telnet is considered insecure because it transmits ALL data — including usernames, passwords, and the actual session content — in completely unencrypted PLAINTEXT, meaning anyone able to intercept the network traffic (e.g., via packet sniffing) can read everything, including login credentials. SSH (Secure Shell) has largely replaced Telnet, providing the same remote terminal access functionality but with full encryption, protecting against eavesdropping and interception.

**Q6. Walk through the DHCP DORA process step by step.**
DISCOVER: the client broadcasts a message looking for any available DHCP server on the network. OFFER: a DHCP server responds, offering an available IP address and other configuration details. REQUEST: the client broadcasts a message explicitly requesting to use the specific offered IP address (broadcasting this also informs any OTHER DHCP servers that might have made competing offers that their offer wasn't accepted). ACK: the server sends a final acknowledgment, formally confirming and assigning that IP address to the client for a specific lease duration.

**Q7. Why does the DHCP client broadcast its REQUEST message, rather than sending it directly (unicast) to the specific server whose offer it's accepting?**
Broadcasting the REQUEST serves a dual purpose: it confirms to the CHOSEN server that its offer was accepted, while simultaneously informing any OTHER DHCP servers on the network (that might have also sent competing OFFER messages) that their specific offers were NOT accepted, allowing those other servers to release/reclaim the IP addresses they had tentatively reserved for this client, making them available again for other devices.

**Q8. What happens when a DHCP lease expires, and why do leases have a limited duration in the first place?**
When a DHCP lease is nearing expiration, the client typically attempts to RENEW it (usually via a simpler, more direct request-acknowledge exchange, without needing to repeat the full DISCOVER-OFFER process) with the same DHCP server. Leases have a limited duration specifically to allow IP addresses to be reclaimed and reused if a device leaves the network (is powered off, disconnected, etc.) without properly notifying the DHCP server — ensuring the pool of available addresses doesn't get permanently exhausted by devices that are no longer actually present on the network.

**Q9. What is SNMP used for, and what are its main components?**
SNMP (Simple Network Management Protocol) is used by network administrators to monitor and remotely manage network devices like routers, switches, and servers — collecting performance statistics and allowing configuration changes. Its main components are: Managed Devices (the actual hardware being monitored), Agents (software running on each managed device that collects/reports data), the Network Management Station/Manager (the central system querying and collecting this data), and the MIB (Management Information Base, a structured definition of what data can be queried on a given device).

**Q10. What is the difference between SNMP's use of Port 161 versus Port 162?**
Port 161 is used by AGENTS (running on managed devices) to receive and respond to QUERIES from the management station — this is the standard "ask a question, get an answer" pattern. Port 162 is used by the MANAGER to receive unsolicited "TRAP" messages — alerts that an agent proactively sends WITHOUT being asked, typically to immediately notify the manager of a significant event (like a device going down or a critical threshold being exceeded), rather than waiting for the next scheduled query.

**Q11. Why might a company still occasionally use Telnet today, despite its known security flaws?**
Telnet might still occasionally be used in very specific, controlled scenarios — such as basic connectivity/port testing during network troubleshooting (since it's simple to establish a raw TCP connection with it for diagnostic purposes), or in legacy/isolated internal lab environments where the security risk of plaintext transmission is considered acceptable due to the network being fully isolated from any untrusted access. However, for any real remote administration or credential-involving access, SSH is now essentially universally required/preferred instead.

**Q12. If a mail client is configured to check email from multiple devices but the user notices that reading an email on their phone doesn't mark it as read on their laptop, what protocol misconfiguration might be the cause?**
This behavior strongly suggests the mail client(s) are configured to use POP3 rather than IMAP — since POP3 typically downloads mail to a single device without maintaining synchronized state on the server, actions like marking an email as "read" on one device wouldn't be reflected when checking mail from a different device. Switching the configuration to use IMAP instead would resolve this, since IMAP is specifically designed to keep mailbox state synchronized across multiple devices accessing the same account.
