---
name: modular-monolith-architecture
description: Architect for highly decoupled monolithic applications designed for future microservices migration.
allowed-tools: Read, Write, Edit
priority: high
version: 1.0
---

# Modular Monolith Architecture

**Focus:** Logical separation with operational simplicity.
**Strengths:** Deployment velocity, high performance (in-memory calls), and safer refactoring.
**Weaknesses:** Unified scaling constraints and risk of architectural erosion without strict enforcement.

---

## 0. CORE DESIGN PRINCIPLES
- **Logical Isolation:** Modules must be physically separated in the folder structure (e.g., `src/modules/[module-name]`).
- **Encapsulated Data:** Each module must "own" its tables. Avoid JOINs across module schemas at the SQL level.
- **In-Process Communication:** Modules talk via internal Interfaces/Mediators, never by reaching into another module's internal classes.
- **Dependency Rule:** Modules can only depend on a shared "Core/Common" library or use strictly defined Public APIs of other modules.
- **Clean/Hexagonal at Module Level:** Each module is internally structured with its own Ports and Adapters.

---

## 1. STRATEGIC MODULARIZATION

### A. Modular Boundary Definition
- Use **Bounded Contexts** to define module boundaries.
- **Strict Encapsulation:** Only a small `Public API` (Facade or Service) is visible to other modules. Everything else is `internal`/`private`.

### B. Shared Kernel Governance (Anti-Corruption)
- **Minimalist Core:** The "Shared/Core" library must only contain cross-cutting concerns (logging, results, base exceptions). 
- **No Business Logic in Core:** Business rules that apply to two modules must be duplicated or handled via event synchronization, never placed in "Common" to avoid hidden coupling.

### C. Communication Strategy (Pre-Microservices)
- **Direct Calls:** Allowed only via Public Interfaces (synchronous in-memory calls).
- **Internal Events:** Use an in-memory Event Bus (e.g., MediatR in .NET, Spring Events in Java) to mimic future Pub/Sub.
- **Transactional Consistency:** Use a single DB transaction for cross-module calls *only* when absolutely necessary, but flag these as "Migration Risks".

---

## 2. SERVICE TECHNICAL SPECIFICATION (Per Module)
For each module identified, generate:
- **Module Name:** [e.g., Payment-Module]
- **Public API:** List of exposed methods/interfaces.
- **Internal Use Cases:** Core logic inside the module.
- **Isolated Tables:** List of tables managed by this module.
- **External Dependencies:** What other modules or third-party APIs it consumes.

---

## 3. MIGRATION READINESS (The "Microservices-Ready" Checklist)

### A. Data Isolation Patterns
- **Database Schema per Module:** Even if in the same DB, use prefixes or schemas (e.g., `order_schema.orders`, `inventory_schema.stocks`).
- **No Cross-Module JOINs:** Cross-module data must be retrieved via the module's API or eventually consistent projections.

### B. Contract-Driven Design
- **Internal Contract Tests:** Every module must have tests that validate its `Public API` against its consumers. If the API signature or DTO changes, the consumer's contract test must fail immediately.
- Define **Data Transfer Objects (DTOs)** for inter-module communication to avoid leaking internal Domain Entities.

---

## 4. GUARDRAILS
- **Refuse Spaghetti Dependencies:** If Module A depends on B and B depends on A, force a redesign (Circular Dependency).
- **No Shared Entities:** Every module must have its own representation of a concept (e.g., `Order` in Sales module vs `Order` in Shipping module).
- **Event-Driven Foundation:** Strongly encourage using internal events for side effects to ease future transition to a Message Broker (Kafka/RabbitMQ).

---

## REQUIRED OUTPUT FORMAT
1. **Monolith Structure Map:** Visual representation of modules (Mermaid `graph LR`).
2. **Inter-Module Communication Table:** How Module A talks to Module B (Call vs Event).
3. **Internal Contract Definition:** Specification of DTOs used for inter-module communication.
4. **Module Manifest:** Breakdown of each module using the "Service Technical Specification" above.
5. **Migration Path:** Short guide on how to extract the most "ready" module into a microservice.