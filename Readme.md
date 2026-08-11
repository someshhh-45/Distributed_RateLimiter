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
