````markdown
# GOATED GENIUS ENGINEERING STANDARD

> A language-agnostic engineering standard for building maintainable small, medium, and large software projects.

---

## 1. Purpose

This document defines how software should be designed, structured, implemented, tested, documented, reviewed, secured, and operated.

The goal is not maximum abstraction or cleverness. The goal is software that is:

- Easy to understand
- Easy to change
- Easy to test
- Easy to debug
- Secure by default
- Observable in production
- Consistent across teams
- Appropriate for the project’s actual size

---

## 2. Core Principles

### 2.1 Correctness before cleverness

Prefer obvious, boring code over clever code.

A new engineer should understand the code without knowing hidden conventions or undocumented behavior.

### 2.2 Keep it simple

Do not introduce:

- Microservices without a demonstrated need
- Generic frameworks for one use case
- Interfaces with only one implementation unless they create a real boundary
- Event-driven architecture for simple synchronous operations
- Distributed infrastructure for problems a single application can solve

Start simple. Introduce complexity only when measurable constraints require it.

### 2.3 DRY, but not at any cost

DRY means avoiding duplicated knowledge, not eliminating every repeated line.

Create an abstraction when:

1. The same business rule exists in multiple places.
2. The duplicated code changes for the same reason.
3. The abstraction has a clear name.
4. The abstraction reduces cognitive load.

Do not abstract code merely because it looks similar. Two workflows may look alike today while representing different business concepts.

A useful rule:

> Duplicate twice, observe the pattern, and abstract when the pattern becomes stable.

### 2.4 Separation of concerns

Keep these concerns separate:

- Transport: HTTP, GraphQL, queues, CLI
- Application workflows
- Domain and business rules
- Persistence
- External integrations
- Configuration
- Observability
- Presentation

A controller should not contain SQL, business rules, email delivery, and response formatting in one function.

### 2.5 High cohesion, low coupling

Code belonging to one capability should remain close together.

Modules should expose small public APIs and hide implementation details. Avoid modules that reach into another module’s database tables, private classes, or internal folders.

### 2.6 Dependency direction

Dependencies should generally point inward:

```text
Delivery mechanisms
        ↓
Application/use cases
        ↓
Domain rules
```
````

Infrastructure implements capabilities required by the application:

```text
Application → interface/port ← infrastructure implementation
```

Domain rules should not depend on:

- Web frameworks
- Databases
- Message brokers
- Cloud SDKs
- UI libraries

### 2.7 Explicit behavior

Make important behavior visible:

- Validate inputs explicitly.
- Represent expected errors explicitly.
- Name side effects clearly.
- Document transaction boundaries.
- Define timeouts and retry policies.
- Avoid hidden global state.
- Avoid functions whose names conceal network or database activity.

### 2.8 Fail safely

Failures must be:

- Detected
- Classified
- Logged safely
- Returned consistently
- Observable
- Recoverable where appropriate

Never silently swallow exceptions.

### 2.9 Optimize for maintainability first

Do not optimize based on assumptions. Measure before optimizing.

Use:

- Profiling
- Tracing
- Database query analysis
- Load testing
- Production metrics

---

## 3. Project Size Classification

Project size is determined by complexity, risk, traffic, domain boundaries, integrations, and team size—not merely lines of code.

### Small project

Usually:

- One to three developers
- One deployable application
- Limited domain complexity
- Few integrations
- Low operational risk

Recommended architecture:

- Modular monolith
- One primary database
- Direct function calls
- Simple deployment
- Minimal infrastructure

### Medium project

Usually:

- Multiple contributors or teams
- Several business capabilities
- Multiple external integrations
- Background jobs
- Stronger security and reliability requirements

Recommended architecture:

- Modular monolith by default
- Clearly defined module ownership
- Internal events where useful
- Separate workers only when operationally justified
- Strong automated testing and observability

### Large project

Usually:

- Multiple autonomous teams
- Distinct business domains
- Independent scaling requirements
- High availability requirements
- Complex compliance or security needs

Recommended architecture:

- Domain-oriented services where justified
- Explicit service ownership
- Versioned contracts
- Strong platform standards
- Centralized observability
- Automated delivery and governance

Do not use large-project architecture for a small project. Premature distribution creates unnecessary failure modes.

---

## 4. Standard Repository Structure

Do not organize the entire application globally by technical type:

```text
controllers/
services/
models/
repositories/
```

As the project grows, this scatters each feature across unrelated folders.

Prefer organizing by feature or domain.

### 4.1 Small project

```text
project/
├── README.md
├── docs/
│   ├── architecture.md
│   └── endpoints.md
├── src/
│   ├── app/
│   ├── features/
│   │   ├── users/
│   │   ├── orders/
│   │   └── payments/
│   ├── shared/
│   ├── config/
│   └── main.*
├── tests/
│   ├── integration/
│   └── e2e/
├── scripts/
├── migrations/
├── .env.example
├── .editorconfig
├── .gitignore
└── project configuration files
```

