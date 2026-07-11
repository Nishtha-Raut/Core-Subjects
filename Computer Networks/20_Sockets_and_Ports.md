# 20: Sockets & Ports

## 1. What is a Port?
- A **Port** is a **16-bit number** (ranging from **0 to 65535**) used at the **Transport Layer** to identify a **specific application/process** running on a device.
- While an **IP address** identifies a specific **device/host** on a network, a **Port** identifies a specific **service/application** running on that device — together, `IP:Port` uniquely identifies a specific communication endpoint.

```
       IP Address                    Port
   (identifies the DEVICE)    (identifies the APPLICATION)
   192.168.1.10          :          443
        |                             |
        v                             v
   "which computer               "which specific service
    on the network?"               on that computer?"
```

## 2. Port Ranges

| Range | Name | Usage |
|---|---|---|
| **0 – 1023** | **Well-Known Ports** | Reserved for standard, common services (HTTP, FTP, SSH, etc.) — require admin/root privileges to bind to on most OSes |
| **1024 – 49151** | **Registered Ports** | Assigned to specific applications by IANA upon request, but not as tightly restricted as well-known ports |
| **49152 – 65535** | **Dynamic/Private/Ephemeral Ports** | Used temporarily by client applications for outgoing connections (e.g., your browser's source port when connecting to a website) |

## 3. Common Well-Known Ports (memorize for interviews)

| Port | Protocol/Service |
|---|---|
| 20, 21 | FTP (Data, Control) |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 67, 68 | DHCP (Server, Client) |
| 80 | HTTP |
| 110 | POP3 |
| 143 | IMAP |
| 161, 162 | SNMP (Agent, Manager/Trap) |
| 443 | HTTPS |
| 3306 | MySQL |
| 5432 | PostgreSQL |

## 4. What is a Socket?
- A **Socket** is one **endpoint** of a two-way communication link between two programs running on a network — it's the actual programming **interface/abstraction** that applications use to send/receive data over the network.
- A socket is uniquely identified by the combination: **(Protocol, Local IP, Local Port, Remote IP, Remote Port)** — this 5-tuple uniquely identifies a specific network connection.

```
A single TCP connection is uniquely identified by this 5-tuple:

(TCP, 192.168.1.10, 54321, 93.184.216.34, 443)
  |         |          |          |         |
Protocol  Client IP  Client Port  Server IP  Server Port
                     (ephemeral)            (well-known)
```

> **Extra — Why can a server handle MANY simultaneous connections on the SAME port (e.g., port 443 for HTTPS)?** Because each individual connection is uniquely identified by the FULL 5-tuple, not just the server's port alone. Even though thousands of different clients might all be connecting to the SAME server IP and port (443), each client has a DIFFERENT client IP and/or a different ephemeral client port — making each of those 5-tuples unique, allowing the server's OS to correctly distinguish and route data for each individual connection separately, even though they all share the same destination IP:Port.

## 5. Socket Programming Basics (Client-Server Model)

```
        SERVER                              CLIENT
           |                                    |
      socket()                                  |
           |                                    |
       bind()  <- attach to a specific IP:Port   |
           |                                    |
      listen() <- wait for incoming connections  |
           |                                    |
      accept() <- blocks, waiting for a client   |
           |                                    |
           |                                socket()
           |                                    |
           |<---------- connect() --------------|  (client initiates connection
           |                                    |   to server's IP:Port)
           |                                    |
    [connection established — a NEW socket is created
     by accept() specifically for this client]
           |                                    |
        send()/recv()  <---------->  send()/recv()   (data exchange)
           |                                    |
        close()                              close()
```

### Key Socket API Calls (conceptual, language-agnostic)
1. **`socket()`:** Creates a new socket (an endpoint for communication).
2. **`bind()`:** Associates the socket with a specific local IP address and port number (typically done by the server).
3. **`listen()`:** Puts a server socket into a state where it's ready to accept incoming connection requests.
4. **`accept()`:** Blocks (waits) until an incoming connection request arrives, then creates a **new, separate socket** specifically for that individual client connection (the original listening socket continues waiting for further NEW connections).
5. **`connect()`:** Used by a client to initiate a connection to a server's specific IP:Port.
6. **`send()` / `recv()`:** Used to actually transmit and receive data over an established connection.
7. **`close()`:** Terminates the connection and releases the socket's resources.

> **Extra — Why does `accept()` create a NEW socket, rather than reusing the original listening socket?** This design allows the server to CONTINUE listening for and accepting NEW incoming connections on the original socket/port, WHILE simultaneously handling ongoing communication with already-connected clients via their own dedicated sockets — this is precisely what allows a single server (on a single port) to serve many simultaneous clients concurrently.

## 6. TCP Socket vs UDP Socket

| Aspect | TCP Socket (SOCK_STREAM) | UDP Socket (SOCK_DGRAM) |
|---|---|---|
| Connection | Requires `connect()`/`accept()` (connection-oriented) | No connection setup needed — just `sendto()`/`recvfrom()` |
| Reliability | Reliable, ordered | Unreliable, unordered |
| Typical use | Web servers, databases, file transfer | DNS, streaming, real-time gaming |

---

## Interview Questions & Answers

**Q1. What is the difference between an IP Address and a Port number, and why are both needed?**
An IP Address identifies a specific DEVICE/HOST on a network. A Port number identifies a specific APPLICATION/SERVICE running on that device. Both are needed together because a single device can run many different network applications/services simultaneously (a web server, an email server, an SSH service, etc.) — the IP address alone can get data to the right DEVICE, but the port number is needed to further direct that data to the correct SPECIFIC APPLICATION on that device.

**Q2. What is the range of Well-Known Ports, and why do they typically require administrator/root privileges to bind to?**
Well-Known Ports range from 0 to 1023, reserved for standard, widely-recognized services like HTTP (80), HTTPS (443), SSH (22), etc. They typically require administrator/root privileges to bind to as a security measure — preventing regular, unprivileged user-level applications from impersonating standard system services by binding to these well-known, expected port numbers.

**Q3. What is a Socket, and what uniquely identifies a specific TCP connection?**
A Socket is the programming interface/abstraction representing one endpoint of a network communication link, which applications use to send and receive data. A specific TCP connection is uniquely identified by a 5-tuple: (Protocol, Local IP, Local Port, Remote IP, Remote Port) — this combination ensures that even multiple connections sharing some of these values (like many clients connecting to the same server IP:Port) remain individually distinguishable.

**Q4. How can a web server handle thousands of simultaneous connections, all arriving on the same port (like port 443)?**
Because each individual TCP connection is identified by the FULL 5-tuple (Protocol, Local IP, Local Port, Remote IP, Remote Port), not just the server's listening port alone. Even though many different clients connect to the exact same server IP and port, each client has its own unique combination of client IP address and/or ephemeral client port number, making each resulting 5-tuple unique — allowing the server's operating system to correctly distinguish, route, and manage data for each individual client connection separately.

**Q5. What is the purpose of the `bind()` function in socket programming?**
`bind()` associates a socket with a specific local IP address and port number, essentially telling the operating system "this socket should be reachable at this specific address/port combination" — this is typically done by a server, so that clients know exactly where (which IP:Port) to connect to in order to reach that particular service.

**Q6. Why does the `accept()` function create a NEW socket for each incoming connection, rather than just using the original listening socket directly?**
Creating a new, dedicated socket for each accepted connection allows the SERVER to continue using its ORIGINAL listening socket to keep accepting FURTHER new incoming connections, while simultaneously handling ongoing, potentially long-running communication with each already-connected client through their own separate, dedicated sockets. This design is exactly what enables a single server process (listening on one port) to concurrently serve many different clients at the same time.

**Q7. What is the difference between using a TCP socket versus a UDP socket in socket programming?**
A TCP socket (SOCK_STREAM) is connection-oriented, requiring an explicit `connect()`/`accept()` handshake before data can be exchanged, and provides reliable, ordered delivery. A UDP socket (SOCK_DGRAM) is connectionless — data can simply be sent directly using functions like `sendto()`/`recvfrom()` without any prior connection setup, but with no guarantees of reliability or ordering, reflecting UDP's fundamentally different characteristics compared to TCP.

**Q8. What are Ephemeral (Dynamic/Private) ports, and when are they typically used?**
Ephemeral ports fall within the range 49152–65535, and are TEMPORARILY assigned by the operating system for OUTGOING client-side connections — for example, when your web browser connects to a website, your browser doesn't use a fixed, predetermined port; instead, the OS dynamically assigns it an available ephemeral port for that specific connection's LOCAL (client) side, which is released again once the connection closes.

**Q9. If you're debugging a "port already in use" error when trying to start a server application, what does this typically indicate?**
This typically indicates that another process/application on the same machine is already bound to (actively using) that specific port number — since only one process can `bind()` to a given IP:Port combination at a time (for a given protocol), attempting to start a second application trying to use that SAME already-occupied port will fail with this type of error, requiring you to either stop the conflicting process or configure your new application to use a different, available port instead.

**Q10. Why is UDP commonly used for DNS queries, from a socket programming perspective?**
DNS queries are typically small, simple request-response exchanges, where the overhead of TCP's connection setup (3-way handshake) for every single lookup would add unnecessary latency for what's usually just one quick round-trip of data. UDP sockets avoid this connection-setup overhead entirely (no `connect()`/`accept()` handshake needed), allowing a DNS query and its response to be exchanged with minimal latency — though DNS does fall back to using TCP sockets for larger responses that exceed UDP's practical size limitations.

**Q11. In the client-server socket model, why does the CLIENT typically not need to call `bind()` explicitly, while the SERVER does?**
The SERVER needs to `bind()` to a specific, well-known/predetermined IP:Port so that CLIENTS know exactly where to find and connect to that particular service — this address needs to be fixed and predictable. The CLIENT, on the other hand, doesn't need a fixed, known address for OTHERS to find it at — it just needs SOME available local port to use for its outgoing connection, so the operating system typically handles this automatically behind the scenes by assigning an available ephemeral port when the client calls `connect()`, without the client needing to explicitly request or manage this itself.

**Q12. How does understanding the 5-tuple (Protocol, Local IP, Local Port, Remote IP, Remote Port) help explain why you can have multiple browser tabs open to the SAME website simultaneously, each functioning independently?**
Even though all the tabs might be connecting to the exact same server IP address and port (e.g., the same website's HTTPS server on port 443), each individual browser tab's connection typically gets assigned its OWN unique ephemeral LOCAL port number by your operating system when it calls `connect()`. This means each tab's connection has a distinct 5-tuple (differing at least in the Local Port value), allowing your operating system (and the remote server) to treat and manage each tab's connection as a completely separate, independent socket/connection, even though they're all communicating with the identical remote destination.
