# Architecture Decision: Microservices vs. Monolith for E-Commerce Platform

## Context

- **Project**: New e-commerce platform
- **Team size**: 5 engineers
- **Decision**: Microservices vs. Monolith

---

## My Recommendation: Start with a Modular Monolith

For a team of 5 engineers building a new e-commerce platform, I strongly recommend starting with a **well-structured modular monolith**, with a clear path to extract services later if and when the need arises.

Here is my reasoning.

---

## Analyzing Microservices for This Scenario

Microservices offer real benefits -- independent deployability, technology flexibility, isolated scaling, and fault isolation. However, these benefits come at a significant cost, and that cost is disproportionately heavy for a small team.

### The operational burden is real

With microservices, your 5 engineers would need to manage:

- **Service discovery and communication** (synchronous via REST/gRPC, asynchronous via message queues)
- **Distributed data management** (each service owns its database, requiring eventual consistency patterns like Saga)
- **Deployment infrastructure** (container orchestration with Kubernetes or similar, CI/CD pipelines per service)
- **Observability** (distributed tracing, centralized logging, health checks across services)
- **Network reliability** (retries, circuit breakers, timeouts, handling partial failures)
- **API gateway and routing**
- **Cross-cutting concerns** (authentication, authorization, rate limiting replicated or centralized)

This is not a trivial list. Each item represents weeks or months of engineering effort to get right. For 5 engineers, this infrastructure work would consume a large portion of your capacity before you write a single line of business logic.

### The coordination tax

With microservices, a feature that touches multiple domains (e.g., "apply a coupon at checkout that adjusts inventory hold and triggers a notification") requires coordinating changes across multiple repositories, multiple deployments, and multiple teams -- except you do not have multiple teams. You have 5 people. The coordination overhead that microservices impose is designed to solve problems of *organizational* scale (dozens or hundreds of engineers stepping on each other), not technical scale.

### Premature distribution is costly

You do not yet know where your domain boundaries truly lie. E-commerce seems well-understood, but *your* e-commerce platform's specific needs will only become clear through building it. Drawing service boundaries incorrectly is one of the most expensive mistakes in microservices architecture, because refactoring across service boundaries requires API versioning, data migration, and careful orchestration -- far more expensive than refactoring modules within a monolith.

---

## Why a Modular Monolith Works

### What it is

A modular monolith is a single deployable unit, but internally organized into well-defined modules with clear boundaries. Think of it as microservices *in concept* but monolith *in deployment*.

```
e-commerce-platform/
├── modules/
│   ├── catalog/          # Product catalog, categories, search
│   ├── inventory/        # Stock management, reservations
│   ├── orders/           # Order lifecycle, checkout
│   ├── payments/         # Payment processing, refunds
│   ├── users/            # Authentication, profiles
│   ├── notifications/    # Email, SMS, push notifications
│   └── shipping/         # Shipping rates, tracking
├── shared/               # Shared kernel (common types, utilities)
└── infrastructure/       # Database, caching, messaging
```

Each module:
- Has its own internal models and logic
- Exposes a well-defined interface (public API) to other modules
- Can own its own database schema (or at minimum, its own set of tables with no cross-module joins)
- Communicates with other modules through defined contracts, not by reaching into their internals

### The advantages for your team

1. **Simple operations**: One application to deploy, one database to back up, one set of logs to search, one CI/CD pipeline. Your 5 engineers spend time on product features, not infrastructure.

2. **Fast development**: Function calls between modules are orders of magnitude faster and more reliable than network calls between services. No serialization, no network latency, no partial failure handling for internal communication.

3. **Easy refactoring**: When you discover that your initial domain boundaries were wrong (and you will), refactoring is straightforward -- move code between modules, update imports, done. No API versioning, no data migration between databases.

4. **Straightforward debugging**: A single stack trace tells you everything. No need to correlate traces across services.

5. **Clear extraction path**: Because modules have explicit boundaries, extracting one into a separate service later is a well-defined operation. You are not "locked in" to a monolith -- you are building the foundation for potential future services.

---

## When to Consider Extracting Services

The modular monolith gives you natural extraction points. Consider pulling a module into its own service when:

- **A specific module needs independent scaling** (e.g., your product search is getting 100x more traffic than the rest of the platform)
- **Your team grows beyond 8-10 engineers** and coordination within the monolith becomes a bottleneck
- **A module has fundamentally different deployment or availability requirements** (e.g., payment processing needs 99.99% uptime while the recommendation engine can tolerate more downtime)
- **You want to use a different technology for a specific module** (e.g., a search module that benefits from Elasticsearch as its primary store)

The key insight: you do not need to make this decision now. The modular monolith preserves optionality.

---

## Practical Implementation Advice

### Enforce module boundaries from day one

The biggest risk with a monolith is that it degrades into a "big ball of mud" where everything depends on everything. Prevent this by:

- Defining clear public interfaces for each module
- Using linting rules or architectural tests to prevent cross-module imports that bypass the public API
- Keeping each module's database schema isolated (no cross-module foreign keys or joins)

### Use asynchronous patterns internally where appropriate

Even within a monolith, you can use an internal event bus for cross-module communication (e.g., "OrderPlaced" event triggers inventory update and notification). This decouples modules and makes future service extraction easier.

### Invest in a good CI/CD pipeline

One of the valid advantages of microservices is independent deployment. You can partially achieve this with feature flags, blue-green deployments, and good test coverage -- all within a monolith and all manageable by a team of 5.

---

## Common Objections Addressed

**"But microservices scale better."**
Scaling a monolith is straightforward for most workloads: run more instances behind a load balancer. You would need enormous traffic before a monolith's scaling limits become the bottleneck. Most e-commerce platforms handle substantial load this way. When you do hit those limits, you extract the specific bottleneck module.

**"We'll have to rewrite it all later."**
No, because you built it as a modular monolith. Extraction is incremental. You move one module at a time. The modular boundaries you established from the start make this a controlled operation.

**"Microservices are the industry standard."**
Microservices became prevalent at companies with hundreds or thousands of engineers (Netflix, Amazon, Google). They solved organizational problems at scale. Many companies have publicly discussed regretting premature adoption of microservices. The architecture should serve your current reality, not an aspirational future.

**"What about fault isolation?"**
A crash in one module can bring down the monolith. This is a real concern, but it is mitigatable: good error handling, bulkheading (thread pool isolation), and the fact that most faults in practice are logic bugs (which crash a microservice just as effectively). The operational complexity of distributed systems introduces its own class of failures that are harder to diagnose and recover from.

---

## Summary

| Factor | Monolith (Modular) | Microservices |
|--------|-------------------|---------------|
| Team of 5 engineers | Well-suited | Overstretched |
| Operational complexity | Low | High |
| Development speed (early) | Fast | Slow (infra overhead) |
| Domain boundary discovery | Easy to adjust | Expensive to adjust |
| Scaling | Sufficient for most loads | Granular but complex |
| Future flexibility | Extract services as needed | Already distributed |
| Debugging | Straightforward | Requires distributed tracing |
| Time to market | Faster | Slower |

**Start with a modular monolith. Build clear boundaries. Extract services when real, measured needs demand it -- not before.**

The most pragmatic architecture is one that lets your 5 engineers focus the maximum amount of their effort on building the product that serves your customers, rather than on the infrastructure that serves the architecture.
