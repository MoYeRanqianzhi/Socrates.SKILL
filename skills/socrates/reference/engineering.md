# Engineering Specialization Overlay

This overlay augments the standard dialectical cycle when the problem is in the software engineering domain. It adds domain-specific questioning dimensions -- it never replaces the core cycle.

## Detection

Activate this overlay when the input involves:
- System architecture or design decisions
- Technology/framework selection
- Performance, scalability, reliability trade-offs
- Code structure, patterns, refactoring
- Infrastructure, deployment, operations
- Data modeling, API design

## Overlay by Stage

### Antithesis Augmentation

In addition to the standard five question types, probe these engineering-specific dimensions:

| Standard question | Engineering augmentation |
|------------------|------------------------|
| "Is this assumption valid?" | "Does this assumption hold at 10x current load? At 100x?" |
| "What's the counter-example?" | "Which known systems failed under the same assumption? (Name specific systems)" |
| "What's the evidence?" | "Is this backed by benchmarks under production-like conditions, or synthetic tests?" |
| "What's the opposing view?" | "What does this look like from ops? From a new hire in 2 years? From the on-call engineer at 3am?" |
| "What are the consequences?" | "If every team adopted this, what happens to the overall system? What are the second-order infrastructure effects?" |

### Synthesis Augmentation

**First Principles Reduction -- Engineering version:**

Strip these to find what remains:
- "Industry best practice says..." (convention, not principle)
- "Company X does it this way..." (authority, not principle)
- "We've always done it like..." (habit, not principle)
- "The framework encourages..." (tool bias, not principle)

What IS a first principle in engineering:
- Physical constraints (latency of light, disk I/O speeds, network bandwidth)
- Theoretical limits (CAP theorem, Amdahl's law, no free lunch)
- Mathematical truths (consistency models, algorithmic complexity)
- Resource constraints (team size, budget, timeline -- these are facts, not opinions)

**Occam's Rebuild -- Engineering version:**

"If you had to build this from scratch using only the standard library and the constraints above, what is the simplest system that satisfies the requirements?"

This question cuts through accumulated complexity. The answer is not "use the standard library" -- it is "what is the essential complexity vs. accidental complexity?"

## Common Engineering Assumption Traps

These are assumptions that engineers frequently treat as self-evident but often deserve questioning:

| Assumption | The question it deserves |
|-----------|------------------------|
| "Microservices are better for scaling" | "Scaling what -- requests, team, or features? At what team size does the coordination overhead exceed the benefit?" |
| "Caching improves performance" | "Which access pattern? What is the cache invalidation strategy? What is the cost of serving stale data?" |
| "NoSQL is better for unstructured data" | "What queries will we run? Do we need transactions? What happens when schema implicitly emerges?" |
| "We need real-time updates" | "What latency is actually acceptable? Would polling every 5 seconds solve 95% of use cases?" |
| "This won't scale" | "What is the actual current load? What is the projected load? When specifically does this become a problem?" |
| "We should abstract this" | "How many times has this pattern actually repeated? Is the abstraction simpler than the duplication?" |
| "Technical debt must be paid" | "What is the actual cost of this debt? Is it in a hot path? Will we touch this code again in the next 6 months?" |
