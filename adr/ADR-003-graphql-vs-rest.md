# ADR-003

# Choosing GraphQL for Client-Facing APIs while retaining REST for Service-to-Service Communication

## Metadata

| Item | Value |
|------|-------|
| Status | Accepted |
| Category | API Architecture |
| Decision Type | API Design |
| Complexity | Medium |
| Impact | High |
| Primary Concern | Client-Server Communication |
| Applicable To | Web, Mobile & API Platforms |
| Version | 1.0 |

---

# Problem Statement

The platform serves multiple frontend applications, including web and mobile clients, each requiring different combinations of business data.

Traditional REST APIs often require multiple requests to retrieve related information, resulting in increased network latency, duplicated aggregation logic on the client, and frequent API changes as UI requirements evolve.

The engineering team evaluated whether GraphQL should replace REST entirely or be introduced selectively for client-facing APIs.

---

# Context

The platform supports:

- Single Page Applications (SPA)
- Mobile applications
- Internal administration portals
- AI-assisted workflows
- Multiple business domains
- Cloud-native deployment on AWS

Frontend teams frequently require data spanning multiple business domains, such as:

- User Profile
- Orders
- Notifications
- Recommendations
- AI-generated insights

These datasets often need to be presented together on a single screen.

The architecture must provide flexibility while maintaining security, performance, and long-term maintainability.

---

# Practical Experience

During application modernization initiatives, frontend teams frequently encountered situations where rendering a single page required calling multiple REST endpoints. Different clients required different subsets of the same business data, resulting in over-fetching, under-fetching, and additional client-side aggregation.

Introducing GraphQL as a client-facing API simplified frontend development by allowing each client to request only the data it required while keeping existing backend services unchanged.

REST APIs continued to serve internal service-to-service communication where predictable contracts, caching, and operational simplicity remained important.

---

# Decision Drivers

| Driver | Importance |
|---------|------------|
| Client Flexibility | High |
| Developer Productivity | High |
| API Evolution | High |
| Performance | High |
| Security | High |
| Caching | Medium |
| Tooling | High |
| Operational Simplicity | Medium |
| Learning Curve | Medium |

---

# Options Considered

## Option A

Continue using REST APIs exclusively.

---

## Option B

Replace all APIs with GraphQL.

---

## Option C

Use GraphQL for client-facing APIs while retaining REST for internal service communication.

---

# Decision

Adopt GraphQL as the primary interface for frontend applications while continuing to use REST APIs for service-to-service communication.

This approach combines GraphQL's flexibility for UI development with REST's simplicity and operational maturity for backend integrations.

---

# Rationale

GraphQL allows clients to define exactly which fields are required, reducing unnecessary network traffic and minimizing client-side aggregation.

Rather than introducing GraphQL across every service, a GraphQL Gateway provides a unified schema while backend services continue exposing REST APIs.

Benefits include:

- Single endpoint for clients
- Reduced over-fetching
- Reduced under-fetching
- Strongly typed schema
- Self-documenting APIs
- Faster frontend development
- Independent backend evolution

This approach minimizes disruption while improving developer experience.

---

# Comparison

| Capability | GraphQL | REST |
|------------|----------|------|
| Client Flexibility | Excellent | Moderate |
| Over-fetching | Minimal | Common |
| Under-fetching | Minimal | Common |
| Single Endpoint | Yes | No |
| HTTP Caching | Limited | Excellent |
| CDN Support | Moderate | Excellent |
| Versioning | Schema Evolution | API Versioning |
| Discoverability | Excellent | Moderate |
| File Uploads | More Complex | Simple |
| Tooling | Excellent | Excellent |
| Learning Curve | Higher | Lower |

---

# Why REST Was Not Replaced Completely

REST remains an excellent choice for many backend integrations.

Advantages include:

- Mature ecosystem
- Excellent HTTP caching
- Simple debugging
- Straightforward monitoring
- Efficient file uploads
- Predictable resource-oriented APIs

