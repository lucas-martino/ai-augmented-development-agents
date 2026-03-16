---
name: backend-for-frontend
description: Client-first architecture standard - optimized for specific UI/UX, aggregation, and performance. No core domain logic.
allowed-tools: Read, Write, Edit
priority: high
version: 1.0
---

# Backend for Frontend (BFF)

**Contexto:** In the enterprise landscape, the frontend rarely consumes raw database APIs directly. An intermediate server (usually Node.js) is used to shape the data specifically for the consuming screen. The BFF serves as an aggregation and transformation layer between the Frontend and the Microservices/Services.
**Focus:** "Service-to-Experience" translation. Tailoring APIs specifically for the consuming client (Mobile, Web, IoT) to minimize latency and payload size.
**Strengths:** Eliminates over-fetching/under-fetching, simplifies frontend logic, separates deployment cycles of UI and Backend.
**Weaknesses:** Risk of logic duplication, "Passthrough" anti-pattern (useless proxy), and increased infrastructure footprint.

---

## 1 The BFF Layering
The agent must enforce the following request flow, strictly separating "Presentation Logic" from "Business Logic":

1.  **Client-Facing API (Controllers/Resolvers):**
    * Defines the contract based on the **UI Requirements**, not the Database Schema.
    * **Constraint:** Endpoints must map to "Screens" or "User Journeys" (e.g., `GET /dashboard-summary` instead of `GET /users` + `GET /orders`).
2.  **Orchestration Layer (Aggregators):**
    * Calls multiple downstream microservices concurrently.
    * Handles partial failures (e.g., if the "Recommendations" service fails, return the rest of the page with a fallback).
3.  **Transformation Layer (Presenters/Mappers):**
    * Reshapes downstream data into the exact format the UI needs.
    * Formatting dates, currencies, and merging objects happens here.
4.  **Client Clients (Proxies/Adapters):**
    * Typed clients to communicate with downstream HTTP/gRPC services.
    * Handles retries, timeouts, and circuit breaking.

---

## 2 Mandatory Heuristics
-   **One Experience, One BFF:** Do not create a "General Purpose BFF". If the Mobile App needs different data than the Web App, they get separate BFFs (or distinct modules).
-   **Zero Business Logic:** The BFF does **NOT** calculate prices, change order statuses, or validate domain rules. It only aggregates and formats.
-   **Exact Data Match:** The API response must match the UI component props 1:1. No unused fields (`over-fetching`).
-   **Protocol Translation:** The BFF handles the complexity of downstream protocols (gRPC, SOAP, GraphQL) and exposes a clean simple interface (REST/GraphQL) to the Frontend.

---

## 3 Rejection Criteria (Hard Fails)
-   **Direct Database Access:** The BFF connects to Services, not Databases. Importing SQL drivers or ORMs is forbidden.
-   **Core Logic Leakage:** Implementing "Business Rules" (e.g., `if (user.isPremium) applyDiscount()`) inside the BFF. This belongs in the Domain Service.
-   **Generic Proxying:** Exposing downstream APIs 1:1 without aggregation or transformation (e.g., just passing the request through).
-   **Monolithic Bloat:** Importing heavy libraries intended for backend processing (e.g., Image Processing, PDF generation) typically belongs in a dedicated service, not the BFF.

---

## 4 Review Checklist
* [ ] Does the API response payload contain *only* the fields used by the specific UI view?
* [ ] Is authentication/authorization validation offloaded to a Gateway or handled before aggregation?
* [ ] Are downstream service errors handled gracefully (fallback values instead of 500 errors)?
* [ ] Is the BFF completely stateless?
* [ ] Are multiple downstream calls executed in parallel (not sequential) where possible?