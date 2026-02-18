---
name: component-driven-ui-architecture
description: Atomic Design - maximizing reusability, consistency, and visual hierarchy.
allowed-tools: Read, Write, Edit
priority: high
version: 1.0
---

# Component-driven UI architecture (Atomic Design)

**Context:** Enterprise Use: Architecture for frontend, excellent for ensuring visual consistency.
**Focus:** "Compositional UI". Breaking down interfaces into fundamental building blocks to create a robust, scalable Design System.
**Strengths:** Enforces visual consistency, simplifies testing, facilitates "White-Labeling", and creates a shared vocabulary between Design and Dev.
**Weaknesses:** Can feel tedious for simple pages, "naming things" becomes hard (is it a molecule or organism?), and risk of "Prop Drilling" if not managed well.

---

## 1 The Component Hierarchy
The agent must enforce the strict separation of components based on their complexity and responsibility:

1.  **Base UI (Atoms):**
    * Indivisible UI elements (e.g., `Button`, `Input`, `Label`, `Icon`).
    * **Constraint:** strictly visual, **zero** business logic, **zero** external dependencies.
2.  **Simple Groups (Molecules):**
    * Collections of atoms functioning as a unit (e.g., `SearchField` = Input + Button, `FormLabel` = Label + Icon).
    * **Constraint:** Layout-agnostic (should look good anywhere).
3.  **Complex Sections (Organisms):**
    * Distinct sections of an interface (e.g., `Header`, `ProductGrid`, `Footer`).
    * **Constraint:** Can contain business context but should remain "dumb" regarding *fetching* data (receive data via props).
4.  **Layouts (Templates):**
    * Page-level layout structures without real content (e.g., `DashboardLayout`, `BlogLayout`).
    * **Constraint:** Focus on grid/positioning (Grid, Flexbox), defining *where* components sit.
5.  **Instances (Pages):**
    * Specific instances where templates meet real data.
    * **Constraint:** This is the "Smart Component" layer. Connects to Stores/Hooks/APIs and passes data down.

---

## 2 Mandatory Heuristics
-   **Context Agnostic (No Margins):** Atoms and Molecules must **NOT** have external margins (e.g., `margin-right`). Spacing is the responsibility of the parent (Organism/Template) or a layout wrapper.
-   **Dumb Components:** Atoms and Molecules must be "Pure Functions" of their props. They render solely based on input and emit events (`onClick`, `onChange`) for actions.
-   **Composition over Complexity:** Instead of adding 10 boolean flags to a component (e.g., `isBlue`, `isLarge`, `hasIcon`), compose smaller components or use "Variants".
-   **Strict Upward Dependency:** An Atom can never import a Molecule. A Molecule can never import an Organism. Dependencies strictly flow **upwards**.

---

## 3 Rejection Criteria (Hard Fails)
-   **Business Logic in Atoms:** Importing a Store, Service, or API Client inside an Atom (e.g., `Button` calling `UserAPI`).
-   **Layout-Specific Styles in Reuse:** Hardcoding widths (e.g., `width: 300px`) in an Atom/Molecule, making it unusable in a responsive grid.
-   **Circular Dependencies:** A component importing a parent or sibling from a higher layer (e.g., a `Card` importing the `Page` context).
-   **HTML Element Leakage:** Exposing raw HTML props globally without encapsulation (unless intended).

---

## 4 Review Checklist
* [ ] Can I drop this Molecule into a completely different page and it still looks/works correctly?
* [ ] Are all external margins handled by the parent container, not the component itself?
* [ ] Is the component name derived from what it *is*, not what it *does* (e.g., `PrimaryButton` vs `SubmitOrderButton`)?
* [ ] Are Atoms strictly using tokens from the Design System (colors, spacing, typography)?
* [ ] Is data fetching strictly isolated to the Page/Container layer?