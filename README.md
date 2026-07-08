# Architecture Playbook

This repository documents architecture decisions commonly encountered when designing modern cloud-native platforms.

Rather than prescribing a single "best" solution, each Architecture Decision Record (ADR) explains the business context, available options, trade-offs, risks, and the reasoning behind the final decision.

The objective is to demonstrate architectural thinking and decision-making rather than implementation details.

## ADR Index

| ADR | Topic 
|------|-------
| ADR-001 | [EventBridge vs Kafka](./adr/ADR-001-eventbridge-vs-kafka.md) 
| ADR-002 | [Modular Monolith over Microservices](./adr/ADR-002-modular-monolith-over-mircroservices.md)
| ADR-003 | [Graphql vs Rest](./adr/ADR-003-graphql-vs-rest.md) 
| ADR-004 | [Amazon Lambda over Ecs](./adr/ADR-004-amazon-lambda-over-ecs.md) 
| ADR-005 | [Amazon Aurora over Dynamodb for transactions](./adr/ADR-005-amazon-aurora_over_dynamodb_for_transactions.md)

## ADR Format

Each ADR contains:

- Problem Statement
- Context
- Decision Drivers
- Options Considered
- Decision
- Trade-offs
- Risks
- Consequences
- When NOT to use the selected approach
- Lessons Learned

## Disclaimer

These Architecture Decision Records (ADRs) are intended as a professional knowledge base that documents architectural patterns, design decisions, and trade-offs encountered in cloud-native application development. Some ADRs are inspired by production experience, while others explore realistic architecture scenarios. They are not project-specific records and do not disclose confidential information.
