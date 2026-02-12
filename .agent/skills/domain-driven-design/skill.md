---
name: domain-driven-design
description: Guardian of **Model-Driven Design**. The primary objective is to capture the domain expert's mental model into the software.
allowed-tools: Read, Write, Edit
version: 1.0
priority: high
---

# Domain Driven Design (DDD) - Pragmatic AI Coding Standards

## 0. The "Why" (The Philosophy)

**The Goal:** The primary objective is to capture the **Domain Expert's Mental Model** into the software.
* **Code as Communication:** The code is a form of documentation. If a developer reads the code, they should understand the business rules without needing to ask the expert.
* **Ubiquitous Language:** The language used in the code must strictly match the language spoken by domain experts *in this specific context*.

---

## 1. Strategic Design & Bounded Contexts (The Environment)

Before coding, you MUST identify the **Bounded Context**.
* **Context Boundaries:** A model is valid *only* within its context. Do not try to create a "Universal Model".
* **Polysemy Check:** If a term (e.g., "Ticket") means different things in different parts of the system, split them into separate models (e.g., `SupportTicket` vs `LotteryTicket`) or separate Bounded Contexts.
* **Ubiquitous Language:** The language used in the code must strictly match the language spoken by domain experts *in this specific context*.

---

## 2. Tactical Steps

It must follow these steps in order:
1.  **Map Ubiquitous Language:** Extract business terms and use them for all class and method names. Avoid technical jargon (e.g., use `placeOrder()` not `saveOrderToDB()`).
2.  **Define Invariants:** Identify the rules that must *always* be true for the domain objects.
3.  **Domain-First Generation:** Generate the **Domain Layer** (Aggregates, Entities, VOs) before touching Infrastructure or Application layers.
4.  **Encapsulation Check:** Ensure no setter or property allows an object to enter an invalid state.

---

## 3. Layered Architecture (Strict Separation)

| Layer | Responsibility | Restriction |
| :--- | :--- | :--- |
| **Domain** | Entities, Value Objects, Aggregates, Domain Services, and Repository Interfaces. | **Strictly Forbidden:** Importing ORMs (TypeORM/Hibernate) or Web logic. |
| **Application** | Use Cases, transaction orchestration, and DTO mapping. | **Strictly Forbidden:** Containing business decision logic or state transitions. |
| **Infrastructure** | Repository implementations, API adapters, messaging, and DB schemas. | Technical details must be encapsulated here and hidden from the core. |

---

## 4. Structural Comparison: VO vs. Entity vs. Aggregate

To prevent "God Objects" and memory leakage, the agent must strictly distinguish between **Local Entities**, **Aggregate Roots**, and **Value Objects**. Mixing their responsibilities is a **High-Severity Architectural Violation**.

| Feature | **Value Object (VO)** | **Local Entity** | **Aggregate Root** |
| :--- | :--- | :--- | :--- |
| **Identity** | None. Defined by attributes. | Local. Unique within the Root. | **Global**. Unique system-wide. |
| **Immutability** | **Yes**. Always replace, never modify. | No. State changes over time. | No. State changes over time. |
| **Persistence** | Stored as values of the Parent. | Part of the Root's document/table. | Has its own **Repository**. |
| **Validation** | Self-validating on creation. | Validated by the Root. | Guarantees all internal rules. |
| **Relationship** | Shared by value. | Owned by one Root. | References others by **ID ONLY**. |

---

## 5. Implementation Patterns

#### A. Aggregate Roots
* **State Encapsulation:** Attributes must be `private` or `readonly`. Direct access to state is discouraged.
* **Behavior over Attributes:** Methods must name business intentions (e.g., `confirmPayment()` instead of `setStatus('PAID')`).
* **Consistency Boundaries:** The Aggregate Root is the transactional boundary. It ensures all internal invariants are satisfied before state changes.
* **Atomic Consistency:** Only the data that *must* be consistent in a single transaction should be in the same Aggregate.
* **Interaction Rules:**
    1.  An Aggregate acts on itself.
    2.  An Aggregate can hold a reference to the **ID** (Identity) of another Aggregate, but **NEVER** the object reference itself.
    3.  **Eventual Consistency:** To update another Aggregate, publish a **Domain Event** (e.g., `OrderPlacedEvent`) instead of modifying it directly.

#### B. Local Entities
* **State Encapsulation:** Attributes must be `private` or `readonly`.
* **Behavior over Attributes:** Methods must name business intentions.
* **Lifecycle:** They exist only inside an Aggregate. If the Root is deleted, they are deleted.
* **Access:** Cannot be retrieved directly via Repository; must be accessed through the Root.

#### C. Value Objects
* **Self-Validation:** An "invalid" VO cannot exist. Validation happens in the constructor.
* **Immutability:** VOs cannot change. Any modification must return a new instance.
* **Value Equality:** Comparison must be based on internal attributes, not memory reference. Must implement an `equals()` method.
* **Examples:** `Email`, `Address`, `SKU`, `DateRange`, `Money`.

#### D. Domain Services
* **Definition:** Used when an operation involves multiple Aggregates or doesn't naturally fit into the responsibility of a single Entity.
* **Statelessness:** A Domain Service must be stateless. It performs an action and returns a result without maintaining its own internal state.
* **Pure Business Logic:** It should only contain domain logic. Interaction with DB/API must be via Interfaces defined in the Domain.

#### E. Repositories
* **Roots Only:** Strictly create Repositories for Aggregate Roots. **Never** for Local Entities or VOs.
* **Interface in Domain:** The repository signature must only use Domain Types.
* **Collection Metaphor:** The repository should act like an in-memory collection. Use names like `add()`, `remove()`, `get(id)`. Avoid SQL-like names like `insert()` or `update()`.

#### F. Factories
* **Complex Creation:** If creating an Aggregate requires complex assembly, validation of external data, or invariants that involve multiple steps, use a **Factory** (method or class).
* **Separation:** Keep the Entity constructor simple; move complex creation logic to the Factory.

#### G. Specifications (Explicit Rules)
* **Complex Rules:** When a business rule is a complex boolean logic (e.g., "Is this customer eligible for a refund?"), do not bury it in an `if` statement inside a Service or Entity.
* **Explicit Class:** Create a specific class (e.g., `RefundEligibilitySpecification`) with a method `isSatisfiedBy(candidate)`.
* **Reusability:** This allows the rule to be tested in isolation and reused for validation or query filtering.

---

## 6. Anti-Patterns to Block (Watchlist)

It must **flag** or **refactor** if the following are detected:
1.  **Anemic Domain Model:** Domain classes that are just property bags (getters/setters only).
2.  **Logic Leakage:** Business rules leaking into Controllers, Application Services, or UI.
3.  **God Aggregates:** Overly large aggregates attempting to manage unrelated entities.
4.  **Primitive Obsession:** Using `string` or `number` for domain concepts like `Email` or `SKU`.

---

## 7. Post-Generation Quality Gate
- [ ] Is the model rich or just a data structure (anemic)?
- [ ] Are Value Objects used to describe entity properties?
- [ ] Does the Domain logic work without any external library/DB dependency?
- [ ] Is persistence logic leaking into the domain?
- [ ] Does the method name describe a business intention (e.g., `enrollStudent` vs `updateStatus`)?
- [ ] Do method names sound like business actions (Ubiquitous Language)?
- [ ] Is the Domain Service stateless and focused only on orchestration?
- [ ] Are all state transitions protected by internal business rules?
- [ ] Are all Value Objects immutable and self-validating?