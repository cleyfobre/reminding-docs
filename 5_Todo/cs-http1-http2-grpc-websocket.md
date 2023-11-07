## http1 and http2

### http2 is default protocol by all the browsers

- HTTP/2 is a newer version of the HTTP protocol that improves upon the performance of HTTP/1.1 by using features such as binary framing, server push, and header compression
- HTTPS (HyperText Transfer Protocol Secure) is the secure version of HTTP, which uses Transport Layer Security (TLS) to encrypt the data that is being transmitted between the client (i.e. your browser) and the server. 


### http2 is more faster and efficient than http1
- Most modern browsers, such as Google Chrome, Firefox, and Edge, support HTTP/2 by default. HTTP/2 offers several improvements over HTTP/1, including faster page load times, improved security, and better use of network resources.


### grpc is built on top of http2
- gRPC uses HTTP/2 as its underlying protocol for communication between the client and the server. HTTP/2 provides several benefits over HTTP/1.1, such as multiplexing, server push, and header compression, which can improve the performance and efficiency of bi-directional communication in gRPC.
- gRPC is a high-performance RPC (remote procedure call) framework that uses binary serialization and HTTP/2 protocol for communication. It is designed for efficient communication between microservices and can support bidirectional streaming for real-time communication. gRPC offers language support for many programming languages, and it can generate client and server stubs automatically from a protocol buffer definition file (protobuf).


### websocket is built on top of http1

- WebSocket, on the other hand, is a protocol for creating a persistent, bi-directional communication channel between a client and a server. It is designed for real-time, low-latency communication, and is commonly used in applications such as chat rooms, online games, and financial trading platforms.
- WebSocket is built on top of the HTTP protocol. It uses the initial HTTP handshake to establish a connection and then upgrades the connection to the WebSocket protocol. This allows WebSocket to work with existing web infrastructures, such as load balancers and proxies, that are designed to work with HTTP traffic.
- The initial HTTP handshake request and response contain an "Upgrade" header which indicates that the client wants to upgrade the connection to the WebSocket protocol. The server can then respond with a "101 Switching Protocols" status code and upgrade the connection to the WebSocket protocol. Once the connection is upgraded, both the client and server can send messages to each other in real-time without the overhead of HTTP headers.