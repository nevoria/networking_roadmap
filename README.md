# 🌐 Networking Mastery Roadmap — Zero to Expert

> **Goal:** Understand, edit, rework, and design networking in real applications — from the first cable to cloud-scale infrastructure.

---

## How to Use This Roadmap

- Work through phases **in order** — each one builds on the last.
- Mark items `[x]` as you complete them.
- Every phase has a **Milestone Project** — build it before moving on.
- Don't memorize. **Build things. Break things. Fix things.**

---

## Phase 0: Before You Write a Single Line of Code 🧱
> *Absolute zero. If you already know what an IP address is and how to ping something, skim this.*

### The Mental Model
- [ ] What is a "network"? Two computers sharing data — that's it.
- [ ] Client vs. Server: who asks, who answers.
- [ ] What is a protocol? An agreed-upon language between two machines.
- [ ] Packets: why data is broken into chunks, not sent as one big blob.
- [ ] Latency vs. Bandwidth: the difference between speed and capacity (like a highway vs. a pipe).

### Your First Tools (No Code Yet)
- [ ] `ping` — check if a host is reachable, measure round-trip time.
- [ ] `traceroute` / `tracert` — see every router ("hop") between you and a destination.
- [ ] `nslookup` / `dig` — ask a DNS server what IP a domain maps to.
- [ ] `ifconfig` / `ipconfig` / `ip addr` — inspect your own machine's network interfaces.
- [ ] `netstat` / `ss` — see open connections and listening ports on your machine.
- [ ] `curl` — make raw HTTP requests from the command line.

### The OSI Model (Your Map of Everything)
- [ ] Why the OSI model exists: layered abstraction, each layer solves one problem.
- [ ] Layer 1 — Physical: cables, signals, bits.
- [ ] Layer 2 — Data Link: MAC addresses, frames, switches.
- [ ] Layer 3 — Network: IP addresses, routing, packets.
- [ ] Layer 4 — Transport: TCP/UDP, ports, reliability.
- [ ] Layer 5–6 — Session & Presentation: largely absorbed by modern protocols.
- [ ] Layer 7 — Application: HTTP, DNS, TLS — what your code talks to.
- [ ] Encapsulation: how each layer wraps the one above it (headers added going down, stripped going up).

### Binary & Hexadecimal Basics
- [ ] Binary: counting in base 2 (0s and 1s). Why computers use it.
- [ ] Converting decimal ↔ binary by hand for small numbers (e.g., 192 = `11000000`).
- [ ] Hexadecimal: base 16, used for MAC addresses and memory. `FF` = 255.

### 🎯 Milestone: Setup & Explore
> Run `ping google.com`, `traceroute google.com`, and `dig google.com` from your terminal. Write down what every line of output means. You should understand every number.

---

## Phase 1: Core Packet Mechanics & Routing 🌐
> *How data actually travels from A to B across the internet.*

### Physical & Data Link Layers
- [ ] **MAC Addresses:** 48-bit hardware address burned into every NIC. OUI (first 3 bytes = manufacturer) and NIC-specific (last 3 bytes).
- [ ] **Frames vs. Packets:** frames live at Layer 2 (between hops), packets live at Layer 3 (end-to-end).
- [ ] **ARP (Address Resolution Protocol):** how your machine learns the MAC address for a given IP. Broadcast request → unicast reply → cached in ARP table.
- [ ] **ARP Poisoning:** attack where a malicious host sends fake ARP replies to redirect traffic. The basis for man-in-the-middle on local networks.
- [ ] **Switches:** learn MAC addresses by watching traffic (MAC learning). Store in CAM (Content Addressable Memory) tables. Forward unicast frames only to the correct port; flood unknowns.
- [ ] **Broadcast Domains vs. Collision Domains:** switches break collision domains; routers break broadcast domains.

