---
name: hexagonal-architecture
description: Guardian of Hexagonal Architecture (Ports and Adapters pattern). Decouples application logic from external technologies, frameworks, and delivery mechanisms.
allowed-tools: Read, Write, Edit
version: 1.0
priority: high
---

# Hexagonal Architecture - Pragmatic Standards

> **CORE PRINCIPLE:** The application core is technology-agnostic. External tools are interchangeable details. Dependencies always point **INWARD**.

---

## 1. The Separation of Concerns

| Layer | Responsibility | Components |
| :--- | :--- | :--- |
| **Inside (Core)** | Application Logic & Business Rules | Use Cases, Services, Interactors, Models |
| **Ports (Interfaces)** | Contract for communication with the Core | Inbound Ports (API), Outbound Ports (SPI) |
| **Outside (Adapters)** | Translation between external world and Core | Controllers, Repositories, Mailers, CLI |

---

## 2. Dependency Flow Rule

**Dependency Direction:** Always **INWARD** toward the Core.
* The Core must NEVER import anything from an Adapter (e.g., no Express types, no ORM decorators).
* Outer layers depend on Interfaces (Ports) defined inside the Core.

---

## 3. Ports: The Boundary Contracts

### A. Inbound Ports (Driving)
* **Definition:** Interfaces that define what the application *can do*.
* **Usage:** Called by Controllers or CLI.
* **Constraint:** Must only use simple DTOs or Core Models.

### B. Outbound Ports (Driven)
* **Definition:** Interfaces that define what the application *needs* from the outside (Persistence, Messaging).
* **Usage:** Implemented by Infrastructure (Adapters).
* **Constraint:** Signature must be technology-agnostic (e.g., `saveUser()` not `insertIntoMongo()`).

---

## 4. Adapters: The Translators

### A. Driving Adapters (Input)
* **Examples:** REST Controllers, GraphQL Resolvers, CLI commands.
* **Job:** Convert external requests (HTTP, JSON) into Core-friendly data and call an Inbound Port.

### B. Driven Adapters (Output)
* **Examples:** SQL Repositories, Redis Cache, AWS S3 Client.
* **Job:** Implement an Outbound Port, converting Core data into specific technology formats.

---

## 5. Implementation Mapping (Data Integrity)

To avoid "Leaky Abstractions", use **Mappers**:
1.  **Input Mapper:** Controller Request -> Use Case DTO.
2.  **Output Mapper:** Use Case Result -> UI Response.
3.  **Persistence Mapper:** Core Model <-> DB Schema/Entity.

> **Rule:** Do not use the same class for DB persistence and Application logic.

---

## 6. Directory Structure (Agnostic Layout)

Esta estrutura foca na separação por **propósito** em vez de frameworks, permitindo evolução para DDD (subdomínios) ou Clean Architecture.

```text
src/
├── @shared/               # Global types, agnostic utilities, and Kernel
├── core/                  # The Core (Inside)
│   ├── domain/            # Business logic flows, Logic-heavy objects (Agnostic)
│   ├── application/       # Application logic flows, Use Cases / Interactors
│   └── ports/             # INTERFACES (contracts)
│       ├── in/            # Driving Ports (input)
│       └── out/           # Driven Ports (output)
├── infrastructure/        # The Adapters (Outside)
│   ├── adapters/          # Implementation of Outbound Ports
│   │   ├── persistence/   # DB (TypeORM, Prisma, etc.)
│   │   └── external/      # API Clients, Mailers
│   └── transport/         # Entry points (Web, CLI, Grpc, Events); Driving Adapters (Controllers, Routes)
└── main                   # Composition Root and Dependency Injection Setup
```


## 7. Dependency Injection & Composition Root
To ensure total decoupling, the Core never instantiates its own Adapters.
* **The Rule:** The main/ folder (or a dedicated DI container) is the only place allowed to couple the Core with Infrastructure implementations.
* **The Mechanism:** Use Constructor-based Dependency Injection.
* **The Check:** If a Use Case or Domain Service uses the new keyword to instantiate a Repository or an external Service, the architecture is violated.
* **Transaction Management:** Transactions must be orchestrated at the Application Layer using an abstract Unit of Work or Transaction Port, ensuring the Core remains agnostic of the specific commit/rollback implementation.

---

## 8. Error Handling & Exception Mapping
To maintain a pure Core, technological failures must not pollute the business logic.
* **Rule:** Infrastructure exceptions (e.g., SQLException, AxiosError) MUST NOT pass through Outbound Ports.
* **Mechanism:** The Adapter must catch technical exceptions and wrap/map them into Domain Exceptions defined inside the Core.
* **Flow:** Adapter (Catch ToolError) -> Map to DomainError -> Throw -> Use Case (Handle DomainError).

---

## 9. Testability & Mocking Strategy
The quality of the architecture is directly measured by how easily the Core can be tested in isolation.
* **Core Unit Tests:** Must test application/ and domain/ without complex mocking frameworks or heavy dependencies. Use In-Memory Fakes (e.g., InMemoryUserRepository) to satisfy ports/out.
* **Adapter Integration Tests:** Focus on testing the real implementation of adapters against actual resources (using Docker, TestContainers, or dev-databases).
* **The 100% Rule:** If testing a Use Case requires starting a database, an HTTP server, or any external IO, the Hexagonal Architecture has been violated.
* **Validation:** Use Cases should be triggered via Inbound Ports during testing to ensure the contract is respected.

---

## 10. Anti-Patterns to Block (Watchlist)
- **The "Big Ball of Mud":** Business logic directly inside an Express/Fastify controller.
- **ORM Contamination:** Putting decorators on models inside the application/folder.
- **Direct Coupling:** The Core importing a specific library (e.g., axios or knex) instead of using a Port.
- **Shared DTOs:** Using the same DTO for the Web API and the Database.

---

## 11. Quality Gate (Hexagonal)
- [ ] Core is free of framework-specific imports?
- [ ] All external integrations are defined as interfaces in ports/out/?
- [ ] Business logic is testable with zero IO/Database?
- [ ] Dependencies point only toward the Core?
- [ ] All infrastructure errors are mapped to Domain Exceptions?    