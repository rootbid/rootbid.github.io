---
title: "HTTP/3 - HTTP over QUIC"
date: 2019-10-25T15:01:03-05:00
draft: false
tags: 
  - HTTP/3
  - QUIC
  - internet
description: "A gentle Theoretical Introduction."
---

As I am sipping down my morning coffee and read my Twitter feed, my app is making multiple API calls over HTTPS to Twitter's servers somewhere on the Internet. Those HTTPS connections are running over TCP via my home WiFi and broadband connection. All's well, the WiFi connection has the least inference, the broadband connection is stable and so there's no packet loss. Those are perfect conditions for HTTPS running over TCP. Not a packet dropped, no congestion. HTTP/2 loves these perfect conditions too, where multiple streams of requests and responses are being transferred between my phone and servers. Unlike HTTP/1.1, HTTP/2 is able to use a single TCP connection for multiple, simultaneous requests with a significant speed advantage under similar conditions.

As my day boots, I step out of my house to be at work. My phone silently switches from my home WiFi to 4G changing the IP address associated with my phone, the TCP connections in use between my phone and Twitter's servers are now in trouble. The connections either stall or get dropped with a delay while internal timers informs my app that the cnnections have disappeared or as connections are re-established. All the connections are suddenly silent.

It might be tempting to blame it on my ISP, but in actual Internet was not designed to work this way. We weren't meant to be carrying around pocket supercomputers that roam across lossy, noisy networks all the while trying to remain productive while complaining about sub-seccond delays in app response time.

---

### QUIC (Quick UDP Internet Connections)
A proposed solution to these problems is QUIC (Quick UDP Internet Connections), a new transport designed from the ground up to improve performance for HTTPS traffic with a new way to send packets across the Internet.


QUIC is an encrypted-by-default Internet transfer protocol, which replaces most of the traditional HTTPS stack: HTTP/2, TLS and TCP. QUIC makes an HTTPS connection between a computer and server with a collection of technologies:

- **UDP replacing TCP.** UDP is widely used for fire-and-forget protocols where packets are sent but their arrival or ordering in not guranteed (while TCP guarantees arrival order and delivery at a cost). Because UDP doesn't have TCP's guarantees it allows developers to innovate new protocols (on top of UDP) that do guarantee delivery and ordering and can incorporate features that TCP lacks.

![HTTP/2 stack vs HTTP/3 stack](/img/quic/HTTP_3_stack.jpg)

- **Reliable data transfers.** While UDP is not a reliable transport, QUIC adds a layer on top of UDP that introduces reliability. It offers re-transmission of packets, congestion control, pacing and other features otherwise present in TCP.

- **Multiple streams within connections.** Similar to SCTP, SSH and HTTP/2, QUIC features separate logical streams within the physical connections (parallel streams, transferring data simultaneously over a single connection without affecting the others). A connection is a negotiated setup between two end-points similar to how TCP works. A QUIC connection is made to a UDP port and IP address, but once established the connection is associated by its _'connnection ID'_.
Over an established connection, either side can create streams and send data to the other end.

- **In order delivery.** QUIC guarantees in-order delivery of streams independently. This means that each stream will send data and maintain data order, but different streams may be delivered out-of-order.

- **Fast handshakes.** QUIC offers both 0-RTT and 1-RTT connection setups, meaning that at best QUIC needs no extra round-trips at all when setting up a new connection.

[![handshakes](/img/quic/handshakes.gif)](https://cloudplatform.googleblog.com/2018/06/Introducing-QUIC-support-for-HTTPS-load-balancing.html)

- **Early data.** QUIC allows a client to include data in the 0-RTT handshake. This feature allows a client to deliver data to the peer as fast as it possibly can, and that then of course allows the server to respond and send data back even sooner.

- **TLS 1.3** QUIC's transport security is TLS 1.3 ([RFC 8446](https://tools.ietf.org/html/rfc8446https://tools.ietf.org/html/rfc8446)) and there are never any unencrypted QUIC connections.

- **Transport and Application level.** QUIC is a transport protocol, which allows the use to other application protocols. The initial application layer protocol is HTTP/3.

- **HTTP/3 over QUIC.** HTTP/3 does HTTp-style transports, including HTTP header compression using QPACK - which is similar to HTTP/2 compression named HPACK.
The HPACk algorithm depends on an ordered delivery of streams so it was not possible to reuse it for HTTP/3, since QUIC offers streams that can be delivered out-of-order.

### The Hyper Text Transfer Protocol
HTTP powers the Web(Internet), it's life began with the HTTP/0.9 protocol in 1991. By 1997 it had evolved to HTTP/1.1, which was standardised within the IETF (Internet Engineering Task Force). After a long life adoption of the protocol and growing number of users on the Web, the ever changing needs on the Web called for a better suited protocol, which is now known as HTTP/2 emerging in 2015. More recently the IETF is intending to deliver a new version - HTTP/3. The motivation for a new protocol was to overcome inefficiencies by the latter facing today’s complexity of the Web.

HTTP/3 is promising, it is set to deliver better performance and security on the Web. Cloudflare, Mozilla and Google have already started implementations while working closely with IETF peers. Open source software and communities have also tracked the advancements in the protocol, implemeting experimental support in their projects ([announcement for HTTP/3 support in curl](https://github.com/curl/curl/wiki/HTTP3)).

### Projects
1. [Implementations of the QUIC transport protocol](https://github.com/quicwg/base-drafts/wiki/Implementations)


### Suggested Readings
1. [The Road to QUIC](https://blog.cloudflare.com/the-road-to-quic/)
2. [HTTP Version 3](https://quicwg.org/base-drafts/draft-ietf-quic-http.html)
3. [QUIC (Active Internet-draft)](https://datatracker.ietf.org/doc/draft-ietf-quic-transport/?include_text=1)