### Network Layer (IP)
- [ ] **IPv4:** 32-bit address. 4 octets (`192.168.1.1`). ~4.3 billion addresses total — exhausted.
- [ ] **IPv6:** 128-bit address. 8 groups of 4 hex digits. Solves exhaustion, built-in security.
- [ ] **IPv4 Packet Header:** Version, IHL, TTL, Protocol (6=TCP, 17=UDP), Source IP, Destination IP, Checksum. Know what each field does.
- [ ] **TTL (Time to Live):** decremented by each router. Hits 0 → packet dropped + ICMP "Time Exceeded" sent back. Prevents infinite loops.
- [ ] **Fragmentation:** if a packet is larger than the MTU (usually 1500 bytes on Ethernet), routers break it into fragments. Reassembled at the destination. IP flags: DF (Don't Fragment), MF (More Fragments), Fragment Offset.
- [ ] **ICMP:** the "control" protocol. Used for ping (Echo Request/Reply) and error messages. Not TCP or UDP.

### Subnetting & CIDR
- [ ] **Binary Math for IPs:** convert each octet to 8 bits. `192.168.1.1` = `11000000.10101000.00000001.00000001`.
- [ ] **Subnet Mask:** a 32-bit mask where 1s = network bits, 0s = host bits. `/24` = `255.255.255.0` = first 24 bits are network.
- [ ] **CIDR Notation:** `192.168.1.0/24` — the `/24` tells you how many bits are the network portion.
- [ ] **Calculating from any CIDR:**
  - Network ID: IP AND mask.
  - Broadcast ID: Network ID with all host bits set to 1.
  - Usable hosts: 2^(host bits) - 2.
  - First host: Network ID + 1. Last host: Broadcast - 1.
- [ ] **VLSM (Variable Length Subnet Masking):** carving one large network into unequal subnets. Real-world ISPs and enterprise networks use this constantly.
- [ ] **Private IP Ranges:** `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16` — never routed on the public internet.

### Routing Fundamentals
- [ ] **The Routing Table:** every router and every OS has one. Maps destination networks to next-hop IPs and outgoing interfaces.
- [ ] **Routing Metrics:** cost of a path (hop count, bandwidth, delay). Lower is preferred.
- [ ] **Static Routes:** manually configured. `ip route add 10.0.0.0/8 via 192.168.1.1`.
- [ ] **Default Route:** `0.0.0.0/0` — the "if nothing else matches, go here" route. Points to your ISP's router.
- [ ] **Dynamic Routing Protocols:**
  - **Distance-Vector (e.g., RIP):** each router tells neighbors its full routing table. Simple, slow to converge.
  - **Link-State (e.g., OSPF):** each router knows the full topology. Builds a map, runs Dijkstra's shortest-path. Fast convergence.
  - **BGP:** the internet's routing protocol. How ISPs exchange routes. Not an interior protocol — you don't configure it in your app, but you interact with its results every day.
- [ ] **Longest Prefix Match:** when multiple routes match a destination, the most specific one (longest prefix) wins.

### NAT & Gateways
- [ ] **Why NAT Exists:** private IPs can't be routed on the public internet. NAT translates them.
- [ ] **SNAT (Source NAT):** outgoing traffic. Your private IP → replaced with the router's public IP. Return traffic mapped back.
- [ ] **DNAT (Destination NAT):** incoming traffic. Rewrites the destination IP. Used for port forwarding.
- [ ] **PAT (Port Address Translation) / Masquerade:** the most common form. One public IP + thousands of ports = thousands of simultaneous connections. Your home router does this.
- [ ] **NAT Table:** the router maintains a state table mapping `{private_IP:private_port}` ↔ `{public_IP:public_port}`. Essential for understanding firewall rules.
- [ ] **NAT Traversal:** the reason peer-to-peer apps (games, video calls) need special techniques (STUN, TURN, ICE) to punch through NAT.

### 🎯 Milestone: Build a Subnet Calculator
> Write a CLI tool in any language that takes an IP and CIDR (e.g., `192.168.50.0/22`) and outputs: Network ID, Broadcast, first/last usable host, and total usable hosts. Then diagram what PAT looks like for 3 devices behind one router.

---

## Phase 2: Transport & Session Control 📦
> *How TCP makes the internet reliable, and when to throw that reliability away.*

### TCP Deep Dive
- [ ] **The 3-Way Handshake:**
  1. **SYN:** client picks a random ISN (Initial Sequence Number), sends SYN.
  2. **SYN-ACK:** server picks its own ISN, acknowledges client's ISN+1.
  3. **ACK:** client acknowledges server's ISN+1. Connection established.
  - Why random ISNs? Prevents sequence number prediction attacks.
- [ ] **Reliability Mechanism:**
  - Every byte is numbered (Sequence Number).
  - Receiver sends ACKs (cumulative: "I've received everything up to byte N").
  - Sender starts a retransmission timer. No ACK before timeout → retransmit.
  - Duplicate ACKs signal packet loss before timeout (fast retransmit).
- [ ] **The 4-Way Teardown:** FIN → ACK → FIN → ACK. Either side can initiate. Each direction closes independently.
- [ ] **TCP State Machine — the key states:**
  - `LISTEN` — server waiting for connections.
  - `SYN_SENT` — client sent SYN, waiting.
  - `ESTABLISHED` — data can flow.
  - `CLOSE_WAIT` — received FIN, waiting for app to close.
  - `TIME_WAIT` — sent final ACK, waiting 2×MSL before releasing port. Prevents old packets arriving on a new connection.
  - `FIN_WAIT_2` — sent FIN, waiting for server's FIN.
- [ ] **Head-of-Line Blocking:** in TCP, a lost packet blocks ALL subsequent data on that stream, even if later data arrived. HTTP/2 over TCP still suffers from this at the transport layer.

### Congestion Control
- [ ] **Two Windows:**
  - **Receive Window (rwnd):** how much data the receiver's buffer can hold. Prevents overwhelming the receiver.
  - **Congestion Window (cwnd):** how much data the sender can have in flight. Prevents overwhelming the network.
  - **Effective window = min(rwnd, cwnd).**
- [ ] **Slow Start:** begin with `cwnd = 1 MSS`. Double every RTT until threshold. Why? You don't know the network's capacity yet.
- [ ] **Congestion Avoidance:** after threshold, increase linearly (additive increase). On packet loss, cut in half (multiplicative decrease) — AIMD.
- [ ] **TCP Reno vs. CUBIC vs. BBR:**
  - **Reno:** classic AIMD. Assumes packet loss = congestion.
  - **CUBIC:** loss-based, faster recovery on high-bandwidth links (default in Linux).
  - **BBR (Bottleneck Bandwidth and RTT):** model-based, measures actual bandwidth and RTT. Better on high-latency links. Used by Google.

### UDP Mechanics
- [ ] **The 8-Byte Header:** Source Port, Destination Port, Length, Checksum. That's it. No connection state, no ordering, no retransmission.
- [ ] **Statelessness:** each datagram is independent. The OS doesn't track UDP connections.
- [ ] **Why UDP exists:**
  - DNS lookups: one packet out, one packet back. TCP handshake overhead is pointless.
  - Real-time games: old data is worse than no data. Lag > packet loss.
  - Video/audio streaming: a dropped frame is better than a paused one.
  - QUIC (HTTP/3): implements its own reliability on top of UDP, avoiding TCP's kernel-level limitations.
- [ ] **UDP "Reliability":** if you need ordering or retransmission over UDP (like a game), you implement it yourself in userspace. This is exactly what QUIC does.

### Sockets API
- [ ] **What a Socket Is:** a file descriptor that represents one end of a network connection. The OS abstraction over the network stack.
- [ ] **TCP Server Flow:** `socket()` → `bind()` → `listen()` → `accept()` → `read()/write()` → `close()`
- [ ] **TCP Client Flow:** `socket()` → `connect()` → `read()/write()` → `close()`
- [ ] **UDP Flow:** `socket()` → `bind()` (server) → `recvfrom()/sendto()` (no connect needed)
- [ ] **Socket Families:** `AF_INET` (IPv4), `AF_INET6` (IPv6), `AF_UNIX` (Unix domain sockets — same machine only, no TCP overhead).
- [ ] **Socket Types:** `SOCK_STREAM` (TCP — connection-oriented), `SOCK_DGRAM` (UDP — connectionless).
- [ ] **`SO_REUSEADDR` / `SO_REUSEPORT`:** critical socket options. `SO_REUSEADDR` lets you restart a server without waiting for `TIME_WAIT`. `SO_REUSEPORT` lets multiple processes share a port (for multi-core servers).
- [ ] **Ports:** 0–1023 = well-known (root required). 1024–49151 = registered. 49152–65535 = ephemeral (OS assigns to clients).

### File Descriptors (FDs)
- [ ] **Unix Philosophy:** everything is a file. A socket, a pipe, a regular file — all represented as integers (FDs) in the process's FD table.
- [ ] **FD Table:** per-process. Kernel keeps the actual socket state. Multiple FDs can point to the same socket (after `fork()`).
- [ ] **`read()` vs `recv()`:** `read()` is the generic file call; `recv()` is socket-specific and supports flags like `MSG_PEEK` (read without consuming) and `MSG_WAITALL`.
- [ ] **FD Limits:** `ulimit -n` controls max open FDs per process. On a busy server with 10,000 connections, you need to raise this. Default is often 1024.
- [ ] **`select()`:** the original I/O multiplexer. Pass three FD sets (read/write/except). Returns which FDs are ready. Problem: O(n) scan, 1024 FD limit.

### 🎯 Milestone: Build a TCP Echo Server & Client
> In Python or C, build a server that accepts connections and echoes back whatever the client sends. Then extend it: handle multiple clients using `select()`. Check with `netstat` or `ss` that your connections are in `ESTABLISHED` state. Observe `TIME_WAIT` after closing.

---

## Phase 3: Application Protocols & Structuring Bytes 🗣️
> *How applications agree on what the bytes mean.*

### DNS Architecture
- [ ] **What DNS Does:** maps human-readable names (`example.com`) to IP addresses. The internet's distributed phone book.
- [ ] **Recursive vs. Iterative Resolution:**
  - **Recursive:** your resolver does all the work for you (asks root → TLD → authoritative, returns the answer).
  - **Iterative:** each server just refers you to the next one.
  - Your OS uses a recursive resolver (your ISP's or `8.8.8.8`).
- [ ] **Resolution Hierarchy:** Root servers (13 logical roots, `a.root-servers.net`) → TLD servers (`.com`, `.net`, `.io`) → Authoritative servers (the actual records for `example.com`).
- [ ] **DNS Records:**
  - `A` — domain → IPv4 address.
  - `AAAA` — domain → IPv6 address.
  - `CNAME` — alias → another domain name.
  - `MX` — mail server for a domain.
  - `TXT` — arbitrary text. Used for SPF, DKIM, domain verification.
  - `NS` — nameservers for a domain.
  - `PTR` — reverse DNS (IP → domain).
- [ ] **TTL on DNS Records:** how long resolvers cache the answer. Low TTL = fast propagation changes, high DNS server load. High TTL = slower updates, less load.
- [ ] **DNS over UDP vs. TCP:** queries use UDP (fast). Responses > 512 bytes fall back to TCP. DNS over HTTPS (DoH) and DNS over TLS (DoT) encrypt DNS traffic.
- [ ] **DNS as a Single Point of Failure:** understanding why CDNs use Anycast and multiple authoritative servers.

### HTTP Evolution
- [ ] **HTTP/1.0:** new TCP connection for every request/response. Terrible performance.
- [ ] **HTTP/1.1:** `Keep-Alive` — reuse the TCP connection. But requests must be sequential on one connection (pipelining is broken in practice).
- [ ] **HTTP/1.1 Request Structure:**
  ```
  GET /path HTTP/1.1
  Host: example.com
  User-Agent: curl/7.68.0
  Accept: */*
  ```
  Response: Status line + headers + blank line + body.
- [ ] **HTTP/2:** binary framing (not text). Multiplexing: multiple requests in parallel over ONE TCP connection using streams. Header compression (HPACK). Server Push. Still suffers from TCP-level head-of-line blocking.
- [ ] **HTTP/3:** replaces TCP with QUIC (built on UDP). Streams are independent — one lost packet doesn't block others. Faster handshake (0-RTT). TLS 1.3 built in.
- [ ] **Status Codes you must know:** 200 OK, 201 Created, 204 No Content, 301 Moved Permanently, 302 Found, 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 409 Conflict, 429 Too Many Requests, 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable, 504 Gateway Timeout.
- [ ] **Important Headers:** `Content-Type`, `Content-Length`, `Authorization`, `Cache-Control`, `ETag`, `Location`, `X-Forwarded-For`, `Transfer-Encoding: chunked`.

### WebSockets & SSE
- [ ] **The Problem They Solve:** HTTP is request-response. The client always initiates. What if the server needs to push data?
- [ ] **Server-Sent Events (SSE):** HTTP response that never ends. Server sends `data: ...\n\n` events. Unidirectional (server → client). Works over plain HTTP, easy to implement, auto-reconnects.
- [ ] **WebSockets:** bidirectional, full-duplex channel. Starts as HTTP, then upgrades:
  - Client sends `Upgrade: websocket` header + `Sec-WebSocket-Key`.
  - Server responds `101 Switching Protocols`.
  - Both sides can now send frames at any time.
- [ ] **WebSocket Frames:** opcode (text/binary/ping/pong/close), payload length, masking (client→server frames are always masked to prevent cache poisoning).
- [ ] **When to use which:** SSE for dashboards, live feeds, log streaming. WebSockets for chat, collaborative editing, multiplayer games.

### TLS/SSL Encryption
- [ ] **Why TLS:** HTTP is plaintext. TLS adds: encryption (nobody can read it), authentication (you're talking to who you think), integrity (data wasn't tampered with).
- [ ] **Symmetric Encryption:** same key encrypts and decrypts. Fast (AES-256-GCM). Problem: how do two strangers agree on a key securely?
- [ ] **Asymmetric Encryption:** public key + private key pair. Data encrypted with public key can only be decrypted with private key. Slow. Used for key exchange, not bulk data.
- [ ] **The TLS 1.3 Handshake:**
  1. **ClientHello:** supported cipher suites, TLS version, random number, key share (for ECDHE).
  2. **ServerHello:** chosen cipher, server's key share, certificate.
  3. Both sides derive the symmetric session key independently (via ECDHE — no key is ever transmitted).
  4. **Finished:** both sides confirm the handshake. Encrypted from here.
- [ ] **Certificates:** contain the server's public key + identity, signed by a Certificate Authority (CA). Your OS/browser trusts a list of root CAs. Chain of trust: Root CA → Intermediate CA → Your Certificate.
- [ ] **mTLS (Mutual TLS):** both client AND server present certificates. Used for service-to-service auth inside clusters.
- [ ] **Certificate Transparency (CT):** all issued certs are logged publicly. Prevents rogue CAs from issuing certs for domains they don't own.
- [ ] **HSTS (HTTP Strict Transport Security):** tells browsers to never use HTTP for this domain. Prevents downgrade attacks.

### RPC & gRPC
- [ ] **What RPC Is:** calling a function on a remote machine as if it were local. The network is abstracted away.
- [ ] **REST vs. RPC:** REST is resource-oriented (nouns: `/users/123`), RPC is action-oriented (verbs: `GetUser(123)`). Both are valid; teams choose based on use case.
- [ ] **Protocol Buffers (Protobuf):** binary serialization format. Define a `.proto` schema, generate code in any language. Smaller and faster than JSON.
  ```proto
  message User {
    int32 id = 1;
    string name = 2;
  }
  ```
- [ ] **gRPC:** Google's RPC framework. Uses Protobuf + HTTP/2. Supports unary (req-res), server streaming, client streaming, bidirectional streaming.
- [ ] **Why gRPC over REST for internal services:** strongly typed contracts, automatic code generation, streaming built-in, smaller payloads.

### 🎯 Milestone: HTTP from Scratch
> Using only raw TCP sockets (no HTTP library), write a program that: (1) connects to a real website, (2) sends a valid HTTP/1.1 GET request manually, (3) parses the response status code and headers, (4) prints the body. Then, sniff the TLS handshake of any HTTPS connection in Wireshark — identify the ClientHello and ServerHello packets.

---

## Phase 4: High-Performance App Networking 🚀
> *How to write servers that handle thousands of connections without collapsing.*

### Blocking vs. Non-Blocking I/O
- [ ] **Blocking I/O (Default):** `read()` doesn't return until data arrives. Thread sleeps. Simple to code, but one thread per connection = runs out of threads at scale.
- [ ] **Thread-Per-Connection Model:** works fine up to ~1000 connections. Beyond that: too much memory (each thread = ~8MB stack), too much context switching CPU overhead.
- [ ] **Non-Blocking I/O:** `read()` returns immediately with `EAGAIN`/`EWOULDBLOCK` if no data. Your code must loop and retry. More complex but doesn't block the thread.
- [ ] **Setting Non-Blocking:** `fcntl(fd, F_SETFL, O_NONBLOCK)`. Or pass `SOCK_NONBLOCK` to `socket()`.
- [ ] **The C10K Problem:** in 1999, Dan Kegel posed the question: how do you handle 10,000 simultaneous connections? The answer led to epoll, event loops, and modern async frameworks.

### I/O Multiplexing (epoll)
- [ ] **The Problem with `select()`:** you pass all FDs to the kernel on every call. Kernel scans all of them. O(n) — terrible at scale. Also limited to 1024 FDs.
- [ ] **`poll()`:** no FD limit, but still O(n) scan. Better, not great.
- [ ] **`epoll` (Linux):** the real solution.
  - `epoll_create1()` — create an epoll instance (returns an FD).
  - `epoll_ctl(epfd, EPOLL_CTL_ADD, fd, &event)` — register an FD to watch.
  - `epoll_wait(epfd, events, maxevents, timeout)` — block until FDs are ready. Returns ONLY ready FDs.
  - **O(1) per notification.** Internally uses a Red-Black tree for registered FDs and a ready-list for events.
- [ ] **`kqueue` (macOS/BSD):** equivalent to epoll on BSD systems. Used by Node.js on macOS.
- [ ] **IOCP (Windows):** the Windows equivalent. Completion-based (proactor pattern).

### Trigger Modes
- [ ] **Level-Triggered (LT) — Default:**
  - epoll notifies you repeatedly as long as data remains in the buffer.
  - Safe: if you only partially read, you'll be notified again.
  - Fine for most applications.
- [ ] **Edge-Triggered (ET):**
  - epoll notifies you ONCE when the FD's state changes (e.g., data arrives).
  - You MUST read until `EAGAIN` on every notification — if you don't, you might never be notified again.
  - Requires `while (read(...) > 0)` loops.
  - Higher performance (fewer syscalls), but easy to write subtle bugs.
  - Nginx uses ET mode for maximum throughput.

### Asynchronous Patterns
- [ ] **The Reactor Pattern (event-driven, notification-based):**
  - Single event loop calls epoll_wait.
  - When an FD is ready, dispatch the corresponding handler.
  - Handler must be non-blocking (if it blocks, the entire server stalls).
  - Used by: Node.js, Nginx, Redis, Python asyncio.
- [ ] **The Proactor Pattern (completion-based):**
  - You submit an async I/O operation to the OS.
  - The OS does the I/O.
  - OS notifies you when it's DONE (not just "ready").
  - Used by: Windows IOCP, `io_uring` on Linux (newer), Boost.Asio.
- [ ] **`io_uring` (Linux 5.1+):** the modern Linux async I/O. Shared ring buffers between kernel and userspace. Near-zero syscall overhead. Increasingly used in high-performance servers.
- [ ] **Coroutines & Green Threads:** async/await in Python, Go goroutines, Rust async — all are user-space cooperative threading built on event loops. The language runtime handles scheduling, so you write sequential-looking code that's actually async under the hood.

### Framework Implementation
- [ ] **Python asyncio:**
  - `asyncio.get_event_loop()` wraps epoll (Linux) or kqueue (macOS).
  - `async def` functions are coroutines — they yield control at `await` points.
  - `asyncio.create_task()` schedules a coroutine on the event loop.
  - The event loop calls `epoll_wait`, then resumes the right coroutine.
  - `uvloop` replaces the default event loop with a libuv-based one — 2–4× faster.
- [ ] **C++ Boost.Asio:**
  - `io_context` is the event loop.
  - All async operations take a completion handler (callback or coroutine).
  - `strand` ensures handlers for a given connection run serially — thread-safe without mutexes.
  - Supports stackful coroutines (`asio::spawn`) and C++20 coroutines (`co_await`).
- [ ] **Node.js:** single-threaded event loop (libuv) + worker pool for CPU-bound tasks. `worker_threads` for true parallelism.
- [ ] **Go:** goroutines are multiplexed over OS threads by the Go runtime. I/O is async under the hood; `Read()`/`Write()` calls look synchronous but don't block the OS thread.

### 🎯 Milestone: Event-Loop HTTP Server
> In C or Python, write an HTTP server that handles 1000 simultaneous connections using epoll (C) or asyncio (Python). Benchmark with `wrk` or `hey`. Observe: how does throughput change as concurrency rises? Where does it break?

---

## Phase 5: Modern Architecture & Infrastructure 🏗️
> *Production-scale networking: how real systems are built.*

### Load Balancing
- [ ] **Why Load Balancing:** one server can't handle all traffic. Distribute across a fleet.
- [ ] **Layer 4 (Transport) Load Balancing:**
  - Sees: source/destination IP and port, TCP flags.
  - Routes TCP connections. Doesn't look at HTTP content.
  - Very fast, low overhead. Example: HAProxy in TCP mode, AWS NLB.
- [ ] **Layer 7 (Application) Load Balancing:**
  - Sees: HTTP headers, URL paths, cookies, method.
  - Route `/api/*` to one backend, `/static/*` to another.
  - Can do content-based routing, A/B testing, canary deployments.
  - Example: Nginx, HAProxy HTTP mode, AWS ALB.
- [ ] **Load Balancing Algorithms:**
  - **Round Robin:** request 1 → server A, request 2 → server B, request 3 → server C, repeat.
  - **Weighted Round Robin:** server A gets 3×, server B gets 1×. For heterogeneous hardware.
  - **Least Connections:** route new connections to the server with fewest active connections. Better for varying request duration.
  - **IP Hashing:** hash the client IP → always route to the same server. Needed for non-sticky sessions.
  - **Random:** simple, works well with many servers.
- [ ] **Health Checks:** load balancer pings backends. If a backend fails, remove it from rotation. Passive (watch for errors) vs. active (periodic probes).
- [ ] **Connection Draining:** when removing a server, don't kill existing connections — wait for them to finish.
- [ ] **Sticky Sessions:** route a client to the same backend using a cookie. Needed for legacy apps with local session state (prefer stateless backends instead).

### Reverse Proxies
- [ ] **What a Reverse Proxy Does:** sits in front of your backend. Clients talk to the proxy; the proxy talks to backends.
- [ ] **Benefits:** TLS termination, load balancing, caching, compression, rate limiting, authentication.
- [ ] **TLS Termination:** the proxy decrypts TLS, forwards plain HTTP to backends (inside your trusted network). Simpler cert management — only the proxy needs a cert.
- [ ] **`X-Forwarded-For`:** added by the proxy so your backend knows the real client IP (not the proxy's IP).
- [ ] **`X-Real-IP`, `Forwarded` header:** alternatives to `X-Forwarded-For`.
- [ ] **Nginx configuration:** `proxy_pass`, `upstream` blocks, `proxy_set_header`, `worker_processes`, `worker_connections`.
- [ ] **Caching at the proxy layer:** Nginx/Varnish can cache GET responses. `Cache-Control: public, max-age=3600` tells the proxy what it can cache.

### Container Networking
- [ ] **Linux Namespaces:** the kernel feature that makes containers possible. Network namespaces give each container its own isolated network stack — interfaces, routing table, iptables rules.
- [ ] **veth Pairs:** virtual ethernet cable. One end in the container's namespace, other end in the host (or a bridge). Packets go in one end, come out the other.
- [ ] **Linux Bridge (`docker0`):** Docker's default network mode. Connects all containers on a host via a virtual switch. The bridge has an IP (gateway for containers).
- [ ] **iptables / nftables:** Docker and Kubernetes use iptables rules to handle NAT (containers → internet) and port forwarding (host port → container port).
- [ ] **Overlay Networks (Flannel, Calico, Cilium):** in Kubernetes, pods on different nodes need to communicate. Overlay networks create a virtual L2 network on top of the physical L3 network using VXLAN or similar encapsulation.
- [ ] **CNI (Container Network Interface):** the standard plugin interface Kubernetes uses to plug in networking implementations.
- [ ] **eBPF (Cilium):** the modern approach. Programs run in the Linux kernel, replacing iptables for networking. Much faster and more programmable.

### Service Meshes
- [ ] **The Problem:** in microservices, every service talks to every other service. Implementing mTLS, retries, timeouts, circuit breaking, and observability in every service is duplicated work.
- [ ] **Sidecar Proxy:** deploy an Envoy proxy alongside every app instance. All network traffic goes through the sidecar. The app doesn't change.
- [ ] **mTLS Between Services:** every service gets a certificate. Traffic between services is encrypted and mutually authenticated automatically.
- [ ] **The Control Plane (Istio, Linkerd):** centrally configures all the sidecars. Push routing rules, retry policies, and certificates without restarting apps.
- [ ] **Observability:** the mesh automatically collects metrics (latency, error rate, throughput), distributed traces (Jaeger), and access logs for every service-to-service call.
- [ ] **Circuit Breaking:** if service B starts failing, stop routing requests to it and return an error immediately (instead of waiting for timeouts). Prevents cascading failures.

### Network Analysis & Debugging
- [ ] **Wireshark:**
  - Capture on any interface. Filter with display filters (`tcp.port == 80`, `http`, `dns`).
  - Follow a TCP stream: right-click → "Follow TCP Stream" — see the full conversation.
  - Analyze the TLS handshake, see exact packet timing, diagnose retransmissions.
  - Decode protocols: Wireshark knows hundreds of protocol formats.
- [ ] **tcpdump:**
  - `tcpdump -i eth0 port 80` — capture HTTP traffic.
  - `tcpdump -w capture.pcap` — save to file, open in Wireshark later.
  - BPF syntax: `host 1.2.3.4`, `src port 443`, `tcp and dst port 8080`.
  - `-n` flag: don't resolve hostnames (faster, less noise).
- [ ] **strace:**
  - `strace -p <pid>` — attach to running process.
  - See every syscall: `socket()`, `connect()`, `read()`, `write()`, `epoll_wait()`.
  - Invaluable for understanding what your app actually does vs. what you think it does.
  - `-e trace=network` to filter only network syscalls.
- [ ] **`ss` (Socket Statistics):**
  - `ss -tnp` — all TCP sockets with PID and name.
  - `ss -s` — summary statistics.
  - Much faster than `netstat`.
- [ ] **`iptraf-ng` / `nethogs` / `iftop`:** real-time traffic monitoring tools.
- [ ] **MTR (Matt's Traceroute):** combines ping + traceroute. Shows per-hop latency and packet loss in real time. Essential for diagnosing network path issues.

### 🎯 Milestone: Dissect a Real App's Network Traffic
> Pick any app you use (a game, a web app, an API client). Capture its traffic with Wireshark or tcpdump. Identify: the TLS handshake, DNS queries, HTTP/2 streams or WebSocket frames. Then, using only what you captured, reconstruct what the app is "asking for" at the network level.

---

## Phase 6: Security & Hardening 🔒
> *What goes wrong — and how to stop it.*

### Network Attack Surface
- [ ] **Port Scanning (Nmap):** `nmap -sV -p 1-65535 <host>`. Understand what you're exposing. Every open port is a potential entry point.
- [ ] **Firewall Rules (iptables/nftables/ufw):** default-deny. Only allow what you explicitly need. `INPUT`, `OUTPUT`, `FORWARD` chains.
- [ ] **DDoS Types:** volumetric (flood the pipe), protocol (SYN flood, amplification), application (HTTP floods). Understanding each → knowing the mitigation.
- [ ] **SYN Flood:** attacker sends SYN packets with spoofed IPs. Server allocates resources for each half-open connection. Mitigated with SYN cookies (server encodes state in the SYN-ACK, allocates nothing until ACK).
- [ ] **Amplification Attacks:** DNS, NTP, and Memcached can be abused as amplifiers. Small request → huge response sent to victim's IP. UDP makes source IP spoofing easy.
- [ ] **Rate Limiting:** at the application layer (tokens per IP per second), at the proxy (Nginx `limit_req`), and at the CDN layer.

### Common Application Vulnerabilities (Network-Level)
- [ ] **SSRF (Server-Side Request Forgery):** app fetches a URL from user input. Attacker passes internal service URLs. Always validate URLs before fetching. Block access to `169.254.169.254` (AWS metadata service).
- [ ] **DNS Rebinding:** attacker controls a domain. First DNS response: real IP. After browser caches expire: resolves to internal IP. Browser same-origin policy bypass.
- [ ] **Man-in-the-Middle:** intercept traffic between client and server. Mitigated by TLS + certificate pinning.
- [ ] **HTTP Request Smuggling:** ambiguity in how front-end proxies and backend servers parse `Content-Length` vs. `Transfer-Encoding`. Can bypass security controls, poison caches.
- [ ] **Timing Attacks:** measuring how long an operation takes reveals secrets (e.g., is this API key valid?). Always use constant-time comparison for secrets.

### 🎯 Milestone: Harden Your Server
> Take your Phase 4 HTTP server. Add: (1) iptables rules to only allow ports 22, 80, 443. (2) Rate limiting (max 100 req/s per IP). (3) Fail2ban to block IPs after repeated failed connections. Use `nmap` to verify your attack surface before and after.

---

## Phase 7: Expert Level — Performance, Observability & Design 🧠
> *The stuff that separates engineers who understand networking from those who just use it.*

### Kernel Bypass & Zero-Copy
- [ ] **The Cost of the Kernel Network Stack:** every packet: NIC → kernel space → userspace. Multiple copies, multiple context switches.
- [ ] **Zero-Copy I/O:** `sendfile()` sends a file directly from kernel buffer to NIC. No userspace copy. Used by Nginx for static file serving.
- [ ] **`splice()`:** moves data between kernel buffers (pipe, socket) without userspace. For proxying: read from one socket → splice to another.
- [ ] **DPDK (Data Plane Development Kit):** bypass the kernel entirely. App owns the NIC, runs in userspace, polls for packets. Sub-100μs latency. Used in telecom and trading.
- [ ] **io_uring:** Linux 5.1+. Async I/O with shared ring buffers. Single syscall can submit batches of operations. The future of high-performance Linux networking.
- [ ] **XDP (eXpress Data Path):** run eBPF programs at the NIC driver level, before even the kernel network stack. Drop DDoS packets in nanoseconds.

### Observability
- [ ] **The Three Pillars:** Metrics (quantitative: latency p99, error rate), Logs (structured events), Traces (distributed request journey).
- [ ] **RED Method:** Rate, Errors, Duration — the three metrics that matter for a service.
- [ ] **USE Method:** Utilization, Saturation, Errors — for resources (CPU, NIC, disk).
- [ ] **Distributed Tracing:** each request gets a trace ID. Propagated via HTTP headers (`traceparent`, `X-B3-TraceId`). Jaeger/Zipkin/Tempo collect spans.
- [ ] **Prometheus + Grafana:** export metrics (connections, request latency, error rate). Scrape with Prometheus. Visualize with Grafana. Alert on anomalies.
- [ ] **Connection Pool Monitoring:** watch pool exhaustion, wait times, connection errors. Saturated pools cause cascading failures.
- [ ] **Latency Percentiles:** p50, p95, p99, p99.9. Averages lie. A p99 of 2s means 1 in 100 users waits 2 seconds.

### Designing Network Architectures
- [ ] **Stateless Backends:** don't store session state in memory. Use Redis, JWT, or cookies. Enables horizontal scaling and any-server load balancing.
- [ ] **Connection Pooling:** don't open a new TCP connection (and TLS handshake) for every request. Reuse connections. Libraries: `pgbouncer` for PostgreSQL, `http.Client` in Go, connection pool in any HTTP client.
- [ ] **Backpressure:** if a downstream service is slow, don't let the queue grow unbounded. Reject or throttle upstream requests. Prevents out-of-memory crashes.
- [ ] **Bulkhead Pattern:** isolate thread pools / connection pools per dependency. If service B's pool is exhausted, it doesn't affect service C's pool.
- [ ] **Timeouts Everywhere:** connect timeout, read timeout, write timeout, total request timeout. No timeout = potential hang forever. Set them at every layer.
- [ ] **Retry Strategy:** retry on transient errors. Use exponential backoff with jitter. Never retry non-idempotent requests without care.
- [ ] **CDN (Content Delivery Network):** push static assets to edge nodes globally. Reduce latency by serving from a location close to the user.
- [ ] **Anycast:** one IP address, multiple servers worldwide. BGP routes clients to the nearest one. Used by Cloudflare, Google (8.8.8.8).
- [ ] **DNS-Based Load Balancing:** return different IPs based on client geography or server health. Simple, but clients cache DNS and ignore TTLs.
- [ ] **API Gateway Pattern:** single entry point for all clients. Handles auth, rate limiting, routing, protocol translation. Example: Kong, AWS API Gateway.
- [ ] **East-West vs. North-South Traffic:** North-South = client ↔ your system (controlled by load balancer). East-West = service ↔ service inside your system (controlled by service mesh).

### 🎯 Final Milestone: Design & Build a Production-Ready System
> Design a system with: (1) Nginx as TLS-terminating reverse proxy. (2) 3 backend application servers behind a load balancer. (3) Redis for session state (stateless backends). (4) Prometheus metrics exposed from each backend. (5) A health check endpoint the load balancer uses. Document every networking decision: why L7 load balancing, why mTLS between services, what happens when one backend goes down.

---

## Reference: Tools Cheat Sheet

| Tool | Purpose | Key Command |
|------|---------|-------------|
| `ping` | Reachability + RTT | `ping -c 4 1.1.1.1` |
| `traceroute`/`mtr` | Path + per-hop latency | `mtr google.com` |
| `dig`/`nslookup` | DNS queries | `dig +trace example.com` |
| `ss`/`netstat` | Socket state | `ss -tnp` |
| `curl` | HTTP requests | `curl -v https://example.com` |
| `tcpdump` | Packet capture (CLI) | `tcpdump -ni eth0 port 80 -w out.pcap` |
| `Wireshark` | Packet capture (GUI) | Filter: `tcp.stream eq 0` |
| `strace` | Syscall tracing | `strace -e trace=network -p <pid>` |
| `nmap` | Port scanning | `nmap -sV -p- <host>` |
| `iperf3` | Bandwidth testing | `iperf3 -s` / `iperf3 -c <host>` |
| `wrk`/`hey` | HTTP benchmarking | `wrk -t12 -c400 -d30s http://...` |
| `socat` | TCP/UDP relay/debug | `socat TCP-LISTEN:8080,fork TCP:localhost:80` |

---

## Recommended Resources

### Books
- **"TCP/IP Illustrated, Vol. 1"** — W. Richard Stevens (the definitive reference)
- **"Computer Networks"** — Tanenbaum & Wetherall (broad and deep)
- **"High Performance Browser Networking"** — Ilya Grigorik (free online, excellent on HTTP/2, TLS)
- **"The Linux Programming Interface"** — Michael Kerrisk (sockets + epoll chapters)
- **"Unix Network Programming, Vol. 1"** — Stevens (the sockets bible)

### Interactive Practice
- **Beej's Guide to Network Programming** — free, hands-on C sockets guide
- **Cloudflare Blog** — deep technical articles on real networking problems
- **Julia Evans' "Networking! ACK!"** — zine format, approachable
- **TryHackMe / HackTheBox** — network security hands-on labs
- **Packet Tracer (Cisco)** — free network simulator for routing/switching practice

### Projects to Build
1. TCP chat server (multi-client with epoll)
2. HTTP/1.1 parser from scratch
3. DNS resolver (send raw UDP packets, parse responses)
4. TLS certificate inspector (parse a certificate, show chain)
5. Simple load balancer (round-robin TCP proxy)
6. Rate limiter middleware (token bucket algorithm)
7. WebSocket server from scratch (parse upgrade handshake, frame encoding)

---

*Last updated: 2026. The fundamentals (TCP/IP, sockets, routing) have not changed in 30 years and won't change. The application layer (HTTP/3, QUIC, eBPF, io_uring) is evolving rapidly — always check current docs.*
