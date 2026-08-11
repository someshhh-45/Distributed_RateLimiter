## Understanding the Problem

### What is a Rate Limiter?

A rate limiter controls how many requests a client can make within a given
timeframe. Think of it as a traffic controller for your API 
for example,
allowing 100 requests per minute per user, then rejecting anything beyond
that with an `HTTP 429 Too Many Requests` response.

Rate limiters serve three key purposes:

- **Prevent abuse** — stop malicious or careless clients from hammering your API
- **Protect infrastructure** — shield your servers from being overwhelmed by traffic bursts
- **Ensure fairness** — guarantee no single user can starve others of capacity


## Requirements

### Functional Requirements

- **FR1:** The system must limit the number of requests a client can make within a configurable time window.
- **FR2:** The system must support rate limiting by arbitrary key (user ID, API key, IP address, etc.).
- **FR3:** The system must allow configurable capacity (burst limit) and refill rate per key.
- **FR4:** The system must reject requests exceeding the limit with an `HTTP 429 Too Many Requests` response.
- **FR5:** The system must return metadata with each request — remaining tokens and, when denied, a `Retry-After` value.
- **FR6:** The system must enforce limits consistently across multiple application instances (i.e. a limit of 100/min must hold cluster-wide, not 100/min per instance).
- **FR7:** The system must support both synchronous and asynchronous acquisition APIs.
- **FR8:** The system must allow easy integration via servlet filters and Spring interceptors, without requiring changes to business logic.

### Non-Functional Requirements

- **NFR1 (Consistency):** Token consumption checks must be atomic — concurrent requests from different instances must never both succeed when only one token remains.
- **NFR2 (Low latency):** Each rate-limit check should add minimal overhead (target: single-digit milliseconds at p99) to the request path.
- **NFR3 (Scalability):** The system must handle high request throughput (e.g. thousands of checks/sec) without becoming a bottleneck.
- **NFR4 (Availability):** The limiter should degrade gracefully if Redis is temporarily unreachable (e.g. fail-open or fail-closed, configurable) rather than crashing the calling service.
- **NFR5 (Memory efficiency):** Idle bucket keys must expire automatically (TTL) to prevent unbounded growth in Redis memory.
- **NFR6 (Portability):** The library must work with standalone Redis, Redis Sentinel, and Redis Cluster.
- **NFR7 (Observability):** The system should expose metrics (allowed/denied counts, latency) for monitoring.
- **NFR8 (Testability):** Core logic must be covered by unit tests, and cross-instance behavior must be verifiable via integration tests.
