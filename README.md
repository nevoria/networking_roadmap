Here is the expanded, highly granular roadmap with specific technical sub-concepts. This breaks down exactly what you need to research and understand for each major topic to build a deep, engineering-level mental model.

Phase 1: Core Packet Mechanics & Routing 🌐
[ ] Physical & Data Link Layers:

[ ] MAC addresses: Organizationally Unique Identifier (OUI) and Network Interface Controller (NIC) specific parts.

[ ] Address Resolution Protocol (ARP): Broadcast requests, unicast replies, and ARP caching/poisoning.

[ ] Switches: MAC learning, forwarding tables (CAM tables), and broadcast domains.

[ ] Network Layer (IP):

[ ] IPv4 vs. IPv6: Address structure and depletion.

[ ] Packet Headers: Time-to-Live (TTL), Source/Destination IPs, and Checksums.

[ ] Fragmentation: Maximum Transmission Unit (MTU), IP flags, and reassembly.

[ ] Subnetting & CIDR:

[ ] Binary Math: Translating dotted-decimal IPs to binary.

[ ] Masks: Calculating Network IDs, Broadcast IDs, and usable host ranges.

[ ] VLSM (Variable Length Subnet Masking): Carving large networks into smaller, unequal subnets.

[ ] Routing Fundamentals:

[ ] The Routing Table: Next-hop concepts and routing metrics.

[ ] Static vs. Default Routes: Manual path definitions (0.0.0.0/0).

[ ] Dynamic Protocols: Distance-vector vs. link-state routing (understanding OSPF at a high level).

[ ] NAT & Gateways:

[ ] SNAT vs. DNAT: Source vs. Destination Network Address Translation.

[ ] PAT (Port Address Translation): How thousands of home devices share one public IP.

Phase 2: Transport & Session Control 📦
[ ] TCP Deep Dive:

[ ] The 3-way handshake: SYN, SYN-ACK, ACK.

[ ] Reliability: Sequence numbers, acknowledgment numbers, and retransmission timeouts.

[ ] The 4-way teardown: FIN, ACK, FIN, ACK.

[ ] State Machine: Understanding TIME_WAIT, CLOSE_WAIT, and ESTABLISHED states.

[ ] Congestion Control:

[ ] Flow Control: The TCP Receive Window (preventing the receiver from being overwhelmed).

[ ] Congestion Window (cwnd): Preventing the network from being overwhelmed.

[ ] Algorithms: High-level differences between Reno, Cubic, and BBR.

[ ] UDP Mechanics:

[ ] Datagram Structure: The minimal 8-byte header.

[ ] Statelessness: The lack of ordering, guarantees, or handshakes.

[ ] Use Cases: Why DNS and fast-paced multiplayer games choose UDP.

[ ] Sockets API:

[ ] System Calls: socket(), bind(), listen(), accept(), connect(), close().

[ ] Socket Families & Types: AF_INET vs AF_INET6, and SOCK_STREAM vs SOCK_DGRAM.

[ ] File Descriptors (FDs):

[ ] Unix Philosophy: How network connections are treated exactly like text files by the OS.

[ ] Stream I/O: Using read()/write() vs. send()/recv().

Phase 3: Application Protocols & Structuring Bytes 🗣️
[ ] DNS Architecture:

[ ] Queries: Recursive vs. iterative resolution.

[ ] Hierarchy: Root servers -> TLD servers -> Authoritative servers.

[ ] Records: A, AAAA, CNAME, MX, and TXT records.

[ ] HTTP Evolution:

[ ] HTTP/1.1: Keep-Alive connections and text-based headers.

[ ] HTTP/2: Binary framing, streams, and multiplexing over a single TCP connection.

[ ] HTTP/3: Replacing TCP with QUIC (built on UDP) to fix head-of-line blocking.

[ ] WebSockets & SSE:

[ ] The Upgrade Header: Moving from standard HTTP to a persistent WebSocket.

[ ] Server-Sent Events (SSE): Unidirectional data streaming from server to client.

[ ] TLS/SSL Encryption:

[ ] Cryptography: Symmetric (fast data transfer) vs. Asymmetric (secure key exchange).

[ ] The TLS Handshake: ClientHello, ServerHello, and key generation.

[ ] Certificate Authorities (CAs): Chain of trust and public/private key pairs.

[ ] RPC & gRPC:

[ ] Serialization: Protocol Buffers (binary) vs. JSON (text).

[ ] App-to-App: Defining strict service interfaces for backend microservices.

Phase 4: High-Performance App Networking (The Code) 🚀
[ ] Blocking vs. Non-Blocking I/O:

[ ] Context Switching: The CPU cost of thread-per-connection models.

[ ] Flags: Using fcntl to set O_NONBLOCK.

[ ] Handling Errors: Managing EAGAIN, EWOULDBLOCK, and EINPROGRESS without crashing.

[ ] I/O Multiplexing (epoll):

[ ] The Bottleneck: Why older select() and poll() system calls are slow (O(n)).

[ ] The Architecture: How epoll uses Red-Black trees and ready-lists in the Linux kernel.

[ ] The API: epoll_create(), epoll_ctl(), and epoll_wait().

[ ] Trigger Modes:

[ ] Level-Triggered (LT): The OS tells you continuously if data is available.

[ ] Edge-Triggered (ET): The OS tells you once when state changes. Managing the while loops required for ET.

[ ] Asynchronous Patterns:

[ ] The Reactor Pattern: The event loop dispatches handlers (common in Linux/epoll).

[ ] The Proactor Pattern: The OS handles the read/write and notifies the app when finished (common in Windows/IOCP).

[ ] Framework Implementation:

[ ] Python: Understanding the inner workings of the asyncio event loop.

[ ] C++: Grasping Boost.Asio concepts like io_context, completion tokens, and thread-safe strands.

Phase 5: Modern Architecture & Infrastructure 🏗️
[ ] Load Balancing:

[ ] Layer 4 (Transport): Routing raw TCP/UDP packets (e.g., HAProxy).

[ ] Layer 7 (Application): Routing based on HTTP headers or URL paths (e.g., Nginx).

[ ] Algorithms: Round Robin, Least Connections, and IP Hashing.

[ ] Reverse Proxies:

[ ] TLS Termination: Decrypting traffic before it hits the backend app.

[ ] Headers: Injecting X-Forwarded-For so the backend knows the real client IP.

[ ] Container Networking:

[ ] Namespaces: How Linux isolates network interfaces.

[ ] veth pairs: Virtual ethernet cables connecting a Docker container to the host bridge.

[ ] Service Meshes:

[ ] Sidecar Proxies: Running an Envoy proxy next to every app instance.

[ ] mTLS (Mutual TLS): Encrypting traffic internally between your own servers.

[ ] Network Analysis (Debugging):

[ ] Wireshark: Visually dissecting packet captures and following TCP streams.

[ ] tcpdump: Capturing raw packets from the command line using BPF syntax.

[ ] strace: Tracing the exact system calls your app makes to the OS.

Let's begin Phase 1. To get two physical machines talking on the same local network, they need to know each other's MAC addresses. If a computer only knows the target machine's IP address, how does it use the ARP protocol to discover that target's MAC address?
