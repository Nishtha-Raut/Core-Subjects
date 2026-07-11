# 19: Network Security & Cryptography

## 1. Symmetric vs Asymmetric Encryption

### Symmetric Encryption
- Uses the **SAME single key** for both **encryption and decryption**.
- **Fast**, efficient for encrypting large amounts of data.
- **Key distribution problem:** Both sender and receiver must somehow **securely share** this same secret key beforehand — if an attacker intercepts the key during this exchange, the entire scheme is compromised.
- **Examples:** AES (Advanced Encryption Standard), DES (older, now considered insecure).

```
Plaintext --[Encrypt with Key K]--> Ciphertext --[Decrypt with SAME Key K]--> Plaintext
```

### Asymmetric Encryption (Public Key Cryptography)
- Uses a **mathematically related PAIR of keys**: a **Public Key** (freely shared with everyone) and a **Private Key** (kept absolutely secret by its owner).
- Data encrypted with the **Public Key** can only be decrypted with the corresponding **Private Key** (and vice versa, used for digital signatures).
- **Slower** than symmetric encryption (more computationally intensive), but **solves the key distribution problem** — no need to secretly share anything beforehand, since the Public Key is meant to be public.
- **Examples:** RSA, ECC (Elliptic Curve Cryptography).

```
Sender encrypts with Receiver's PUBLIC key
             |
             v
        [Ciphertext]  (sent over the network, safe even if intercepted)
             |
             v
Receiver decrypts with THEIR OWN PRIVATE key (never shared, never sent)
```

> **Extra — Symmetric vs Asymmetric comparison table (very common interview question):**

| Aspect | Symmetric | Asymmetric |
|---|---|---|
| Keys used | One shared secret key | Public/Private key pair |
| Speed | Fast | Slower (more computation) |
| Key distribution | Problematic (must securely share) | Easier (public key can be shared openly) |
| Common use | Encrypting bulk data | Key exchange, digital signatures, initial handshake |
| Examples | AES, DES | RSA, ECC |