A feature may initially be simple:

```text
features/users/
├── user.*
├── user-service.*
├── user-repository.*
├── user-controller.*
├── user-validation.*
└── user.test.*
```

Do not create empty architectural layers merely to satisfy a template.

### 4.2 Medium project

```text
project/
├── README.md
├── CHANGELOG.md
├── docs/
│   ├── architecture/
│   │   ├── overview.md
│   │   ├── boundaries.md
│   │   └── decisions/
│   ├── api/
│   │   ├── endpoint-index.md
│   │   ├── authentication.md
│   │   └── errors.md
│   ├── operations/
│   │   ├── deployment.md
│   │   ├── monitoring.md
│   │   └── incident-response.md
│   └── development/
│       ├── setup.md
│       └── testing.md
├── src/
│   ├── modules/
│   │   ├── identity/
│   │   │   ├── application/
│   │   │   ├── domain/
│   │   │   ├── infrastructure/
│   │   │   ├── presentation/
│   │   │   └── tests/
│   │   ├── orders/
│   │   └── payments/
│   ├── shared/
│   │   ├── errors/
│   │   ├── logging/
│   │   ├── observability/
│   │   └── security/
│   ├── bootstrap/
│   └── main.*
├── tests/
│   ├── integration/
│   ├── contract/
│   └── e2e/
├── migrations/
├── scripts/
├── config/
└── infrastructure/
```

### 4.3 Large project or monorepo

```text
repository/
├── README.md
├── docs/
│   ├── system-map.md
│   ├── service-catalog.md
│   ├── endpoint-index.md
│   ├── architecture/
│   ├── decisions/
│   ├── security/
│   └── operations/
├── apps/
│   ├── public-api/
│   ├── admin-api/
│   ├── web-client/
│   └── workers/
├── services/
│   ├── identity/
│   ├── orders/
│   ├── payments/
│   └── notifications/
├── packages/
│   ├── contracts/
│   ├── observability/
│   ├── testing/
│   └── configuration/
├── infrastructure/
│   ├── environments/
│   ├── deployment/
│   └── monitoring/
├── tools/
└── scripts/
```

Shared packages must have clear ownership and stable responsibilities. Do not turn `shared`, `common`, or `utils` into dumping grounds.

---

## 5. Module Structure

A mature module may use:

```text
module-name/
├── domain/
│   ├── entities/
│   ├── value-objects/
│   ├── services/
│   ├── events/
│   └── errors/
├── application/
│   ├── commands/
│   ├── queries/
│   ├── use-cases/
│   ├── ports/
│   └── dto/
├── infrastructure/
│   ├── persistence/
│   ├── messaging/
│   └── integrations/
├── presentation/
│   ├── http/
│   ├── consumers/
│   └── cli/
└── tests/
```

Only introduce these folders when their concepts actually exist.

### Responsibilities

#### Domain

Contains business concepts and rules.

It should not know how requests arrive or where data is stored.

#### Application

Coordinates workflows:

1. Validate the command’s application-level requirements.
2. Load required state.
3. Execute domain behavior.
4. Persist changes.
5. Trigger required side effects.
6. Return a result.

#### Infrastructure

Contains database, cache, queue, filesystem, email, payment, and third-party implementations.

#### Presentation

Converts protocol-specific input into application commands and converts results into protocol-specific output.

---

## 6. Coding Standards

### 6.1 Naming

Names should communicate intent.

Prefer:

```text
calculateInvoiceTotal
findActiveSubscription
PaymentAuthorizationFailed
MAX_LOGIN_ATTEMPTS
```

Avoid:

```text
handleData
doStuff
process
temp
manager
helper
utils
thing
obj
```

Use the language ecosystem’s standard naming conventions.

### 6.2 Functions

A good function:

- Has one clear purpose
- Operates at one abstraction level
- Has a descriptive name
- Has limited parameters
- Makes side effects obvious
- Returns a predictable type
- Is small enough to understand without excessive scrolling

Do not split functions solely to reduce line count. Extract code when the extracted concept has a meaningful name.

Avoid boolean arguments:

```text
createUser(data, true, false)
```

Prefer options or separate operations:

```text
createUser(data, {
  sendWelcomeEmail: true,
  requireVerification: false
})
```

### 6.3 Classes and modules

A class or module should have one primary reason to change.

Warning signs:

- More than one unrelated responsibility
- Many dependencies
- Generic names such as `Manager` or `Processor`
- Methods that use unrelated groups of fields
- Constant edits by unrelated teams
- Hundreds or thousands of lines

### 6.4 Comments

Comments should explain:

- Why a decision was made
- A non-obvious invariant
- A security constraint
- A compatibility limitation
- A temporary workaround with a tracking reference

Do not comment what the code already says.

Bad:

```text
Increment count by one.
```

Better:

```text
Retries are capped to prevent duplicate payment authorization.
```

### 6.5 Constants and magic values

Replace meaningful magic values with named constants or configuration.

Bad:

```text
if attempts > 5
```

Better:

```text
if attempts > MAX_LOGIN_ATTEMPTS
```

Do not create constants for values whose meaning is already obvious and local.

### 6.6 Nullability

Treat missing data deliberately.

Distinguish between:

- Missing
- Empty
- Unknown
- Not applicable
- Deleted
- Invalid

Avoid chains of unverified nullable values.

### 6.7 Immutability

Prefer immutable data where practical, especially for:

- Configuration
- Commands
- Events
- Value objects
- Shared state

Mutate state through controlled operations that preserve invariants.

### 6.8 Types

Use the strongest practical type system available.

Avoid using generic maps, dictionaries, or `any` for structured domain data.

Prefer:

```text
Money
EmailAddress
OrderId
DateRange
PaymentStatus
```

over primitive strings and numbers when the concept has validation or behavior.

### 6.9 Side effects

Make I/O visible and isolate it:

- Database access
- Network calls
- Filesystem operations
- Clock access
- Randomness
- Environment variables
- Message publication

Inject or wrap clocks, random generators, and external clients when deterministic testing matters.

---

## 7. Business Logic

Business rules belong in domain or application code, not:

- Controllers
- Database triggers by default
- UI components
- Serialization code
- Route definitions
- Framework middleware

Bad flow:

```text
Controller
  → validates business rules
  → performs queries
  → calculates price
  → calls payment provider
  → sends email
  → constructs response
```

Preferred flow:

```text
Controller
  → validates transport input
  → calls use case
      → executes business rules
      → uses repositories/integrations
  → maps result to response
```

Protect domain invariants at the point where state changes.

Examples:

- An order cannot be paid twice.
- A refund cannot exceed the captured amount.
- A reservation cannot end before it starts.
- A disabled account cannot create a session.

---

## 8. API Standards

### 8.1 General conventions

Every endpoint should define:

- Purpose
- Method
- Path
- Authentication
- Authorization
- Path parameters
- Query parameters
- Headers
- Request schema
- Response schema
- Status codes
- Error codes
- Idempotency behavior
- Rate limits
- Side effects
- Examples
- Owning module or service

### 8.2 Resource naming

Prefer nouns and consistent pluralization:

```text
GET    /users
GET    /users/{userId}
POST   /users
PATCH  /users/{userId}
DELETE /users/{userId}
```

For actions that do not map cleanly to CRUD, use explicit operations:

```text
POST /orders/{orderId}/cancel
POST /payments/{paymentId}/capture
```

Do not force complex business actions into misleading CRUD semantics.

### 8.3 Status codes

Use status codes consistently:

- `200 OK` — successful response with content
- `201 Created` — resource created
- `202 Accepted` — accepted for asynchronous processing
- `204 No Content` — successful response without content
- `400 Bad Request` — malformed or invalid request
- `401 Unauthorized` — authentication is missing or invalid
- `403 Forbidden` — authenticated but not permitted
- `404 Not Found` — resource does not exist or is intentionally concealed
- `409 Conflict` — state conflict or uniqueness violation
- `422 Unprocessable Content` — semantically invalid input
- `429 Too Many Requests` — rate limit exceeded
- `500 Internal Server Error` — unexpected server failure
- `503 Service Unavailable` — temporarily unable to serve

### 8.4 Standard error envelope

Use one consistent error structure:

```json
{
  "error": {
    "code": "ORDER_ALREADY_PAID",
    "message": "The order has already been paid.",
    "details": [],
    "requestId": "request-correlation-id"
  }
}
```

Rules:

- `code` is stable and machine-readable.
- `message` is safe for clients.
- `details` contains structured validation information.
- `requestId` supports investigation.
- Never return stack traces, secrets, SQL, or internal paths.

### 8.5 Pagination

Use one consistent pagination strategy.

Cursor pagination is usually preferable for large or frequently changing datasets:

```text
GET /orders?limit=50&cursor=opaque-value
```

Example response:

```json
{
  "data": [],
  "pagination": {
    "nextCursor": "opaque-value",
    "hasMore": true
  }
}
```

Define:

- Default limit
- Maximum limit
- Ordering
- Cursor stability
- Behavior for invalid cursors

### 8.6 Filtering and sorting

Use documented allowlists:

```text
GET /orders?status=paid&sort=-createdAt
```

Never pass arbitrary client-provided field names directly into database queries.

### 8.7 Versioning

Version breaking contract changes intentionally.

A breaking change includes:

- Removing a field
- Renaming a field
- Changing a field’s type
- Making an optional field required
- Changing established semantics
- Removing accepted enum values
- Changing authentication behavior

Prefer additive, backward-compatible evolution when possible.

