# 🧠 The Goated Genius Guide to Software Engineering

This is the definitive playbook for writing standard, scalable, and maintainable code. Forget the legacy spaghetti; this is how top-tier engineers build systems across small, medium, and large-scale applications.

## 1. The Core Tenets (The "Why")

Before you write a single line of code, internalize these principles:

*   **DRY (Don't Repeat Yourself):** Every piece of knowledge must have a single, unambiguous, authoritative representation within a system. If you copy-paste, you have failed. Extract to functions, classes, or helper files.
*   **KISS (Keep It Simple, Stupid):** Complexity is the enemy. Write code for the next engineer (which might be you in 6 months). Clever code is bad code; readable code is goated code.
*   **YAGNI (You Aren't Gonna Need It):** Do not build features or abstractions for "what if" scenarios in the future. Build exactly what is needed now, but build it cleanly so it can be extended later.
*   **SOLID Principles (For Object-Oriented/Medium+ Projects):**
    *   **S**ingle Responsibility: A class/function should do one thing.
    *   **O**pen/Closed: Open for extension, closed for modification.
    *   **L**iskov Substitution: Subclasses should be substitutable for base classes.
    *   **I**nterface Segregation: Many client-specific interfaces are better than one general-purpose interface.
    *   **D**ependency Inversion: Depend upon abstractions, not concretions.

---

## 2. Project Architecture by Scale (The "How")

### 🐣 Small Projects (e.g., Simple Scripts, Landing Pages, Micro-tools)
*Goal: Speed, simplicity, and zero configuration bloat.*

*   **Structure:**
    ```text
    /src
      index.php (or .js/.py)      # Entry point
      /config                     # Environment variables, DB connection
      /includes                   # Shared helpers (security.php, utils.php)
      /public                     # Assets (CSS, JS, images)
    README.md                     # How to run it
    ```
*   **Rules:** No heavy frameworks unless necessary. Keep dependencies to an absolute minimum. Use simple modular functions.

### 🐥 Medium Projects (e.g., Dashboards, Standard APIs, SaaS MVPs)
*Goal: Maintainability, separation of concerns, and clear boundaries.*

*   **Structure (MVC Pattern or Modular):**
    ```text
    /src
      /Controllers                # Handles HTTP requests/responses
      /Models                     # Data logic and DB interactions
      /Views                      # Templates/UI (if server-rendered)
      /Routes                     # API and Web route definitions
      /Middlewares                # Auth, CORS, Rate Limiting
      /Services                   # Business logic (keeps Controllers thin)
      /Utils                      # Helpers, constants, formatters
      /Config                     # App settings, DB connections
    /tests                        # Unit and integration tests
    .env                          # Secrets (NEVER COMMITTED)
    api_pointers.md               # API Documentation (See Section 4)
    ```
*   **Rules:** Controllers should only parse input and return output. All heavy lifting goes into `Services`. Database queries stay in `Models` or `Repositories`.

### 🦅 Large/Enterprise Projects (e.g., Microservices, High-Traffic Platforms)
*Goal: Scalability, fault tolerance, domain boundaries, and team autonomy.*

*   **Structure (Domain-Driven Design / Clean Architecture):**
    ```text
    /src
      /Domain                     # Core entities, interfaces, and business rules (Zero external dependencies)
      /Application                # Use cases, DTOs (Data Transfer Objects)
      /Infrastructure             # DB implementations, external APIs, Redis, Email services
      /Presentation               # Controllers, GraphQL resolvers, CLI commands
    /tests
      /Unit
      /Integration
      /E2E
    docker-compose.yml
    CI/CD Pipelines
    ```
*   **Rules:** Strict dependency injection. Code in `Domain` knows nothing about `Infrastructure` (the database). Everything is interface-driven. High test coverage is non-negotiable.

---

## 3. The Standard Way to Code (The "What")

### Naming Conventions
*   **Variables/Functions:** `camelCase` or `snake_case` (stick to the language standard). *Be descriptive: `getUserById()` not `getUsr()`.*
*   **Classes:** `PascalCase` (e.g., `CustomerRepository`).
*   **Constants:** `UPPER_SNAKE_CASE` (e.g., `MAX_RETRY_COUNT`).
*   **Booleans:** Prefix with `is`, `has`, or `should` (e.g., `isActive`, `hasPermission`).

### Security by Default
*   **Never trust user input.** Validate everything (type, length, format) at the boundary.
*   **Use Parameterized Queries.** Never concatenate SQL.
*   **Centralize Security.** Auth, CSRF, and Rate Limiting should be middleware/wrappers, not copy-pasted into every file.
*   **Fail Securely.** If an error happens, deny access. Do not leak stack traces to the frontend.

### Error Handling
*   Don't just `echo` or `console.log` errors. Use a centralized Error Handler.
*   Return standard HTTP status codes:
    *   `200 OK` (Success)
    *   `201 Created` (Resource created)
    *   `400 Bad Request` (Validation failed)
    *   `401 Unauthorized` (Not logged in)
    *   `403 Forbidden` (Logged in, but lack permissions)
    *   `404 Not Found`
    *   `500 Internal Server Error` (Your code broke)
*   **Standard API Response Format:**
    ```json
    {
      "status": "success", // or "error"
      "data": { ... },     // payload (null if error)
      "message": "..."     // Human readable info
    }
    ```

---

## 4. The API Pointers Documentation (`api_pointers.md`)

As projects grow, finding endpoints becomes a nightmare. The "Goated" standard is to maintain a living document that maps the system. 

**Rule:** Every time you create or modify an endpoint, you update the `api_pointers.md` file.

### How to structure `api_pointers.md` (Example):

```markdown
# 🗺️ API Pointers & Endpoints

This document maps all backend routes to their corresponding files and purposes.

## 👥 Customer Management

| Endpoint (Method) | File Location | Purpose | Auth Required |
| :--- | :--- | :--- | :--- |
| `POST /api/customers/sync` | `/backend/api_sync_customers.php` | Bulk syncs customers from Oracle. | Yes + CSRF |
| `POST /api/customers/status` | `/backend/api_update_client_status.php` | Toggles Active/Inactive status. | Yes + CSRF + RBAC |

## 🛠️ Security Middlewares

*   `requireLogin()`: Located in `/includes/security.php`. Validates session.
*   `validateCsrf()`: Located in `/includes/security.php`. Validates POST token.
```

By maintaining this file, any new developer (or AI) can instantly understand the architecture and locate exactly where logic resides without grepping through the entire codebase.

---
*Stay Goated. Write clean code.*
