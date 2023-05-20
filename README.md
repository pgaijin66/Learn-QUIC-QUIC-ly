# Learn-QUIC-QUIC-ly

QUIC, or Quick UDP Internet Connections, is a protocol that offers several key features and advantages over TCP and UDP. It is important to remember the following aspects of QUIC:

Multi-Channel Streams: QUIC supports multiple channels or streams for bidirectional communication, similar to lanes on a freeway. Each stream can send data in both directions simultaneously, allowing efficient communication.

0 RTT (Round-Trip Time): QUIC incorporates a feature that enables zero round-trip time. If a client has previously established a connection with the server, it can start using QUIC immediately without repeating the handshake process, making it faster than traditional TCP connections. **Note: It allows a client to send data in the first packet of a connection without waiting for a round trip. However, this feature introduces security considerations and may not be applicable in all scenarios.**

Encryption: QUIC encrypts the entire packet, including the header, providing enhanced security compared to unencrypted UDP and even encrypted TCP packets.

Use of UDP: QUIC utilizes the User Datagram Protocol (UDP) as its transport layer protocol. However, it should be noted that QUIC is a separate protocol built on top of UDP, and the QUIC packets are distinct from regular UDP packets.

QUIC does not replace UDP; rather, it uses UDP as its underlying transport protocol. UDP is unencrypted, while QUIC adds encryption and additional features to UDP. Therefore, even though QUIC uses UDP datagrams, it functions separately from plain UDP, especially during the handshake and RTT processes.

QUIC employs port 443, similar to Transport Layer Security (TLS), to facilitate communication. Essentially, QUIC combines elements of TLS, UDP, handshake mechanisms, and 0 RTT to create an efficient and secure protocol.

In terms of its development, QUIC was initially developed by google as gQUICC. Google contributed the protocol to the IETF for standardization, and the IETF formed the QUIC Working Group to develop the protocol further.

QUIC offers several advantages over TCP, including reduced RTT, embedded TLS handshake within one round trip, and zero RTT performance. Additionally, the QUIC protocol encrypts the header itself, enhancing security compared to TCP, where the header remains unencrypted. **Note: QUIC does encrypt the payload of the packet, but the header of the QUIC packet is not entirely encrypted. QUIC includes a partially encrypted header, which provides essential information for routing and processing the packet.**

The QUIC protocol stack consists of IP, UDP, and QUIC (which embeds TLS 1.3) and can work seamlessly with HTTP/3. *Note: It is important to note that QUIC does not directly include TLS 1.3. Instead, QUIC incorporates elements of TLS 1.3 for encryption and security purposes.**

QUIC is particularly beneficial for HTTP/3, as it addresses head-of-line blocking issues present in HTTP/2. HTTP/2 supports multiplexed streams, allowing parallel processing of multiple GET and POST requests. If a packet is lost in HTTP/3 over QUIC, it can be retransmitted without blocking subsequent packets.

When switching to QUIC, the client first checks whether the server supports TCP or QUIC. If QUIC is supported, the client uses QUIC for communication. In the future, the client can start the communication directly with QUIC from the beginning. If a client starts with TCP, it can switch to QUIC using a protocol upgrade during an HTTP redirect (status code 302).

The QUIC handshake involves an initial exchange of TLS hello and Application-Layer Protocol Negotiation (ALPN) for HTTP/3, followed by the handshake phase, which includes TLS certificate and ALPN information. After the handshake is completed, the HTTP streams can be established.

QUIC connections have unique IDs at each endpoint, and clients typically use UDP port 443 for QUIC communication.

QUIC supports bi-directional or unidirectional streams, similar to lanes on a freeway. Each stream acts as a separate communication channel and can transmit data in both directions. QUIC can handle multiple streams efficiently. While QUIC does support multiple streams for bidirectional communication. **Note: Be-careful when talking about lanes. Streams in QUIC are independent data channels within a single QUIC connection and do not have any inherent ordering or prioritization like lanes on a freeway.**

In QUIC, packets are encapsulated within UDP. The packet structure consists of Ethernet header, IP header, UDP header, and QUIC-specific headers such as CRYPTO frames, ACK frames, and STREAM frames.