### 8.8 Idempotency

Operations that may be retried and create financial or external side effects should support idempotency.

```text
Idempotency-Key: client-generated-unique-value
```

Document:

- Key scope
- Expiration
- Response replay behavior
- Conflict behavior
- Storage strategy

### 8.9 Timeouts and retries

Every external call must have a timeout.

Retries must be:

- Bounded
- Limited to retry-safe failures
- Delayed using exponential backoff
- Randomized with jitter
- Safe from duplicating side effects

Do not blindly retry validation errors, authentication failures, or non-idempotent operations.

---

## 9. Endpoint Pointer Documentation

Maintain an endpoint index so engineers can find API behavior without searching the entire repository.

Recommended location:

```text
docs/api/endpoint-index.md
```

Each service may additionally maintain:

```text
services/<service>/docs/endpoints.md
```

### Endpoint index template

```markdown
# Endpoint Index

Last reviewed: YYYY-MM-DD

## Authentication

| Method | Path                   | Purpose             | Auth   | Owner    | Implementation       | Detailed docs                |
| ------ | ---------------------- | ------------------- | ------ | -------- | -------------------- | ---------------------------- |
| POST   | `/v1/sessions`         | Create a session    | Public | Identity | `path/to/controller` | `docs/api/authentication.md` |
| DELETE | `/v1/sessions/current` | End current session | Bearer | Identity | `path/to/controller` | `docs/api/authentication.md` |

## Users

| Method | Path                 | Purpose    | Auth   | Owner    | Implementation       | Detailed docs       |
| ------ | -------------------- | ---------- | ------ | -------- | -------------------- | ------------------- |
| GET    | `/v1/users/{userId}` | Get a user | Bearer | Identity | `path/to/controller` | `docs/api/users.md` |

## Orders

| Method | Path | Purpose | Auth | Owner | Implementation | Detailed docs |
| ------ | ---- | ------- | ---- | ----- | -------------- | ------------- |

## Payments

| Method | Path | Purpose | Auth | Owner | Implementation | Detailed docs |
| ------ | ---- | ------- | ---- | ----- | -------------- | ------------- |
```

Each detailed endpoint entry should use:

```markdown
## Create Order

- **Method:** `POST`
- **Path:** `/v1/orders`
- **Owner:** Orders
- **Authentication:** Bearer token
- **Authorization:** Customer or administrator
- **Implementation:** `src/modules/orders/presentation/http/create-order.*`
- **Use case:** `src/modules/orders/application/create-order.*`
- **Request schema:** `src/modules/orders/presentation/http/schemas/create-order.*`
- **Response schema:** `src/modules/orders/presentation/http/schemas/order-response.*`
- **Tests:** `tests/integration/orders/create-order.*`
- **Contract:** `contracts/openapi.*`
- **Side effects:** Creates an order and emits `OrderCreated`
- **Idempotency:** Required
- **Errors:** `INVALID_ITEM`, `ITEM_UNAVAILABLE`, `CUSTOMER_NOT_FOUND`
```

### Other pointer files

For larger systems, maintain:

```text
docs/
├── endpoint-index.md
├── service-catalog.md
├── event-catalog.md
├── database-map.md
├── dependency-map.md
├── configuration-reference.md
├── error-catalog.md
├── runbook-index.md
└── glossary.md
```

#### Service catalog

For each service include:

- Purpose
- Owner
- Repository or directory
- Runtime
- Deployment
- Database
- Public and internal APIs
- Produced and consumed events
- Dependencies
- Dashboards
- Alerts
- Runbooks

#### Event catalog

For every event include:

- Event name and version
- Producer
- Consumers
- Schema
- Trigger
- Delivery guarantees
- Ordering behavior
- Retry behavior
- Dead-letter handling
- Sensitive fields

#### Error catalog

For every public error include:

- Stable code
- HTTP status
- Meaning
- Retryability
- Client action
- Owning module

Documentation must point to the source of truth rather than duplicate unstable implementation details.

Where practical, generate endpoint indexes and API references from route metadata or API contracts to prevent drift.

---

## 10. Validation

Validate at boundaries.

### Transport validation

Validate:

- Required fields
- Types
- Formats
- Lengths
- Allowed enum values
- Structural constraints

### Business validation

Validate domain-specific rules in the domain or application layer:

- Account eligibility
- Inventory availability
- Allowed state transitions
- Credit limits
- Ownership
- Time restrictions

Never rely only on client-side validation.

Normalize carefully. Do not silently “fix” ambiguous or invalid user input.

---

## 11. Persistence

### 11.1 Repositories

Repositories represent domain-oriented persistence operations.

Prefer:

```text
findOrderById
saveOrder
findPendingOrdersForCustomer
```

Avoid exposing persistence details throughout business code.

Do not create a generic repository that attempts to support every entity and query. Important workflows often need explicit queries.

