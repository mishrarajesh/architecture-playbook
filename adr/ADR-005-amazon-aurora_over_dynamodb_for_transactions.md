# ADR-005

# Choosing Amazon Aurora over Amazon DynamoDB for Transactional Business Applications

## Metadata

| Item | Value |
|------|-------|
| Status | Accepted |
| Category | Data Architecture |
| Decision Type | Database Selection |
| Complexity | High |
| Impact | Very High |
| Primary Concern | Transactional Data Storage |
| Applicable To | Enterprise Business Platforms |
| Version | 1.0 |

---

# Problem Statement

The platform requires a primary database capable of supporting transactional business operations such as customer management, orders, invoices, payments, reporting, and workflow management.

The engineering team evaluated Amazon Aurora and Amazon DynamoDB to determine the most appropriate persistence layer.

The selected solution should support transactional consistency, flexible querying, scalability, operational simplicity, and future growth.

---

# Context

The application manages multiple business domains with highly related data.

Typical operations include:

- Customer Management
- Orders
- Payments
- Reporting
- AI Metadata
- Audit History
- User Management

Business workflows require:

- ACID transactions
- Complex filtering
- Relational queries
- Reporting
- Referential integrity
- Data consistency

Most business entities have strong relationships with one another.

---

# Practical Experience

During modernization of enterprise business applications, relational data remained the foundation of core business processes.

Although DynamoDB offers exceptional scalability and predictable performance, many business capabilities required relational joins, transactional updates, flexible reporting, and evolving query patterns.

Aurora allowed the engineering team to preserve a normalized data model while benefiting from managed infrastructure, automatic backups, read replicas, and high availability.

DynamoDB continued to be an excellent choice for specific workloads such as session storage, metadata, event processing, and high-volume key-value access patterns.

---

# Decision Drivers

| Driver | Importance |
|---------|------------|
| ACID Transactions | High |
| Relational Modeling | High |
| Reporting | High |
| Query Flexibility | High |
| Operational Simplicity | High |
| Scalability | High |
| Performance | High |
| Cost Predictability | Medium |
| Developer Familiarity | High |

---

# Options Considered

## Option A

Amazon Aurora PostgreSQL/MySQL

---

## Option B

Amazon DynamoDB

---

# Decision

Amazon Aurora will be the primary transactional database.

Amazon DynamoDB will be used selectively for workloads that naturally align with key-value access patterns, including caching metadata, session management, event processing, and application state.

---

# Rationale

Most platform capabilities rely on relational business data.

Examples include:

- Customer → Orders
- Orders → Payments
- Users → Roles
- Reports → Transactions
- Products → Categories

Aurora provides:

- Full SQL support
- ACID compliance
- Foreign keys
- Joins
- Complex reporting
- Mature indexing
- Stored procedures (where appropriate)
- Read replicas
- Multi-AZ availability

These capabilities closely align with enterprise business applications.

---

# Comparison

| Capability | Aurora | DynamoDB |
|------------|---------|-----------|
| SQL Support | Excellent | No |
| ACID Transactions | Excellent | Good |
| Complex Joins | Excellent | No |
| Horizontal Scaling | Moderate | Excellent |
| Flexible Queries | Excellent | Limited |
| Schema Flexibility | Moderate | Excellent |
| Operational Overhead | Low | Very Low |
| Global Distribution | Good | Excellent |
| Reporting | Excellent | Limited |
| Learning Curve | Lower | Higher (Modeling) |

---

# Why DynamoDB Was Not Selected

DynamoDB is designed for predictable access patterns.

However, transactional business platforms frequently evolve.

New reporting requirements often introduce previously unknown query patterns.

Implementing relational workflows in DynamoDB would require:

- Data duplication
- Single-table modeling
- Denormalization
- Application-managed relationships
- Additional operational complexity

These trade-offs were not justified for the core business domain.

---

# Trade-offs

## Benefits

- Mature SQL ecosystem
- Rich query capabilities
- Easier reporting
- Simpler data modeling
- Strong consistency
- Familiar development model

---

## Limitations

- Vertical scaling limits
- More expensive at massive scale
- Schema migrations
- Relational tuning required

---

# Risks

## Risk

Database becomes a scaling bottleneck.

**Mitigation**

Introduce read replicas, partitioning, caching, and workload separation.

---

## Risk

Complex reporting affects transactional performance.

**Mitigation**

Move analytical workloads to dedicated reporting databases or data warehouses.

---

## Risk

Application becomes tightly coupled to relational schema.

**Mitigation**

Maintain clean domain boundaries and avoid exposing database structure directly to clients.

---

# Consequences

### Positive

- Simplified development
- Easier reporting
- Strong transactional guarantees
- Lower developer learning curve
- Faster feature delivery

### Negative

- Scaling requires planning
- Schema evolution must be managed
- Large analytical workloads require additional architecture

---

# Architecture

## Aurora

```
                Applications

                     |

             Repository Layer

                     |

             Amazon Aurora

                     |

     Read Replica      Backups
```

---

## DynamoDB (Selective Usage)

```
Applications

      |

+----------------------------+

| Session Storage            |

| AI Metadata                |

| Event State                |

| Cache                      |

+-------------+--------------+

              |

        Amazon DynamoDB
```

---

# When NOT to Choose Aurora

Aurora may not be the best choice when:

- Access patterns are strictly key-value.
- Massive horizontal scaling is required.
- Data relationships are minimal.
- Millisecond latency at global scale is required.
- Serverless applications require extreme elasticity.
- Event metadata dominates the workload.

DynamoDB is generally the stronger option in these scenarios.

---

# Lessons Learned

Choosing a database should begin with understanding data relationships rather than expected traffic volume.

Relational databases continue to be an excellent fit for transactional business applications.

NoSQL databases should complement—not necessarily replace—relational databases when workload characteristics justify their adoption.

---

# Future Considerations

The platform should periodically evaluate:

- Aurora Serverless
- Global Database
- Read replica optimization
- DynamoDB for new bounded contexts
- Data archiving
- CQRS read models
- Data warehouse integration

---

# Decision Metrics

| Metric | Target |
|---------|--------|
| Database Availability | >99.99% |
| P95 Query Response | <100 ms |
| Transaction Success Rate | >99.9% |
| Read Replica Lag | <1 second |
| Failed Transactions | <0.1% |
| Query Optimization Coverage | >90% |

The decision should be revisited if:

- Read/write patterns become predominantly key-value.
- Data relationships reduce significantly.
- Horizontal scaling requirements exceed relational capabilities.
- Business domains become independently owned.

---

# References

## Official Documentation

- [Amazon Aurora Documentation](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html?utm_source=chatgpt.com)
- [Amazon DynamoDB Documentation](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html?utm_source=chatgpt.com)
- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html?utm_source=chatgpt.com)
- [AWS Database Blog](https://aws.amazon.com/blogs/database/?utm_source=chatgpt.com)
