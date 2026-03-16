---
name: feature-based-design
description: Pragmatic grouping by business domain - optimized for speed, low cognitive load, and modular growth.
allowed-tools: Read, Write, Edit
priority: high
version: 1.0
---

# Feature-Based Design

**Context:** Architecture for frontend.
**Focus:** "Cohesion over Layers". Organizing the application by business modules (Features) to make the codebase intuitive and easy to navigate for small to medium-sized teams.
**Strengths:** Rapid development, low learning curve, high discoverability, and "Evolutionary" (easy to migrate to Micro-frontends later).
**Weaknesses:** Risk of "Shared Folder" bloat, potential for circular dependencies between features if not monitored.

---

## 1 The Modular Structure
The agent must enforce the organization of code into self-contained business modules:

1.  **Global Shared (src/components, src/hooks, src/utils):**
    * Truly generic elements (e.g., `Button`, `Input`, `useDebounce`).
    * **Constraint:** Zero business logic. Must be usable in any future project.
2.  **Feature Modules (src/modules/[feature-name]):**
    * The core of the app. Each folder is a mini-application.
    * Internal structure:
        - `/components`: UI elements exclusive to this feature.
        - `/hooks`: Logic and state management (Zustand/Context) exclusive to this feature.
        - `/services`: API calls and data transformation for this feature.
        - `/[Feature]Page.tsx`: The entry point/container for the module.
3.  **App Core (src/main.tsx, src/routes):**
    * Orchestrates the modules. Handles routing and global providers.

---

## 2 Mandatory Heuristics
-   **Local-First:** If a component is only used in "Checkout", it **must** live in `modules/checkout/components`. Moving to `shared` is a last resort.
-   **The "Rule of Three":** Only move a component or utility to the global `shared` folder if it is used by at least three different modules.
-   **Flat Module Hierarchy:** Modules should ideally be siblings. Avoid deep nesting of modules within modules to keep the pathing simple.
-   **Barrel Exports:** Each module should use an `index.ts` to expose its Page or public components, hiding internal implementation details.

---

## 3 Rejection Criteria (Hard Fails)
-   **Technical Layer Folders:** Creating top-level folders like `/controllers`, `/models`, or `/views` (The "Categorization by Type" anti-pattern).
-   **Cross-Feature Leakage:** `modules/billing` importing a private component from `modules/auth`. Only public API (via index.ts) or Shared elements can be imported.
-   **Fat Shared Folder:** Putting feature-specific logic (e.g., `ProductPriceFormatter`) inside the global `utils` folder.
-   **Circular Dependencies:** Module A importing Module B, which in turn imports Module A.

---

## 4 Review Checklist
* [ ] Is the code for this feature contained within a single directory?
* [ ] Can I delete a module folder without breaking the compilation of other modules (except for shared utils)?
* [ ] Does the `shared` folder contain only "Dumb Components" and generic helpers?
* [ ] Is the data fetching logic (API) located within the module it serves?
* [ ] Is the project structure flat enough that a new dev can find a feature in < 5 seconds?