### 11.2 Transactions

Use a transaction when multiple writes must succeed or fail together.

Keep transactions:

- Short
- Explicit
- Free of slow external network calls where possible
- Documented at application boundaries

Do not keep a database transaction open while waiting for email, payment, or unrelated remote services.

### 11.3 Migrations

Every schema change must be version-controlled.

Migrations should be:

- Reviewed
- Tested against production-like data
- Backward-compatible during rolling deployments
- Observable
- Reversible where practical

For risky changes, use expand-and-contract:

1. Add the new schema.
2. Deploy code that supports old and new forms.
3. Backfill data.
4. Switch reads and writes.
5. Verify.
6. Remove the old schema later.

Never edit a migration already applied in shared environments.

### 11.4 Query discipline

Prevent:

- N+1 queries
- Unbounded reads
- Missing indexes
- Full-table scans on critical paths
- Arbitrary dynamic SQL
- Loading entire records when only a few fields are needed

Database constraints should enforce critical integrity rules where possible.

---

## 12. External Integrations

Wrap each provider behind a dedicated adapter.

```text
integrations/
└── payment-provider/
    ├── client.*
    ├── mapper.*
    ├── errors.*
    ├── config.*
    └── client.test.*
```

The adapter should own:

- Authentication
- Request mapping
- Response mapping
- Timeouts
- Safe retries
- Provider-specific error conversion
- Logging and metrics
- Test doubles

Do not allow provider-specific payloads to spread throughout the domain.

Treat third-party responses as untrusted input.

---

## 13. Background Jobs and Messaging

Every job or consumer must define:

- Trigger
- Input schema
- Idempotency strategy
- Retry policy
- Maximum attempts
- Timeout
- Dead-letter behavior
- Ownership
- Monitoring
- Recovery procedure

Assume messages may be delivered more than once unless the infrastructure guarantees otherwise.

Consumers should be idempotent.

For database writes and event publication that must remain consistent, consider an outbox pattern rather than assuming two independent operations will both succeed.

Events should represent completed facts:

```text
OrderCreated
PaymentCaptured
UserEmailChanged
```

Avoid vague events:

```text
OrderUpdated
DataChanged
SomethingProcessed
```

Version externally consumed event contracts.

---

## 14. Error Handling

Classify errors into categories:

### Validation error

Client input is invalid.

### Authentication error

Identity cannot be established.

### Authorization error

Identity exists but lacks permission.

### Not-found error

Requested resource does not exist.

### Conflict error

Request conflicts with current state.

### Dependency error

A required external system failed.

### Transient error

The operation may succeed when retried.

### Internal error

An unexpected defect or invariant violation occurred.

Expected errors should be represented deliberately. Unexpected errors should reach a central error boundary that:

1. Generates or preserves a correlation ID.
2. Logs diagnostic context.
3. Reports the exception.
4. Returns a safe response.
5. Updates operational metrics.

Never use exceptions as ordinary branching when the language provides a clearer result representation.

---

## 15. Security Standards

### 15.1 Secrets

Never place secrets in:

- Source code
- Committed configuration
- Logs
- Error responses
- Test fixtures
- Screenshots
- Documentation examples

Use a secret-management mechanism and rotate credentials.

Provide `.env.example` with names and safe placeholders only.

### 15.2 Authentication and authorization

Authentication answers:

> Who is making the request?

Authorization answers:

> Is this identity permitted to perform this operation on this resource?

Authorization must occur server-side and close to the protected action.

Check object-level authorization. Knowing a resource ID must not grant access.

### 15.3 Input and output safety

Use:

- Parameterized database queries
- Output encoding
- Allowlists for dynamic operations
- File type and size restrictions
- Safe path handling
- Request body limits
- Rate limiting
- Secure headers
- Dependency scanning

### 15.4 Sensitive data

Classify data and minimize collection.

Do not log:

- Passwords
- Access tokens
- Session identifiers
- Secret keys
- Full payment details
- Unnecessary personal data

Mask sensitive fields consistently.

### 15.5 Privilege

Use least privilege for:

- Database users
- Service accounts
- Deployment identities
- CI credentials
- Cloud roles
- Internal service access

---

## 16. Configuration

Configuration should be:

- Externalized
- Validated at startup
- Typed where possible
- Documented
- Environment-specific
- Safe to log only after redaction

Fail fast when required configuration is missing or invalid.

Recommended reference:

```text
docs/configuration-reference.md
```

Template:

```markdown
| Variable             | Required | Default | Sensitive | Description                 |
| -------------------- | -------: | ------- | --------: | --------------------------- |
| `DATABASE_URL`       |      Yes | None    |       Yes | Primary database connection |
| `HTTP_PORT`          |       No | `8080`  |        No | HTTP listener port          |
| `PAYMENT_TIMEOUT_MS` |       No | `5000`  |        No | Payment provider timeout    |
```

