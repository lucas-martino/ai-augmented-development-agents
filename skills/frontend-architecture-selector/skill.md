---
name: frontend-architecture-selector
description: Decision engine to select the best frontend architecture based on team scale, business complexity, and operational needs.
tools: Read, Edit, Write
skills: feature-based-design, feature-sliced-design, component-driven-ui-design
priority: high
version: 1.0
---

# Frontend Architecture Selector (Decision Tree)

**Focus:** Pragmatic selection of frontend organization patterns.
**Goal:** Prevent "Over-engineering" for small teams and "Spaghetti Code" for large organizations.

---

## 1. Decision Matrix (The Core)

The agent must analyze the project against these four primary drivers:

### **A: Complexity of the Business Domain**
* **Low < 10 pages or MVP:** Feature-Based
* **Medium (CRUD, landing pages, tools):** Component-Driven UI Design
    - **Is the team frustrated with "where to put this file"?** Upgrade to **FSD**.
* **High (Financial systems, complex workflows, heavy state):** Feature-Sliced Design (FSD)

### **B: UI Consistency Needs**
* **Unique UI per project:** Standard CSS/Components
* **Shared UI across multiple products:** Component-Driven UI Design (as a Design System library)

---

## 2. Architecture Profiles

### **Profile 1: Feature-Based (The "Keep it Simple" Choice)**
* **Use when:** You need speed, have a small team, and want a "Standard React/Vue" feel.
* **Logic:** Group files by "Feature" (e.g., `/cart`, `/auth`).
* **Bail out if:** The "Shared" folder becomes a dumping ground for everything.

### **Profile 2: Feature-Sliced Design (The "Enterprise Standard")**
* **Use when:** You have a complex domain and need strict rules to prevent coupling.
* **Logic:** 7 standardized layers (Shared -> Entities -> Features -> ...).
* **Bail out if:** The team is struggling with the learning curve or the project is an MVP.

### **Profile 3: Component-Driven UI Design (The "Design System" Choice)**
* **Use when:** You are building a UI Library or a White-label product.
* **Logic:** Base UI -> Simple Groups -> Complex Sections -> Layouts -> Instances.
* **Constraint:** Usually used *inside* other architectures (e.g., within the `Shared` layer of FSD or the `components` folder of Feature-Based).