---
name: feature-sliced-design
description: Architectural methodology for frontend scale - strict decomposition by business scope and technical layers.
allowed-tools: Read, Write, Edit
priority: high
version: 1.0
---

# Feature-Sliced Design (FSD)

**Context:** Enterprise Use: Architectural methodology for frontend scale - strict decomposition by business scope and technical layers.
**Focus:** "Scope-based Decomposition". Organizing code by business value and strictly controlling the dependency flow to prevent "Spaghetti Code" in large-scale applications.
**Strengths:** High predictability, ease of onboarding, controlled refactoring, and clear boundaries between business logic and UI.
**Weaknesses:** Steep learning curve, boilerplate overhead for small features, and rigid rules that can frustrate developers used to flat structures.

---

## 1 The Layered Hierarchy
The agent must enforce a strict downward dependency rule across these layers (from top to bottom):

1.  **App:** Global initialization (Providers, Global Styles, Routing config).
2.  **Processes:** Complex flows spanning multiple pages (e.g., Checkout, Registration). *Note: Optional/Legacy in newer FSD versions.*
3.  **Pages:** Full views composed of widgets and features. Minimal logic, mostly data orchestration.
4.  **Widgets:** Self-contained, rich UI blocks (e.g., `ProductCardGrid`, `UserHeader`). Combines features and entities.
5.  **Features:** User interactions with business value (e.g., `AddToWishlist`, `SearchProducts`, `ChangePassword`).
6.  **Entities:** Business domain models and logic (e.g., `User`, `Order`, `Product`). Includes their state and basic UI.
7.  **Shared:** The foundation. Reusable UI (Atoms), API clients, Utils, and Constants. Zero business logic.

---

## 2 Mandatory Heuristics
-   **Dependency Rule:** A layer can only import from layers **below** it. Never from layers above or from the same layer (Cross-importing within the same layer is forbidden).
-   **Public API:** Every Slice (e.g., `entities/user`) must have an `index.ts` (Public API) that exports only what is necessary. Internal files must not be accessed directly.
-   **Slice Isolation:** A feature (e.g., `features/add-to-cart`) cannot know about another feature (e.g., `features/remove-from-cart`). They can only meet at the `Widgets` or `Pages` level.
-   **Standardized Slices:** Each slice should follow a consistent internal structure: `/ui`, `/model` (state/logic), `/api`, and `/lib`.

---

## 3 Rejection Criteria (Hard Fails)
-   **Cross-Imports:** One slice importing directly from another slice in the same layer (e.g., `features/auth` importing from `features/billing`).
-   **Circular Dependencies:** Any import that creates a loop between layers or slices.
-   **Logic Leakage:** Business logic inside the `Shared` layer.
-   **Deep Imports:** Importing from a slice bypassing the `index.ts` (e.g., `import { x } from '@/entities/user/ui/file'`).
-   **Fat Pages:** Pages containing complex business logic or styling that should be in `Features` or `Widgets`.

---

## 4 Review Checklist
* [ ] Does the folder structure follow the `Layer > Slice > Segment` hierarchy?
* [ ] Are all imports pointing to lower layers only?
* [ ] Is the `Shared` layer 100% free of business-specific terms or logic?
* [ ] Does every Slice expose its functionality through a single `index.ts` entry point?
* [ ] Are `Entities` purely representing the domain without containing specific "Feature" logic?