Avoid environment-specific conditionals scattered throughout business logic.

---

## 17. Logging and Observability

### 17.1 Structured logging

Prefer structured logs:

```json
{
  "level": "info",
  "message": "order_created",
  "requestId": "req-123",
  "orderId": "ord-456",
  "durationMs": 42
}
```

Include useful identifiers, not entire payloads.

### 17.2 Metrics

Track:

- Request rate
- Error rate
- Latency
- Resource saturation
- Queue depth
- Job failures
- Retry counts
- Dependency health
- Important business outcomes

### 17.3 Tracing

Propagate correlation and trace context across:

- HTTP calls
- Queue messages
- Background jobs
- Service boundaries

### 17.4 Health endpoints

Separate:

- Liveness: process is alive
- Readiness: process can serve traffic
- Dependency diagnostics: restricted operational detail

Health responses must not reveal secrets or sensitive infrastructure information.

### 17.5 Alerts

Alerts should be actionable.

Every alert should answer:

- What failed?
- What is the user impact?
- Who owns it?
- Where is the dashboard?
- Where is the runbook?
- What is the immediate response?

---

## 18. Testing Strategy

Use the smallest test that provides sufficient confidence.

### Unit tests

Test isolated business rules and transformations.

They should be:

- Fast
- Deterministic
- Focused
- Independent of network and database access

### Integration tests

Test real boundaries such as:

- Database repositories
- Queue adapters
- Cache integrations
- API serialization
- Authentication middleware

### Contract tests

Verify that APIs and events match their documented contracts.

### End-to-end tests

Test a small number of critical user journeys across deployed components.

Do not attempt to validate every edge case through end-to-end tests.

### Test behavior, not implementation

Prefer:

```text
rejects payment when order is already paid
```

over:

```text
calls repository method once
```

Mocks are useful at true boundaries. Excessive mocking produces tests coupled to implementation details.

### Test structure

Use Arrange, Act, Assert:

```text
Arrange the required state.
Act by executing one behavior.
Assert the observable result.
```

### Required coverage

Every important change should cover:

- Happy path
- Validation failure
- Authorization failure
- Relevant boundary condition
- Dependency failure
- Regression scenario when fixing a defect

Coverage percentage is a signal, not the goal. High coverage with weak assertions is not quality.

---

## 19. API Testing and Examples

Maintain executable request examples for:

- Successful calls
- Validation failures
- Authentication failures
- Authorization failures
- Common business conflicts
- Pagination
- Idempotency
- Rate limiting where relevant

Examples must use safe placeholders:

```text
{{base_url}}
{{access_token}}
{{merchant_id}}
```

Never commit real production credentials.

Keep examples synchronized with the API contract and endpoint index.

---

## 20. Documentation Standards

Every repository should contain:

### README

Include:

- Purpose
- Scope
- Prerequisites
- Setup
- Configuration
- Running locally
- Testing
- Building
- Deployment overview
- Documentation links
- Ownership

### Architecture overview

Include:

- System context
- Main components
- Data flow
- Module boundaries
- External dependencies
- Security boundaries
- Deployment model

### Architecture Decision Records

Store important decisions:

```text
docs/architecture/decisions/
├── 0001-use-modular-monolith.md
├── 0002-use-cursor-pagination.md
└── 0003-adopt-outbox-pattern.md
```

Template:

```markdown
# Decision title

- Status: Proposed | Accepted | Superseded
- Date: YYYY-MM-DD
- Owners: Team or role

## Context

What problem or constraint requires a decision?

## Decision

What was selected?

## Alternatives considered

What other approaches were considered?

## Consequences

What benefits, costs, risks, and follow-up work result?
```

Documentation is part of the change. If behavior changes, update the relevant documentation in the same pull request.

---

## 21. Git and Change Management

### Branches

Use short-lived branches and integrate frequently.

Avoid long-running branches that accumulate unrelated work.

### Commits

A good commit:

- Represents one logical change
- Uses an imperative message
- Includes relevant tests
- Leaves the repository usable

Examples:

```text
Add idempotency handling to payment capture
Fix authorization check for order cancellation
Document cursor pagination behavior
```

Avoid:

```text
stuff
fix
updates
final final
```

### Pull requests

Keep pull requests focused.

A pull request should explain:

- What changed
- Why it changed
- How it works
- How it was tested
- Risks
- Migration or rollout requirements
- Documentation changes
- Screenshots or API examples where useful

Do not mix formatting, refactoring, dependency upgrades, and feature behavior unless necessary.

### Reviews

Review for:

- Correctness
- Security
- Maintainability
- Tests
- Failure behavior
- Data integrity
- Compatibility
- Performance risks
- Observability
- Documentation

Critique the code, not the author.

---

## 22. CI/CD Standards

Every change should automatically run applicable checks:

1. Formatting
2. Linting
3. Static analysis
4. Type checking
5. Unit tests
6. Integration tests
7. Contract validation
8. Security scanning
9. Build
10. Migration validation

Deployments should be:

- Repeatable
- Automated
- Auditable
- Environment-aware
- Reversible or safely forward-fixable

Use staged rollout for high-risk changes:

- Development
- Test
- Staging
- Limited production exposure
- Full production rollout

Define rollback conditions before deployment.

---

## 23. Dependency Management

Before adding a dependency, evaluate:

- Is it necessary?
- Can the standard library solve the problem?
- Is it maintained?
- Is its license acceptable?
- Is its security history acceptable?
- What is its transitive dependency cost?
- Is it appropriate for the project’s scale?
- How difficult would replacement be?

Pin versions according to ecosystem standards and automate safe updates.

Remove unused dependencies.

Avoid building critical business logic directly around a library’s proprietary types. Use adapters where replacement risk matters.

---

## 24. Performance

Performance work must be evidence-driven.

Measure:

- Latency percentiles
- Throughput
- Error rate
- CPU
- Memory
- Database time
- External dependency time
- Queue delay
- Cache effectiveness

Common improvements:

- Appropriate indexing
- Query optimization
- Batching
- Pagination
- Connection pooling
- Caching stable expensive results
- Moving suitable work off the request path
- Reducing serialized payload size

Caching requires explicit answers for:

- Cache key
- Expiration
- Invalidation
- Consistency requirements
- Stampede prevention
- Failure behavior

Do not use caching to hide fundamentally inefficient database access without understanding the tradeoff.

---

## 25. Scalability

Scale only after identifying the actual bottleneck.

A typical progression:

1. Improve code and queries.
2. Add appropriate indexes.
3. Add caching where justified.
4. Scale the application vertically.
5. Scale stateless instances horizontally.
6. Separate background workloads.
7. Partition by clear domain or operational boundaries.
8. Introduce independent services only when benefits exceed distributed-system costs.

Microservices introduce:

- Network failures
- Partial failure
- Eventual consistency
- More deployments
- Contract management
- Distributed tracing
- Data ownership complexity
- Larger operational burden

Use them for real organizational or operational boundaries, not fashion.

---

## 26. Modular Monolith Rules

A modular monolith is the default for many small and medium systems.

Each module should:

- Own its business logic
- Expose an explicit public API
- Hide internal implementation
- Avoid accessing another module’s private persistence
- Communicate through application interfaces or internal events
- Have clear ownership

Possible future service boundaries should be represented by clean modules before physical separation.

Do not simulate microservices inside one process with unnecessary serialization, networking, or infrastructure.

---

## 27. Distributed System Rules

When services are genuinely required:

- Each service owns its data.
- Cross-service database access is forbidden.
- Contracts are versioned.
- Timeouts are mandatory.
- Retries are bounded and safe.
- Idempotency is designed explicitly.
- Partial failure is expected.
- Events are observable.
- Dead-letter handling has an owner.
- Distributed transactions are avoided where possible.
- Data consistency requirements are documented.
- Each service has dashboards, alerts, and runbooks.

Avoid long synchronous call chains. One failed downstream dependency should not unnecessarily collapse the entire system.

---

## 28. Frontend Standards

Organize frontend code by feature where practical:

```text
src/
├── app/
├── features/
│   ├── authentication/
│   ├── checkout/
│   └── orders/
├── shared/
│   ├── components/
│   ├── hooks/
│   ├── api/
│   └── utilities/
└── assets/
```

Keep separate:

- Server state
- Local UI state
- Form state
- URL/navigation state

UI components should not contain hidden business rules that the server fails to enforce.

Provide:

- Loading states
- Empty states
- Error states
- Retry behavior
- Accessibility
- Responsive behavior

Never expose secrets in frontend code.

---

## 29. Utilities and Shared Code

A utility must:

- Have a narrow purpose
- Have a clear name
- Be independently testable
- Avoid unrelated side effects
- Have multiple legitimate consumers

Do not create a miscellaneous `helpers` file containing unrelated functions.

Prefer:

```text
shared/
├── dates/
├── money/
├── validation/
└── identifiers/
```

A shared package is a dependency. Changes to it can affect many consumers, so its API must remain small and stable.

---

## 30. Refactoring Rules

Refactor when:

- Behavior is protected by tests.
- Duplication represents the same knowledge.
- A module has unclear responsibilities.
- Dependencies point in the wrong direction.
- A feature is difficult to change safely.
- Operational incidents reveal structural weakness.

Refactor incrementally.

Separate behavior changes from large structural changes where possible.

Do not rewrite a working system merely because a different architecture looks cleaner. A rewrite requires measurable benefits, migration planning, compatibility strategy, and explicit risk ownership.

---

## 31. Feature Development Workflow

For every feature:

