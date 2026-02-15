# HTTP, HTTPS, Certificates, and HTTP/2 -- Interview Notes

## 10. Quick Interview Summary

HTTP: Stateless request--response protocol over TCP.

HTTPS: HTTP secured using TLS with certificate validation and session
key encryption.

CA Certificates: Preinstalled in OS/JVM; if missing, import manually.

HTTP/2: Solves latency and connection overhead using multiplexing,
header compression, and binary framing.
---
## 1. How HTTP Works

HTTP (HyperText Transfer Protocol) is a stateless request--response
protocol used for communication between a client and a server.

### Steps in an HTTP Request

1.  User enters a URL in browser or client.
2.  DNS resolves domain name to IP address.
3.  TCP connection is established (3‑way handshake).
4.  Client sends HTTP request (method, headers, body).
5.  Server processes request (business logic, database, etc.).
6.  Server sends HTTP response (status code, headers, body).
7.  TCP connection may be reused or closed.

### Important Characteristics

-   Stateless protocol
-   Runs on TCP
-   Default ports:
    -   HTTP → 80
    -   HTTPS → 443

------------------------------------------------------------------------

## 2. How HTTPS Works (Secured Connection)

HTTPS = HTTP over TLS/SSL.

### TLS Handshake Steps

1.  Client Hello:
    -   Supported TLS versions
    -   Cipher suites
    -   Random value
2.  Server Hello:
    -   Selected cipher suite
    -   SSL certificate (contains public key)
3.  Certificate Verification: Client checks:
    -   Certificate validity
    -   Domain match
    -   Trusted Certificate Authority (CA)
    -   Expiry or revocation
4.  Key Exchange:
    -   Client generates session key
    -   Encrypts with server public key
    -   Server decrypts using private key
5.  Secure Communication Begins:
    -   All further data encrypted using symmetric encryption.

------------------------------------------------------------------------

## 3. How Connection Is Secured

Security is achieved using: - Asymmetric encryption (for key exchange) -
Symmetric encryption (for data transfer) - Digital signatures (to verify
server identity)

Benefits: - Confidentiality - Integrity - Authentication

------------------------------------------------------------------------

## 4. What Happens If CA Certificate Is Not Present

If the client cannot verify the certificate chain: - TLS handshake
fails - Browser shows security warning - Java throws
SSLHandshakeException

### How to Add CA Certificate Manually (Java Example)

1.  Obtain certificate: openssl s_client -connect host:443 -showcerts

2.  Import into truststore: keytool -import -trustcacerts -alias mycert
    -file server.crt -keystore cacerts
    $JAVA_HOME/lib/security/cacerts

Default password: changeit

Better approach: Use a custom truststore instead of modifying global
cacerts.

------------------------------------------------------------------------

## 5. Where Trusted CA Certificates Come From

Trusted root certificates are preinstalled in: - Operating system trust
store - Browser trust store - JVM truststore (cacerts)

They are updated via: - OS updates - Browser updates - JDK updates

Websites do not permanently install certificates on your machine.

------------------------------------------------------------------------

## 6. HTTP/2 -- What Problem It Solves

### Problems in HTTP/1.1

-   One request per connection (or limited parallelism)
-   Head-of-line blocking
-   Large header overhead
-   Multiple TCP connections needed

------------------------------------------------------------------------

## 7. How HTTP/2 Works

HTTP/2 introduces:

### Multiplexing

Multiple requests and responses are sent over a single TCP connection
simultaneously using streams.

Requests are split into frames and interleaved, then reassembled.

### Header Compression

HPACK reduces header size to improve performance.

### Binary Protocol

More efficient than text-based HTTP/1.1.

------------------------------------------------------------------------

## 8. Do Developers Need to Do Anything Manually?

Usually no.

HTTP/2 is handled automatically by: - Browsers - Web servers - Load
balancers - Frameworks (Spring Boot, Netty, etc.)

Requirements: - Server must support HTTP/2 - HTTPS usually required -
Modern client or browser

------------------------------------------------------------------------

## 9. How HTTP/2 Handles Multiple Requests

1.  Single TCP connection established.
2.  Each request assigned a stream ID.
3.  Frames from multiple streams interleaved.
4.  Server processes streams independently.
5.  Responses returned in parallel.

No need to open multiple TCP connections.

------------------------------------------------------------------------