Backend services communicating with one another generally require stable contracts rather than highly flexible queries.

Replacing all REST APIs would introduce unnecessary complexity without providing significant additional value.

---

# Trade-offs

## Benefits

- Reduced frontend complexity
- Lower network utilization
- Strong API discoverability
- Schema-driven development
- Better developer experience
- Easier frontend evolution
- Reduced API versioning

---

## Limitations

- More complex server implementation
- Additional gateway layer
- Query optimization required
- Increased authorization complexity
- Caching strategies become less straightforward

---

# Risks

## Risk

Clients may construct expensive queries.

**Mitigation**

Implement query depth limits, complexity analysis, persisted queries, and request timeouts.

---

## Risk

GraphQL gateway becomes a bottleneck.

**Mitigation**

Deploy the gateway horizontally and monitor resolver performance.

---

## Risk

Schema grows without governance.

**Mitigation**

Introduce schema review processes, ownership, and automated schema validation.

---

# Consequences

### Positive

- Improved frontend productivity
- Reduced API maintenance
- Faster UI development
- Better client flexibility
- Reduced network requests
- Easier schema evolution

### Negative

- Additional infrastructure component
- Increased operational monitoring
- Resolver performance becomes important
- More sophisticated authorization model

---

# Architecture

## Traditional REST

```
                React SPA

                    |

      -----------------------------

      |       |        |          |
      v       v        v          v

   Users   Orders  Payments Notifications

      |       |        |          |
      v       v        v          v

   REST    REST     REST       REST
```

A single screen often requires multiple API calls.

---

## GraphQL Gateway

```
                 React SPA

                     |

              GraphQL Gateway

                     |

      ----------------------------------------

      |          |          |               |
      v          v          v               v

    Users     Orders    Payments    Notifications

      |          |          |               |

      +----------+----------+---------------+

                 Existing REST APIs
```

The frontend requests exactly the required fields while backend services remain unchanged.

---

# When NOT to Choose GraphQL

GraphQL may not be the best choice when:

- Building simple CRUD applications
- Creating public APIs with heavy CDN caching
- Developing lightweight internal tools
- Supporting file download services
- Exposing APIs consumed by third-party integrations
- Backend services require predictable resource-oriented APIs

REST often provides a simpler and more efficient solution in these scenarios.

---

# Lessons Learned

GraphQL should be viewed as an API composition layer rather than a replacement for REST.

The primary objective is improving client experience while allowing backend services to evolve independently.

Introducing GraphQL selectively provides significant benefits without requiring a complete redesign of existing services.

Architecture decisions should optimize for the needs of consumers rather than adopting technologies universally.

---

# Future Considerations

As the platform evolves, the following capabilities should be evaluated:

- GraphQL Federation
- Schema Registry
- Persisted Queries
- Query Cost Analysis
- Response Caching
- Real-time Subscriptions
- Fine-grained Authorization
- AI-assisted Query Generation

These enhancements can be introduced incrementally as platform complexity grows.

---

# Decision Metrics

The success of this decision will be evaluated using the following measurable indicators:

| Metric | Target |
|---------|--------|
| Average API calls per page | Reduced by 50% or more |
| Frontend development effort | Reduced implementation time for new screens |
| Backend API versioning | Fewer versioned endpoints |
| GraphQL query response time | P95 < 300 ms |
| Client adoption | 80%+ of new frontend features use GraphQL |
| Query error rate | < 1% |
| Number of breaking API changes | Near zero |

The decision should be revisited if:

- GraphQL gateway becomes a performance bottleneck.
- Query complexity impacts backend services.
- Resolver maintenance becomes difficult.
- Multiple teams require independent schema ownership.

---


# References

- https://graphql.org/learn/
- https://spec.graphql.org/
- https://www.apollographql.com/docs/
- https://martinfowler.com/articles/richardsonMaturityModel.html

---