1. Understand the requirement and user outcome.
2. Identify the owning module.
3. Define acceptance criteria.
4. Define API or event contract changes.
5. Identify security and authorization requirements.
6. Identify data changes and migration needs.
7. Define failure behavior.
8. Define observability requirements.
9. Implement the smallest coherent solution.
10. Add appropriate tests.
11. Update endpoint and architecture pointers.
12. Run automated checks.
13. Review for backward compatibility.
14. Plan deployment and rollback.
15. Verify behavior after deployment.

---

## 32. Bug-Fix Workflow

For every defect:

1. Reproduce it.
2. Determine expected behavior.
3. Identify the root cause.
4. Add a failing regression test when practical.
5. Fix the root cause, not only the symptom.
6. Verify adjacent behavior.
7. Assess security and data impact.
8. Add monitoring if detection was inadequate.
9. Document operational lessons when significant.
10. Deploy and verify.

Do not weaken validation or suppress exceptions merely to make an error disappear.

---

## 33. Definition of Done

A change is complete only when applicable requirements are satisfied:

- Acceptance criteria are met.
- Code follows module boundaries.
- Input validation exists.
- Authorization is enforced.
- Errors are safe and consistent.
- Tests cover important behavior.
- Logging and metrics are adequate.
- No secrets are introduced.
- Database changes are safely migratable.
- API and event compatibility is reviewed.
- Documentation is updated.
- Endpoint pointers are updated.
- CI checks pass.
- Deployment and rollback are understood.
- Production verification is possible.

“Works on my machine” is not done.

---

## 34. Anti-Patterns

Avoid:

- God classes
- God services
- Massive controllers
- Business logic in routes
- Business logic in UI components
- Hidden global mutable state
- Circular dependencies
- Generic repositories for every use case
- Excessive inheritance
- Premature microservices
- Premature abstraction
- Copy-pasted business rules
- Catching errors and doing nothing
- Logging sensitive payloads
- Unbounded queries
- Infinite retries
- Network calls without timeouts
- Shared databases across independent services
- Direct access to another module’s internals
- Boolean argument explosions
- `utils` dumping grounds
- Tests coupled to private implementation
- Documentation that duplicates and drifts from contracts
- TODO comments without context or ownership

---

## 35. Practical Rules by Project Size

### Small

Must have:

- Clear README
- Feature-oriented structure
- Input validation
- Central error handling
- Unit and basic integration tests
- Environment configuration
- Structured logging
- Endpoint index
- Automated formatting and tests

Usually avoid:

- Microservices
- Message brokers
- Multiple databases
- Complex internal frameworks
- Large shared abstraction layers

### Medium

Add:

- Explicit module boundaries
- Application and infrastructure separation
- Contract tests
- Database migration discipline
- Background job standards
- Metrics and tracing
- Architecture decisions
- Ownership documentation
- Deployment runbooks
- Security scanning

### Large

Add:

- Service and event catalogs
- Explicit team ownership
- Versioned contracts
- Compatibility policies
- Platform-level observability
- SLOs and error budgets
- Incident management
- Automated governance
- Resilience and load testing
- Data classification
- Disaster-recovery procedures
- Dependency and supply-chain controls

---

## 36. Final Engineering Rules

1. Build the simplest architecture that safely satisfies current requirements.
2. Organize code around business capabilities.
3. Keep business logic independent from frameworks and infrastructure.
4. Make dependencies and side effects explicit.
5. Use DRY for duplicated knowledge, not superficial similarity.
6. Validate every external input.
7. Enforce authorization server-side.
8. Give every external call a timeout.
9. Retry only when safe.
10. Design retried operations for idempotency.
11. Keep database migrations backward-compatible.
12. Never expose secrets or internal errors.
13. Test behavior at the correct level.
14. Treat observability as part of implementation.
15. Keep API, event, and code pointers discoverable.
16. Document significant architectural decisions.
17. Measure before optimizing.
18. Refactor incrementally.
19. Keep changes focused and reviewable.
20. Prefer boring, explicit, maintainable code.

---

## 37. Required Documentation Checklist

Every project should create and maintain:

```text
README.md
docs/
├── architecture.md
├── endpoint-index.md
├── error-catalog.md
├── configuration-reference.md
├── development-setup.md
├── testing.md
└── decisions/
```

Medium and large projects should additionally maintain:

```text
docs/
├── service-catalog.md
├── event-catalog.md
├── database-map.md
├── dependency-map.md
├── security-model.md
├── deployment.md
├── monitoring.md
├── incident-response.md
└── runbooks/
```

Each pointer document should include:

- Last reviewed date
- Owner
- Source-of-truth links or file paths
- Related tests
- Related contracts
- Operational links where relevant

Stale documentation is a defect. Update it in the same change that modifies the documented behavior.

---

> The standard is successful when engineers can locate behavior quickly, understand it safely, modify it confidently, test it reliably, and operate it without relying on tribal knowledge.

```

```
