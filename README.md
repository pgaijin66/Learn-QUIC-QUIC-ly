# Learn-QUIC-QUIC-ly

## About QUIC

QUIC, or Quick UDP Internet Connections, is a protocol that offers several key features and advantages over TCP and UDP. It is important to remember the following aspects of QUIC:

Multi-Channel Streams: QUIC supports multiple channels or streams for bidirectional communication, similar to lanes on a freeway. Each stream can send data in both directions simultaneously, allowing efficient communication.

0 RTT (Round-Trip Time): QUIC incorporates a feature that enables zero round-trip time. If a client has previously established a connection with the server, it can start using QUIC immediately without repeating the handshake process, making it faster than traditional TCP connections. **Note: It allows a client to send data in the first packet of a connection without waiting for a round trip. However, this feature introduces security considerations and may not be applicable in all scenarios.**

Encryption: QUIC encrypts the entire packet, including the header, providing enhanced security compared to unencrypted UDP and even encrypted TCP packets.

Use of UDP: QUIC utilizes the User Datagram Protocol (UDP) as its transport layer protocol. However, it should be noted that QUIC is a separate protocol built on top of UDP, and the QUIC packets are distinct from regular UDP packets.

QUIC does not replace UDP; rather, it uses UDP as its underlying transport protocol. UDP is unencrypted, while QUIC adds encryption and additional features to UDP. Therefore, even though QUIC uses UDP datagrams, it functions separately from plain UDP, especially during the handshake and RTT processes.

QUIC employs port 443, similar to Transport Layer Security (TLS), to facilitate communication. Essentially, QUIC combines elements of TLS, UDP, handshake mechanisms, and 0 RTT to create an efficient and secure protocol.

In terms of its development, QUIC was initially developed by google as gQUIC. Google contributed the protocol to the IETF for standardization, and the IETF formed the QUIC Working Group to develop the protocol further.

QUIC offers several advantages over TCP, including reduced RTT, embedded TLS handshake within one round trip, and zero RTT performance. Additionally, the QUIC protocol encrypts the header itself, enhancing security compared to TCP, where the header remains unencrypted. **Note: QUIC does encrypt the payload of the packet, but the header of the QUIC packet is not entirely encrypted. QUIC includes a partially encrypted header, which provides essential information for routing and processing the packet.**

The QUIC protocol stack consists of IP, UDP, and QUIC (which embeds TLS 1.3) and can work seamlessly with HTTP/3. *Note: It is important to note that QUIC does not directly include TLS 1.3. Instead, QUIC incorporates elements of TLS 1.3 for encryption and security purposes.**

QUIC is particularly beneficial for HTTP/3, as it addresses head-of-line blocking issues present in HTTP/2. HTTP/2 supports multiplexed streams, allowing parallel processing of multiple GET and POST requests. If a packet is lost in HTTP/3 over QUIC, it can be retransmitted without blocking subsequent packets.

When switching to QUIC, the client first checks whether the server supports TCP or QUIC. If QUIC is supported, the client uses QUIC for communication. In the future, the client can start the communication directly with QUIC from the beginning. If a client starts with TCP, it can switch to QUIC using a protocol upgrade during an HTTP redirect (status code 302).

The QUIC handshake involves an initial exchange of TLS hello and Application-Layer Protocol Negotiation (ALPN) for HTTP/3, followed by the handshake phase, which includes TLS certificate and ALPN information. After the handshake is completed, the HTTP streams can be established.

QUIC connections have unique IDs at each endpoint, and clients typically use UDP port 443 for QUIC communication.

QUIC supports bi-directional or unidirectional streams, similar to lanes on a freeway. Each stream acts as a separate communication channel and can transmit data in both directions. QUIC can handle multiple streams efficiently. While QUIC does support multiple streams for bidirectional communication. **Note: Be-careful when talking about lanes. Streams in QUIC are independent data channels within a single QUIC connection and do not have any inherent ordering or prioritization like lanes on a freeway.**

In QUIC, packets are encapsulated within UDP. The packet structure consists of Ethernet header, IP header, UDP header, and QUIC-specific headers such as CRYPTO frames, ACK frames, and STREAM frames.

## QUIC Discovery

### First Connection

When a request is made to an unfamiliar origin, the client (such as Chrome) doesn't know if the origin supports QUIC (Quick UDP Internet Connections), so it initially sends the request over TCP (Transmission Control Protocol). The server's response includes an Alt-Svc HTTP header, which indicates that the origin also supports QUIC (e.g., 'Alt-Svc: h3=":443"'). This informs Chrome that the origin can communicate using QUIC on port 443. With this knowledge, Chrome can attempt to use QUIC for subsequent requests.

For the next request, Chrome competes a QUIC connection attempt with a TCP connection attempt. These connection attempts are independent of the request itself and serve to establish a connection. Since the first request was sent over TCP, the TCP connection attempt usually wins the race, and the second request is sent over TCP. At some point later, the QUIC connection attempt may succeed. Once that happens, all future requests to the origin are sent over the established QUIC connection.

### Subsequent Connections

Chrome retains the information that an origin supports QUIC, along with the server configuration that enables QUIC's 0-RTT (Zero Round Trip Time) handshake. When Chrome makes a request to an origin it has previously communicated with over QUIC, it competes a TCP connection attempt with a QUIC connection attempt. Since Chrome can perform a 0-RTT handshake using the retained information, the QUIC connection attempt usually wins the race, and the request is issued over the existing QUIC connection.

### Broken Connections

If a QUIC handshake fails, such as when UDP is blocked or the server doesn't support a compatible QUIC version, Chrome marks QUIC as "broken" for that specific host. Any ongoing requests are reissued over TCP. While QUIC is marked as broken for a host, Chrome refrains from attempting any QUIC connections. After a period of 5 minutes, the broken status expires, and QUIC is marked as "recently broken" for that origin. When the next request is sent to that origin, Chrome again competes a QUIC connection attempt with a TCP connection attempt. Since QUIC was recently broken, the 0-RTT handshake is disabled. If the handshake fails again, QUIC is marked as broken for the origin, this time for 10 minutes, and the process continues, doubling the duration of the broken status each time. If the handshake succeeds, the request is sent over QUIC, and QUIC is no longer marked as "recently broken."
