# ADR-002

# Choosing a Modular Monolith over Microservices

## Metadata

| Item | Value |
|------|-------|
| Category | Application Architecture |
| Decision Type | Architectural Style |
| Complexity | High |
| Impact | Very High |
| Primary Concern | System Decomposition |
| Applicable To | Enterprise Applications |
| Version | 1.0 |

---

# Problem Statement

The engineering team is designing a new business platform expected to support multiple domains including user management, orders, payments, notifications, reporting, and AI-assisted workflows.

The primary architectural decision is whether to implement the platform as a collection of independent microservices or as a modular monolith with clearly defined internal boundaries.

The chosen architecture should balance development speed, operational complexity, scalability, maintainability, and future evolution.

---

# Context

The platform is expected to:

- Support multiple business domains
- Serve web and API clients
- Integrate with cloud-native AWS services
- Support asynchronous processing
- Scale as product adoption grows
- Allow incremental modernization over time

The engineering team consists of fewer than ten developers responsible for development, deployment, monitoring, and production support.

The organization prioritizes rapid feature delivery while minimizing operational overhead.

---

# Practical Experience

During several application modernization initiatives, we observed that architectural complexity often becomes a greater challenge than application complexity.

Large systems frequently evolve from a single codebase into multiple services as teams and business capabilities expand. While microservices provide strong isolation and independent scalability, introducing them too early increases operational responsibilities such as distributed tracing, service discovery, deployment orchestration, network security, API versioning, and inter-service communication.

For platforms managed by relatively small engineering teams, maintaining clear module boundaries within a single deployable application often provides a better balance between maintainability and delivery speed.

This approach also enables gradual extraction of services when clear business or operational drivers emerge rather than introducing distributed systems prematurely.

---

# Decision Drivers

| Driver | Importance |
|---------|------------|
| Development Velocity | High |
| Operational Simplicity | High |
| Maintainability | High |
| Independent Scalability | Medium |
| Team Size | High |
| Deployment Complexity | High |
| Reliability | High |
| Technology Flexibility | Medium |
| Cost | High |

---

# Options Considered

## Option A

Modular Monolith

---

## Option B

Microservices

---

# Decision

The platform will initially be implemented as a **Modular Monolith**.

Business capabilities will be separated into independent modules with clearly defined interfaces, ownership, and responsibilities.

Future extraction into microservices will be performed only when justified by measurable business or technical requirements.

---

# Rationale

A modular monolith provides many of the architectural benefits of microservices without introducing the operational complexity of a distributed system.

Each business capability remains logically isolated while sharing:

- Deployment pipeline
- Runtime
- Monitoring
- Database infrastructure
- Security model

Module boundaries are enforced through package structure, interfaces, and architectural rules rather than network boundaries.

This approach enables:

- Faster feature development
- Simpler debugging
- Easier testing
- Lower infrastructure cost
- Reduced deployment complexity

As the platform grows, modules experiencing significantly different scaling characteristics or ownership requirements can be extracted into independent services.

---

# Comparison

| Capability | Modular Monolith | Microservices |
|------------|------------------|---------------|
| Initial Development Speed | Excellent | Moderate |
| Operational Complexity | Low | High |
| Independent Deployment | Limited | Excellent |
| Independent Scaling | Limited | Excellent |
| Infrastructure Cost | Lower | Higher |
| Team Autonomy | Moderate | Excellent |
| Distributed Transactions | Not Required | Often Required |
| Monitoring Complexity | Low | High |
| Testing Simplicity | High | Moderate |
| Technology Diversity | Limited | High |

---

# Why Microservices Were Not Selected

Microservices introduce several operational responsibilities beyond application development.

These include:

- Service discovery
- API gateway management
- Distributed tracing
- Network security
- Circuit breakers
- Retry strategies
- Message contracts
- Service versioning
- Independent deployments
- Infrastructure automation
- Observability across services

For organizations with relatively small engineering teams, these concerns often outweigh the immediate benefits.

The additional infrastructure would increase development effort without providing proportional business value during the early stages of the platform.

---

# Trade-offs

## Benefits

- Faster onboarding
- Simpler debugging
- Single deployment artifact
- Easier local development
- Lower operational overhead
- Reduced infrastructure cost
- Simpler CI/CD pipelines
- Easier database transactions

---

## Limitations

- Entire application is deployed together
- Shared runtime
- Shared database
- Scaling occurs at application level
- Reduced technology flexibility
- Larger deployment artifact over time

---

# Risks

## Risk

Module boundaries may gradually erode.

**Mitigation**

Enforce architecture rules, code ownership, package boundaries, and regular architecture reviews.

---

## Risk

Application becomes a tightly coupled monolith.

**Mitigation**

Define explicit module interfaces and prohibit direct cross-module database access.

---

## Risk

Future service extraction becomes difficult.

**Mitigation**

Design modules around business capabilities rather than technical layers.

---

# Consequences

### Positive

- Faster feature delivery
- Lower infrastructure cost
- Easier production support
- Simpler deployment process
- Improved developer productivity
- Reduced cognitive load for new engineers

### Negative

- Scaling is application-wide
- Independent releases are limited
- Large codebase may require stronger governance
- Future extraction requires careful planning

---

# Architecture

## Modular Monolith

```
                +--------------------------------------+
                |          Web/API Layer               |
                +------------------+-------------------+
                                   |
                +--------------------------------------+
                |        Application Runtime           |
                +--------------------------------------+
                | User Module                          |
                |--------------------------------------|
                | Order Module                         |
                |--------------------------------------|
                | Payment Module                       |
                |--------------------------------------|
                | Notification Module                  |
                |--------------------------------------|
                | Reporting Module                     |
                |--------------------------------------|
                | AI Module                            |
                +------------------+-------------------+
                                   |
                           Shared Database
```

---

## Microservices

```
                  API Gateway
                       |
      ----------------------------------------
      |        |         |         |         |
      v        v         v         v         v
   User     Order    Payment   Reporting   AI
 Service   Service    Service    Service  Service
      |        |         |         |         |
      v        v         v         v         v
    DB1      DB2       DB3       DB4       DB5

      Communication:
      REST APIs
      EventBridge
      SQS
```

---

# When NOT to Choose a Modular Monolith

Avoid a modular monolith when:

- Multiple engineering teams require independent ownership.
- Services have significantly different scaling characteristics.
- Independent deployment is a business requirement.
- Different services require different technology stacks.
- Organizational structure aligns naturally with service ownership.
- Continuous independent releases are required.

In these scenarios, microservices generally provide greater long-term flexibility.

---

# Lessons Learned

Microservices are not a goal; they are an operational trade-off.

Architectural decisions should reflect the maturity of the organization, team size, operational capabilities, and business priorities rather than industry trends.

A well-structured modular monolith is often easier to maintain than a poorly designed microservice ecosystem.

Starting with clear business boundaries enables future service extraction without requiring a complete redesign.

---

# Future Considerations

The platform should periodically evaluate service extraction based on measurable indicators such as:

- Team ownership boundaries
- Independent scaling needs
- Deployment frequency
- Performance bottlenecks
- Security isolation requirements
- Regulatory compliance
- Availability objectives

Modules should transition to microservices only when the operational benefits clearly outweigh the added complexity.

---

# References

- https://martinfowler.com/bliki/MonolithFirst.html
- https://martinfowler.com/articles/microservices.html
- https://microservices.io/
---
