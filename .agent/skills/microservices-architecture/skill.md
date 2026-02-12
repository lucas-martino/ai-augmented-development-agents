---
name: microservices-architecture
description: Guardian of Microservices Architecture and Event-Driven Design (EDD).
allowed-tools: Read, Write, Edit
version: 1.0
priority: high
---

## CORE DESIGN PRINCIPLES
- **Database per Service:** Every service owns its private data. No shared schemas.
- **High Cohesion/Loose Coupling:** Group related functions; minimize inter-service dependencies.
- **API-First Contract:** Define interfaces (OpenAPI/AsyncAPI) before implementation.
- **Event-Driven Preference:** Prioritize asynchronous communication for state changes to ensure system resilience.
- **Observability by Design:** Mandatory Distributed Tracing (Correlation IDs) for all cross-service requests.

---

## 1 EXECUTION FRAMEWORK

### A. Strategic Decomposition
- Analyze the domain to identify **Bounded Contexts**.
- Map **Aggregates** and their boundaries.
- Define the **Context Map** (Upstream/Downstream, Customer-Supplier, etc.).

### B. Communication Modeling
- **Edge Gateway:** Define entry points, Authentication (JWT/OIDC), Rate Limiting, and Request Routing.
- **Sync (REST/gRPC):** Use for reads operations or where immediate consistency is a hard requirement. **Must** include Circuit Breaker and Retry patterns.
- **Async (Pub/Sub):** Use for commands and side effects to ensure eventual consistency.
- **Saga Pattern:** Propose Choreography or Orchestration for transactions spanning multiple services.

### C. Service Technical Specification
For each microservice identified, generate the following structure:
- **Service Name:** [e.g., Order-Management-Service]
- **Business Capability:** The specific "Job to be Done".
- **Data Store:** Specific DB engine (e.g., NoSQL for high-write, Relational for complex queries).
- **Primary Adapters (Inbound):** REST Controllers, Message Listeners.
- **Secondary Adapters (Outbound):** Repository Impls, Event Producers, External Gateway Clients.
- **Strategic Pattern:** (e.g., CQRS, Event Sourcing, or Simple CRUD).

---

## 2. Event-Driven Design (EDD)

### A. Event Classification
The agent must distinguish between different types of events to avoid "API-over-the-wire" anti-patterns:
* **Domain Events:** Fine-grained events used within a Bounded Context (e.g., OrderValidated).
* **Integration Events:** Coarse-grained events used to communicate between different microservices (e.g., OrderPlaced).
* **State Transfer Events (Event Carried State Transfer):** Events containing the full snapshot of data so the consumer doesn't need to "call back" the producer for details.

### B. Interaction Patterns
The skill should propose the best choreography based on the use case:
* **Pub/Sub Pattern:** One producer, multiple independent consumers.
* **Event Sourcing:** Storing the state as a sequence of events rather than just the current snapshot.
* **Command Query Responsibility Segregation (CQRS):** Separating the write model (Event Store) from the read model (Projections/Elasticsearch).

### C. Reliability & Consistency Patterns
When designing EDD, the agent must include:
* **Transactional Outbox Pattern:** Ensuring an event is only published if the database transaction succeeds.
* **Idempotency:** Designing consumers to handle the same event multiple times without side effects.
* **Dead Letter Queues (DLQ):** Handling malformed or unprocessable events without blocking the pipeline.

### D. Strategic Tooling Selection
The agent should recommend tools based on throughput and requirements:
* **Kafka:** For high-throughput, log-based streaming and event replayability.
* **RabbitMQ/SQS:** For complex routing and traditional task queuing.
* **Redis Streams:** For lightweight, low-latency eventing.

---

## 3. GUARDRAILS & OBSERVABILITY
* **Anti-Pattern Alert:** Explicitly refuse "Shared Databases" or "Distributed Transactions (2PC)".
* **Granularity Check:** Warn if a service is a "Nano-service" (too small) or a "Distributed Monolith" (too coupled).
* **Standardized Health:** Every service must implement `/health`, `/metrics` (Prometheus), and `/ready` endpoints.
* **Tracing:** All logs and spans must propagate the `Correlation-ID`.

---

## REQUIRED OUTPUT FORMAT
When designing, always include:
1. **System Overview:** A high-level summary of the architecture.
2. **Mermaid Diagram:** A `graph TD` or `sequenceDiagram` showing service interactions.
3. **Service Manifest:** A breakdown of each service using the "Service Technical Specification" above.
4. **Data Flow:** Explanation of how a primary user request travels through the system.
5. **Failure Mode Analysis:** Brief table on how the system reacts if a specific service or broker fails.