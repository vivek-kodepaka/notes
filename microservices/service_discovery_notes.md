
# Service Discovery – Complete Notes

## What is Service Discovery?

Service Discovery is a mechanism in microservices architecture that allows services to automatically detect the location (IP and port) of other services without hardcoding addresses.

Problem it solves:
- Containers restart → IP changes
- Pods scale dynamically
- Manual configuration becomes impossible

Service discovery maintains a registry of all running service instances.

---

## Basic Flow

1. Service starts
2. Service registers itself in registry
3. Client queries registry
4. Client calls service

---

## Why Service Discovery is Needed

Without service discovery:
- Hardcoded URLs
- Deployment failures
- Scaling issues

With service discovery:
- Dynamic lookup
- Fault tolerance
- Easier scaling

---

## Types of Service Discovery

### 1. Client-Side Discovery

Flow:
1. Client asks registry
2. Client selects instance
3. Client calls service

Example:
- Netflix Eureka
- Ribbon (Load balancing on client)

Advantages:
- Intelligent client routing
- Reduced network hop

Disadvantages:
- Client complexity
- Language-specific logic

---

### 2. Server-Side Discovery

Flow:
1. Client calls load balancer
2. Load balancer queries registry
3. Load balancer routes request

Examples:
- Kubernetes Service
- AWS ELB
- NGINX

Advantages:
- Simpler clients
- Centralized routing

Disadvantages:
- Extra network hop

---

## Popular Service Discovery Tools

### Netflix Eureka
- Client-side discovery
- Used in Spring Cloud
- Each service registers itself

### Consul
- Health checks
- Key-value store
- Multi-datacenter support

### Zookeeper
- Distributed coordination
- Used in Kafka ecosystems

### Kubernetes Service Discovery
- Built-in DNS
Example:
orders.default.svc.cluster.local

---

## Registration Methods

### Self Registration
Service registers itself to registry.

Example:
Spring Cloud Eureka client.

Pros:
- Simple

Cons:
- Service tightly coupled with registry

---

### Third-Party Registration
Sidecar or orchestrator registers service.

Example:
Kubernetes

Pros:
- Decoupled architecture

Cons:
- Slightly complex setup

---

## Health Checks

Registry periodically checks:
- Service availability
- Response time

If service fails:
- Removed from registry

Types:
- HTTP health check
- TCP check
- Heartbeats

---

## Load Balancing with Service Discovery

Two approaches:
1. Client-side load balancing (Spring Cloud LoadBalancer)
2. Server-side load balancing (Kubernetes, NGINX)

Algorithms:
- Round Robin
- Least Connections
- Random

---

## Service Discovery in Kubernetes

Kubernetes provides:
- ClusterIP
- Headless Services
- DNS-based discovery

Example:
http://orders-service

Kube DNS resolves to pod IPs.

---

## Challenges in Service Discovery

- Network latency
- Stale registry entries
- Split brain issues
- Scaling registry

Solutions:
- Heartbeats
- TTL
- Replicated registries

---

# Interview Questions (Collapsible)

<details>
<summary>1. What is Service Discovery?</summary>

Service discovery is a mechanism that allows services to dynamically find the network locations of other services without hardcoding IP addresses.

</details>

<details>
<summary>2. Why is Service Discovery required in Microservices?</summary>

Because containers and pods scale dynamically and their IP addresses change frequently.

</details>

<details>
<summary>3. Difference between Client-Side and Server-Side Discovery?</summary>

Client-side:
Client queries registry and selects instance.

Server-side:
Load balancer queries registry and routes request.

</details>

<details>
<summary>4. What are popular Service Discovery tools?</summary>

Eureka, Consul, Zookeeper, Kubernetes DNS.

</details>

<details>
<summary>5. How does Service Discovery work in Kubernetes?</summary>

Kubernetes assigns DNS names to services and resolves them internally using kube-dns or CoreDNS.

</details>

<details>
<summary>6. What is Self Registration?</summary>

A service registers itself with the service registry on startup.

</details>

<details>
<summary>7. What is Health Check in Service Discovery?</summary>

A mechanism to verify service availability and remove unhealthy instances.

</details>

<details>
<summary>8. What happens if registry goes down?</summary>

Caching and replicated registries are used to prevent total failure.

</details>

<details>
<summary>9. Difference between API Gateway and Service Discovery?</summary>

Service discovery locates services.
API Gateway manages routing, authentication, rate limiting.

</details>

<details>
<summary>10. How does load balancing work with Service Discovery?</summary>

Load balancer selects one instance among many registered services using algorithms like round robin.

</details>

---

# Quick Revision

Service Discovery = Dynamic lookup of services

Client-side → Eureka  
Server-side → Kubernetes / Load balancer  

Used with:
- Load balancing
- Health checks
- Auto scaling

