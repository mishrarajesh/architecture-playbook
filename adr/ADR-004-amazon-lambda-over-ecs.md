# ADR-004

# Choosing AWS Lambda over Amazon ECS/Fargate for Event-Driven Workloads

## Metadata

| Item | Value |
|------|-------|
| Status | Accepted |
| Category | Compute Platform |
| Decision Type | Compute Strategy |
| Complexity | Medium |
| Impact | High |
| Primary Concern | Serverless Compute |
| Applicable To | Event-Driven Cloud Platforms |
| Version | 1.0 |

---

# Problem Statement

The platform requires a compute platform capable of processing asynchronous business events such as notifications, AI requests, workflow execution, document processing, and audit logging.

The engineering team evaluated AWS Lambda and Amazon ECS/Fargate to determine the most suitable execution environment.

The selected solution should minimize operational effort while providing scalability, reliability, security, and cost efficiency.

---

# Context

The platform is built primarily using managed AWS services.

Core services include:

- Amazon EventBridge
- Amazon SQS
- Amazon API Gateway
- Amazon Bedrock
- Amazon DynamoDB
- Amazon Aurora
- Amazon CloudWatch

Workloads are primarily event-driven.

Typical executions include:

- Notification processing
- AI summarization
- File processing
- Audit event generation
- Workflow orchestration

Individual requests generally execute within a few seconds.

Traffic patterns are highly variable with occasional spikes.

---

# Practical Experience

During cloud-native platform development, many workloads were asynchronous and short-lived. Events arriving through EventBridge or SQS typically triggered isolated units of work such as AI processing, notification generation, and background workflows.

Using AWS Lambda allowed these workloads to scale automatically without maintaining container infrastructure. Operational responsibilities such as capacity planning, patching, operating systems, and cluster management were eliminated, allowing the engineering team to focus on application logic.

Container-based services remained appropriate for long-running APIs and workloads requiring persistent processes.

---

# Decision Drivers

| Driver | Importance |
|---------|------------|
| Operational Simplicity | High |
| Automatic Scaling | High |
| Cost Efficiency | High |
| Startup Latency | Medium |
| Development Speed | High |
| Infrastructure Management | High |
| Long-running Workloads | Medium |
| AWS Integration | High |

---

# Options Considered

## Option A

AWS Lambda

---

## Option B

Amazon ECS/Fargate

---

# Decision

AWS Lambda will be used for asynchronous event-driven processing.

Amazon ECS/Fargate will continue to be used for long-running services, APIs, and workloads requiring persistent execution.

---

# Rationale

The majority of platform workloads are stateless, event-driven, and short-lived.

Examples include:

- Process AI requests
- Send notifications
- Generate reports
- Process uploaded documents
- Execute background workflows

Lambda aligns naturally with this execution model by providing:

- Automatic scaling
- Pay-per-use pricing
- Native integration with EventBridge
- Native integration with SQS
- Built-in retry behavior
- Minimal operational overhead

Container orchestration would introduce additional operational responsibilities without providing proportional benefits for these workloads.

---

# Comparison

| Capability | AWS Lambda | Amazon ECS/Fargate |
|------------|------------|--------------------|
| Infrastructure Management | None | Minimal |
| Automatic Scaling | Excellent | Excellent |
| Cold Starts | Possible | None |
| Long-running Tasks | Limited | Excellent |
| Pay-per-use | Excellent | Moderate |
| Startup Time | Seconds | Minutes |
| EventBridge Integration | Native | Good |
| SQS Integration | Native | Good |
| Maximum Execution Time | 15 Minutes | Unlimited |
| Operational Complexity | Very Low | Medium |

---

# Why ECS/Fargate Was Not Selected

Container-based execution remains an excellent choice for many workloads.

However, introducing containers for short-lived asynchronous processing would require managing:

- Container images
- Deployment pipelines
- Task definitions
- Cluster monitoring
- Capacity planning
- Image patching
- Networking configuration

These responsibilities provide limited value for lightweight event processing.

---

# Trade-offs

## Benefits

- No server management
- Automatic scaling
- Reduced operational effort
- Faster feature delivery
- Lower idle cost
- Native AWS integrations
- Simplified deployments

---

## Limitations

- Cold starts
- Maximum execution duration
- Stateless execution
- Memory limitations
- Execution environment constraints

---

# Risks

## Risk

Cold start latency affects user experience.

**Mitigation**

Keep Lambda functions lightweight, provision concurrency for latency-sensitive workloads, and minimize package size.

---

## Risk

Execution exceeds Lambda limits.

**Mitigation**

Move long-running processing to ECS/Fargate or AWS Step Functions.

---

## Risk

Growing number of Lambda functions increases operational complexity.

**Mitigation**

Adopt consistent naming, tagging, infrastructure as code, and centralized observability.

---

# Consequences

### Positive

- Reduced infrastructure management
- Lower operational cost
- Improved scalability
- Faster deployments
- Better developer productivity

### Negative

- Cold start considerations
- Runtime limitations
- Increased dependency on AWS managed services

---

# Architecture

## Event Processing

```
API Gateway

      |

EventBridge

      |
+----------------------+

|      Lambda          |

+----------+-----------+
           |

        Amazon SQS

           |
+----------v-----------+

|      Lambda          |

|   AI Processing      |

+----------+-----------+
           |

      Amazon Bedrock

           |
+----------v-----------+

|      Lambda          |

| Notification Service |

+----------------------+
```

---

## Alternative (Containers)

```
API Gateway

      |

Application Load Balancer

      |

Amazon ECS Service

      |

Amazon ECS Tasks

      |

Database
```

---

# When NOT to Choose AWS Lambda

Lambda is generally not suitable when:

- Processing exceeds 15 minutes.
- Persistent TCP connections are required.
- High-performance computing is required.
- GPU workloads are required.
- Stateful processing is required.
- Large binaries or custom operating system dependencies are needed.
- Long-running streaming applications are involved.

ECS/Fargate is generally a better fit for these scenarios.

---

# Lessons Learned

Serverless computing is most effective when the workload is naturally event-driven, stateless, and short-lived.

Choosing Lambda reduced operational overhead and accelerated development while allowing infrastructure to scale automatically with demand.

Container platforms remain an important part of the architecture and should be selected based on workload characteristics rather than organizational preference.

---

# Future Considerations

The platform should periodically evaluate:

- Lambda SnapStart
- Provisioned Concurrency
- Step Functions for orchestration
- ECS for long-running AI workloads
- Cost optimization
- Multi-region deployments
- Observability improvements

---

# Decision Metrics

| Metric | Target |
|---------|--------|
| Average Lambda Duration | < 5 seconds |
| Cold Start Rate | < 5% |
| Function Error Rate | < 1% |
| Deployment Frequency | Weekly or Better |
| Infrastructure Incidents | Reduced over Time |
| Idle Infrastructure Cost | Near Zero |
| Event Processing Success Rate | > 99.9% |

The decision should be revisited if:

- Average execution time consistently approaches Lambda limits.
- Container workloads become the dominant execution model.
- GPU acceleration becomes a core requirement.
- Operational costs exceed container-based alternatives.

---

# References

- [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/?utm_source=chatgpt.com)
- [Amazon ECS Documentation](https://docs.aws.amazon.com/ecs/?utm_source=chatgpt.com)
- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html?utm_source=chatgpt.com)
- [AWS Serverless Lens](https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/welcome.html?utm_source=chatgpt.com)
- [AWS Compute Blog](https://aws.amazon.com/blogs/compute/)

---