## 2. Why Real Systems Use BOTH (Hybrid Approach)
- In practice (e.g., TLS/HTTPS), systems use **asymmetric encryption** to securely exchange a **temporary symmetric key** (since asymmetric solves the key-distribution problem), and then switch to **symmetric encryption** for the actual, ongoing bulk data transfer (since it's much faster) — getting the **best of both worlds**.

## 3. Digital Signatures & Certificates

### Digital Signature
- Provides **authentication** (proof of who sent the data) and **integrity** (proof the data wasn't tampered with) — achieved using the **sender's Private Key**.
- **Process:** Sender computes a **hash** of the message, then **encrypts that hash using their own Private Key** — this encrypted hash is the "digital signature," attached to the message.
- **Verification:** Receiver decrypts the signature using the sender's **Public Key** (recovering the original hash), independently computes their own hash of the received message, and **compares the two hashes** — if they match, the message is verified as authentic and untampered.

```
SENDER:                                    RECEIVER:
  Message --[Hash]--> Hash1                  Received Message --[Hash]--> Hash2 (computed independently)
  Hash1 --[Encrypt w/ Sender's PRIVATE key]-->  Signature
                                             Signature --[Decrypt w/ Sender's PUBLIC key]--> Hash1

  Message + Signature ----(sent together)---->
                                             Compare: Hash1 == Hash2 ?
                                             YES -> Authentic & Untampered
                                             NO  -> Tampered or Fake
```

### Digital Certificates & CA (Certificate Authority)
- **Problem:** How do you know a **Public Key** genuinely belongs to the entity claiming to own it (and not an attacker impersonating them)?
- A **Digital Certificate** (issued by a trusted **Certificate Authority — CA**, like DigiCert, Let's Encrypt) binds a **Public Key** to a **verified identity** (e.g., a specific domain name), and is itself digitally signed by the CA.
- Your browser trusts a pre-installed list of **trusted Root CAs** — if a website's certificate is signed by one of these (directly or through a chain of trust), the browser trusts that the Public Key genuinely belongs to that website.

## 4. TLS/SSL Handshake (How HTTPS Establishes a Secure Connection)

```
Client                                        Server
   |------ ClientHello (supported ciphers) --->|
   |<----- ServerHello + Certificate -----------|  (server sends its Digital Certificate,
   |                                             |   containing its Public Key)
   |  [Client verifies certificate against       |
   |   trusted CA list]                          |
   |                                             |
   |--- Encrypted Pre-Master Secret ------------>|  (encrypted using server's PUBLIC key,
   |                                             |   from the certificate)
   |                                             |
   [Both sides now independently derive the same SYMMETRIC session key
    from this shared secret]
   |                                             |
   |<==== Encrypted communication using SYMMETRIC key (fast) ====>|
```

1. **ClientHello:** Client initiates, listing supported encryption algorithms (cipher suites).
2. **ServerHello + Certificate:** Server responds with its chosen cipher suite and its **Digital Certificate** (containing its Public Key).
3. **Certificate Verification:** Client checks the certificate's validity against its trusted CA list.
4. **Key Exchange:** Client generates a secret, encrypts it using the server's **Public Key** (asymmetric), and sends it — only the server's **Private Key** can decrypt this.
5. **Session Key Derivation:** Both sides now independently derive the same **symmetric session key** from this shared secret.
6. **Secure Communication:** All further data is encrypted using this fast **symmetric** key, for the remainder of the session.

> **Extra — TLS 1.3 improvement:** TLS 1.3 (the modern standard) streamlines this handshake significantly compared to older TLS versions, reducing it to typically just **1 round-trip** (or even **0-RTT** for resumed connections), improving connection setup speed — directly relevant to why HTTP/3's QUIC (which mandates TLS 1.3) achieves faster connection establishment than older HTTP versions.

## 5. Firewalls
- A **Firewall** monitors and controls incoming/outgoing network traffic based on predefined **security rules**, acting as a barrier between a trusted internal network and untrusted external networks (like the Internet).

| Type | How it Works |
|---|---|
| **Packet-Filtering Firewall** | Examines individual packets (source/destination IP, port, protocol) against a rule set — simple, fast, but no awareness of connection state/context. |
| **Stateful Firewall** | Tracks the state of active connections (e.g., "this is part of an already-established TCP session") — more intelligent, can make decisions based on connection context, not just individual packets in isolation. |
| **Application-Layer (Proxy) Firewall** | Inspects traffic at the Application layer (e.g., understanding actual HTTP requests) — can filter based on much more specific/granular criteria (like blocking specific URLs), but adds more processing overhead. |

## 6. VPN (Virtual Private Network)
- Creates a **secure, encrypted "tunnel"** over a public network (like the Internet), allowing a device to securely access a private network remotely, as if it were directly connected to it.
- Encrypts all traffic passing through the tunnel, protecting it from eavesdropping on the untrusted public network in between.
- **Common use cases:** Remote employees securely accessing company internal resources; masking a user's actual IP address/location for privacy.

## 7. Common Network Attacks

| Attack | Description |
|---|---|
| **Man-in-the-Middle (MITM)** | An attacker secretly intercepts (and potentially alters) communication between two parties who believe they're communicating directly with each other. |
| **DDoS (Distributed Denial of Service)** | Overwhelming a target server/network with a flood of traffic from MANY sources simultaneously, making it unavailable to legitimate users. |
| **Phishing** | Tricking a user into revealing sensitive information (passwords, credit card info) by impersonating a trustworthy entity, typically via deceptive emails/websites. |
| **DNS Spoofing (Cache Poisoning)** | Corrupting a DNS resolver's cache with false information, redirecting users to a malicious IP address instead of the legitimate one. |
| **ARP Spoofing** | Sending falsified ARP messages to associate an attacker's MAC address with a legitimate IP address on a local network, enabling traffic interception (a specific technique often used to carry out a MITM attack). |
| **SQL Injection** | *(Covered in detail in the DBMS notes)* — injecting malicious SQL through unsanitized user input. |

---

## Interview Questions & Answers

**Q1. What is the difference between Symmetric and Asymmetric encryption?**
Symmetric encryption uses a single, shared secret key for both encryption and decryption — it's fast but has a key distribution problem (both parties must securely share this key beforehand). Asymmetric encryption uses a mathematically related public/private key pair — data encrypted with the public key can only be decrypted with the corresponding private key, solving the key distribution problem (since the public key can be shared openly), but at the cost of significantly higher computational overhead, making it slower for large amounts of data.

**Q2. Why do real-world systems like HTTPS/TLS use BOTH symmetric and asymmetric encryption, rather than just one or the other?**
They use asymmetric encryption specifically for the INITIAL key exchange, since it solves the key distribution problem (allowing a secret to be securely shared without any prior secure channel). Once this initial secure exchange establishes a shared secret, both sides derive a SYMMETRIC session key from it and switch to symmetric encryption for the actual, ongoing bulk data transfer, since symmetric encryption is much faster — this hybrid approach gets the security benefits of asymmetric encryption's key exchange along with the performance benefits of symmetric encryption's speed.

**Q3. How does a Digital Signature provide both authentication and integrity?**
A Digital Signature is created by the sender computing a hash of their message and then encrypting that hash using their OWN private key. The receiver can decrypt this signature using the sender's PUBLIC key (proving AUTHENTICATION — only someone with the corresponding private key could have created a signature decryptable by this public key) and compare the decrypted hash against their own independently-computed hash of the received message (proving INTEGRITY — if the hashes match, the message wasn't altered in transit).

**Q4. What problem do Digital Certificates and Certificate Authorities (CAs) solve?**
They solve the problem of verifying that a given Public Key genuinely belongs to the entity claiming to own it, rather than to an attacker impersonating that entity. A trusted Certificate Authority verifies an entity's identity and issues a Digital Certificate binding their Public Key to that verified identity, digitally signed by the CA itself — browsers/clients trust a pre-installed list of Root CAs, allowing them to verify that a certificate (and thus the associated public key) is legitimate.

**Q5. Walk through the basic steps of a TLS handshake used to establish an HTTPS connection.**
The client sends a ClientHello listing supported encryption options; the server responds with a ServerHello and its Digital Certificate (containing its public key); the client verifies this certificate against its trusted CA list; the client then generates a secret, encrypts it using the server's public key (asymmetric encryption), and sends it to the server (which only the server's private key can decrypt); both sides then independently derive a shared symmetric session key from this exchanged secret, and switch to fast symmetric encryption for all subsequent communication in the session.

**Q6. What is the difference between a Packet-Filtering Firewall and a Stateful Firewall?**
A Packet-Filtering Firewall examines each individual packet in isolation (checking source/destination IP, port, protocol) against a static rule set, with no awareness of broader connection context. A Stateful Firewall tracks the state of active connections (e.g., recognizing that a packet is part of an already-established, legitimate TCP session), allowing it to make more intelligent filtering decisions based on that connection context, rather than evaluating every packet in complete isolation.

**Q7. What is a VPN, and what problem does it solve?**
A VPN (Virtual Private Network) creates a secure, encrypted tunnel over a public/untrusted network (like the Internet), allowing a device to securely communicate with a private network as if it were directly, physically connected to it. It solves the problem of needing secure remote access to private resources (like a company's internal network) over inherently insecure public infrastructure, by encrypting all traffic passing through the tunnel to protect it from eavesdropping.

**Q8. What is a Man-in-the-Middle (MITM) attack, and how might encryption (like HTTPS) help protect against it?**
A MITM attack occurs when an attacker secretly positions themselves between two communicating parties, intercepting (and potentially altering) their communication, while both parties believe they're still communicating directly and securely with each other. HTTPS/TLS helps protect against this because the Digital Certificate verification step ensures the client is genuinely communicating with the legitimate server (not an impersonating attacker), and the resulting encryption ensures that even if an attacker DOES intercept the traffic, they cannot read or meaningfully alter the encrypted content without possessing the correct decryption keys.

**Q9. What is DNS Spoofing (Cache Poisoning), and what is a potential consequence for users?**
DNS Spoofing involves corrupting a DNS resolver's cache with FALSE information, causing it to return an incorrect (often malicious) IP address for a legitimate domain name. A potential consequence is that users attempting to visit a legitimate website (like their bank's site) could be silently redirected to a fake, attacker-controlled website instead, potentially leading them to unknowingly enter sensitive credentials into a phishing site that looks identical to the real one.

**Q10. What is ARP Spoofing, and how does it typically enable a Man-in-the-Middle attack on a local network?**
ARP Spoofing involves an attacker sending falsified ARP messages that associate THEIR OWN MAC address with the IP address of a legitimate device (like the network's default gateway/router) on a local network. This tricks other devices on the network into sending their traffic (intended for the legitimate gateway) to the attacker's machine instead, allowing the attacker to intercept, inspect, and potentially modify that traffic before forwarding it on — a classic technique for establishing a local-network Man-in-the-Middle position.

**Q11. Why is a DDoS attack described as "Distributed," and how does this make it harder to defend against compared to a simple single-source attack?**
A DDoS (Distributed Denial of Service) attack is "distributed" because it originates from MANY different sources simultaneously (often a large network of compromised devices, called a "botnet"), rather than a single attacking machine. This makes it much harder to defend against than a simple single-source attack, since simply blocking traffic from one specific IP address is ineffective — the attack traffic is spread across potentially thousands or millions of different source IPs, making it much more difficult to distinguish and filter out malicious traffic from legitimate user traffic based on source alone.

**Q12. In the context of TLS, why does the initial key exchange step specifically need to use asymmetric encryption, rather than just using symmetric encryption from the very start?**
At the very beginning of a TLS handshake, the client and server have NO prior shared secret between them — they've potentially never communicated before, and are connecting over a public, potentially untrusted network. Symmetric encryption fundamentally REQUIRES both parties to already possess the SAME secret key, which creates a chicken-and-egg problem (how do you securely share that key in the first place, over an insecure channel?) — asymmetric encryption elegantly solves exactly this initial problem, since the server's public key can be shared completely openly (even over an insecure channel) without compromising security, allowing a secret to be securely established for the very first time between two parties with no prior relationship.
