---
name: clean-architecture
description: Pragmatic coding standards - concise, direct, no over-engineering, no unnecessary comments
allowed-tools: Read, Write, Edit
priority: high
version: 1.0
---

# Clean Architecture

**Focus:** Keep the business logic "Pure" and the infrastructure "Pluggable". Strict layering and dependency rules pointing inward.
**Strengths:** Highly prescriptive, UI/DB independence, and maximum stability of the Core (Entities).
**Weaknesses:** High abstraction levels, potential "Class Explosion," and risk of over-engineering simple features.

---

## 1 The Layered Architecture
The agent must enforce the following separation of concerns, moving from the center (most abstract) to the periphery (most concrete):
1. **Entities (Enterprise Business Rules):** - Core business objects and logic.
   - **Constraint:** Zero dependencies on any external library, database, or framework.
2. **Use Cases (Application Business Rules):** - Orchestrates the flow of data to/from Entities.
   - Defines the "what" the system does.
3. **Interface Adapters (Controllers/Gateways/Presenters):** - Converts data from Use Case/Entity format to external formats (and vice versa).
   - This is where Repositories and API Controllers live.
4. **Frameworks & Drivers (External Details):** - The Database (Postgres, MongoDB), Web Frameworks (Express, Spring, FastAPI), and UI.

---

## 2 Mandatory Heuristics
- **Dependency Rule:** Dependencies MUST only point inwards.
- **Screaming Architecture:** The folder structure must reveal the application's intent (e.g., `/billing`, `/catalog`) rather than technical types.
- **Dependency Inversion:** High-level modules (Use Cases) must not depend on low-level modules (DB Adapters). Both must depend on abstractions (Interfaces/Ports).
- **Data Isolation:** Use Mappers to convert DB Models to Domain Entities at the boundary. NO leakage of ORM/Framework objects into the inner circles.

---

## 3 Rejection Criteria (Hard Fails)
- Importing frameworks (Spring, Express, FastAPI) or libraries (Prisma, SQLAlchemy) inside Entities/Use Cases.
- Business logic or validation leaking into Controllers.
- Use Cases returning HTTP-specific codes or Web-related objects.
- Entities inheriting from ORM Base classes.

---

## 4 Review Checklist
* [ ] Does the folder structure reflect business features?
* [ ] Are all external services (DB, Auth, Email) accessed via Interfaces/Ports?
* [ ] Is the Domain layer 100% free of framework-specific decorators/annotations?
* [ ] Can I replace the Web Framework or Database without touching the Use Cases?