````markdown
# GOATED GENIUS FRONTEND STRUCTURE AND DESIGN STANDARD

> A technology-neutral standard for building scalable, accessible, secure, responsive, consistent, and maintainable frontend applications.

---

## 1. Mission

A great frontend must be:

- Understandable
- Predictable
- Accessible
- Responsive
- Fast
- Secure
- Testable
- Consistent
- Easy to modify
- Pleasant to use

The goal is not visual complexity. The goal is to help users complete tasks clearly, safely, and efficiently.

---

## 2. Core Principles

1. Organize code around product features.
2. Keep business rules outside presentation components.
3. Prefer composition over inheritance.
4. Keep components focused.
5. Use one clear source of truth for each state.
6. Make loading, empty, error, and success states explicit.
7. Design mobile-first.
8. Accessibility is mandatory.
9. Use design tokens instead of scattered visual values.
10. Reuse stable concepts, not coincidentally similar markup.
11. Measure performance before optimizing.
12. Never trust client-side validation for security.
13. Keep framework-specific code away from core business logic.
14. Prefer boring, explicit code over clever abstractions.
15. Delete unused code instead of preserving it “just in case.”

---

## 3. Project Size and Architecture

### Small project

Usually:

- One to three developers
- Limited features
- Simple state
- One frontend application

Recommended:

```text
src/
├── app/
├── features/
├── shared/
├── assets/
├── styles/
└── main.*
```
````

Use:

- Feature folders
- Local state by default
- A small shared component library
- Simple routing
- Direct API modules
- Minimal abstraction

Avoid:

- Micro-frontends
- Complex state frameworks
- Generic component factories
- Excessive architectural layers
- Premature internal packages

### Medium project

Usually:

- Multiple product areas
- Several developers
- Shared design system
- Complex server interactions
- Multiple roles and workflows

Recommended:

```text
src/
├── app/
├── pages/
├── features/
├── entities/
├── shared/
├── services/
└── testing/
```

Add:

- Clear public module APIs
- Dedicated server-state management
- Feature ownership
- Contract-generated API types where practical
- Visual regression testing
- Accessibility automation
- Design-system documentation

### Large project

Usually:

- Multiple teams
- Several applications
- Shared platform capabilities
- Independent product domains

Recommended:

```text
repository/
├── apps/
├── packages/
├── docs/
├── tooling/
└── infrastructure/
```

Use independent deployments or micro-frontends only when autonomous teams and deployment boundaries genuinely require them.

---

## 4. Standard Small-Project Structure

```text
project/
├── README.md
├── docs/
│   ├── frontend-architecture.md
│   ├── design-system.md
│   ├── route-index.md
│   └── component-index.md
├── public/
├── src/
│   ├── app/
│   │   ├── providers/
│   │   ├── routing/
│   │   ├── layouts/
│   │   ├── error-boundary/
│   │   └── app.*
│   ├── features/
│   │   ├── authentication/
│   │   ├── profile/
│   │   └── checkout/
│   ├── shared/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── utilities/
│   │   ├── types/
│   │   └── constants/
│   ├── services/
│   │   └── api/
│   ├── assets/
│   ├── styles/
│   └── main.*
├── tests/
│   └── e2e/
├── .env.example
└── project configuration files
```

---

## 5. Standard Medium-Project Structure

```text
src/
├── app/
│   ├── providers/
│   ├── routing/
│   ├── layouts/
│   ├── configuration/
│   ├── error-boundaries/
│   └── initialization/
├── pages/
│   ├── home/
│   ├── account/
│   ├── orders/
│   └── not-found/
├── features/
│   ├── sign-in/
│   ├── update-profile/
│   ├── create-order/
│   └── process-payment/
├── entities/
│   ├── user/
│   ├── order/
│   └── payment/
├── widgets/
│   ├── navigation/
│   ├── order-summary/
│   └── account-menu/
├── shared/
│   ├── ui/
│   ├── hooks/
│   ├── utilities/
│   ├── validation/
│   ├── types/
│   ├── constants/
│   ├── formatting/
│   └── accessibility/
├── services/
│   ├── api/
│   ├── analytics/
│   ├── authentication/
│   └── monitoring/
├── styles/
├── assets/
└── testing/
```

### Dependency direction

```text
app
 ↓
pages
 ↓
widgets
 ↓
features
 ↓
entities
 ↓
shared
```

Lower layers must not import higher layers.

Avoid circular dependencies and imports into another module’s private files.

---

## 6. Standard Large-Project Structure

```text
repository/
├── apps/
│   ├── customer-web/
│   ├── admin-web/
│   └── marketing-site/
├── packages/
│   ├── design-system/
│   ├── api-client/
│   ├── authentication/
│   ├── observability/
│   ├── accessibility/
│   ├── configuration/
│   ├── testing/
│   └── linting/
├── docs/
│   ├── frontend-architecture.md
│   ├── application-catalog.md
│   ├── route-index.md
│   ├── design-system.md
│   ├── accessibility.md
│   ├── performance.md
│   └── decisions/
├── tooling/
└── scripts/
```

Shared packages must have:

- A clear owner
- A narrow purpose
- A documented public API
- Independent tests
- Versioning rules
- Migration guidance
- Compatibility expectations

Do not create a shared package for code used by only one feature.

---

## 7. Feature Structure

A mature feature may use:

```text
features/create-order/
├── api/
│   ├── create-order.*
│   └── create-order.types.*
├── components/
│   ├── create-order-form.*
│   └── order-item-field.*
├── hooks/
│   └── use-create-order.*
├── model/
│   ├── state.*
│   ├── validation.*
│   └── transformations.*
├── tests/
├── index.*
└── README.md
```

Use only folders that provide real value.

### Public feature API

Expose intended imports through one public entry point:

```text
features/create-order/index.*
```

Other modules should not import private implementation files:

```text
Bad:
features/create-order/components/internal/private-field

Good:
features/create-order
```

---

## 8. Responsibility Boundaries

### App layer

Owns:

- Application startup
- Global providers
- Routing
- Global layouts
- Error boundaries
- Global configuration
- Authentication initialization

### Pages

Own:

- Route-level composition
- Page titles and metadata
- Page-level loading and error states
- Coordination of features and widgets

Pages should contain little business logic.

### Features

Own user capabilities:

- Sign in
- Add item to cart
- Submit payment
- Update profile
- Search products

### Entities

Own reusable domain concepts:

- User
- Product
- Order
- Payment

### Shared

Own domain-neutral code:

- Buttons
- Dialogs
- Formatting
- Generic hooks
- Accessibility helpers
- Primitive utilities

Shared code must not contain product-specific business rules.

---

## 9. Component Standards

A component should:

- Have one clear purpose
- Have a descriptive name
- Receive an understandable API
- Render predictably
- Handle required UI states
- Be accessible
- Avoid unrelated data fetching
- Avoid hidden global dependencies

### Prefer composition

```text
<Card>
  <CardHeader />
  <CardContent />
  <CardActions />
</Card>
```

Avoid components with dozens of configuration flags:

```text
<Card
  compact
  clickable
  showHeader
  noPadding
  darkMode
  useSpecialBorder
  renderAsModal
/>
```

When behaviors differ significantly, create explicit variants or separate components.

### Component size

Do not enforce arbitrary line limits.

Extract code when:

- A section has its own responsibility
- Logic can be named clearly
- A pattern is reused
- Testing becomes easier
- The parent becomes difficult to understand

Do not split every small block into a component.

---

## 10. Component Categories

### Primitive components

Basic design-system building blocks:

```text
Button
Input
TextArea
Checkbox
Radio
Select
Icon
Text
Heading
Stack
Grid
Container
Divider
```

### Composite components

Combinations of primitives:

```text
FormField
SearchBox
DatePicker
Pagination
DataTable
Modal
Toast
NavigationMenu
```

### Feature components

Product-specific interfaces:

```text
CheckoutForm
OrderSummary
UserProfileCard
PaymentStatus
```

### Page components

Route-level composition:

```text
CheckoutPage
OrdersPage
AccountSettingsPage
```

Do not put product-specific logic into primitive design-system components.

---

## 11. Naming Standards

Use names that communicate meaning.

Prefer:

```text
OrderSummary
PaymentMethodForm
useCurrentUser
formatCurrency
isCheckoutAvailable
```

Avoid:

```text
Component1
DataBox
Manager
Helper
Thing
handleStuff
processData
common
misc
```

### Event handlers

Use names that explain the event:

```text
handleSubmit
handleOrderCancel
handleSearchChange
```

Passed callback props may use:

```text
onSubmit
onOrderCancel
onSearchChange
```

### Boolean names

Use question-like names:

```text
isLoading
hasPermission
canEdit
shouldRetry
```

Avoid ambiguous negation:

```text
notDisabled
noError
```

---

## 12. State Management

Classify state before choosing where it belongs.

### Local UI state

Examples:

- Modal visibility
- Active tab
- Input focus
- Temporary selection

Keep it near the component using it.

### Form state

Examples:

- Field values
- Validation errors
- Submission status
- Dirty state

Keep it within the form feature unless multiple areas genuinely share it.

### Server state

Examples:

- User profile
- Orders
- Search results
- Notifications

Use a dedicated server-state strategy that supports:

- Caching
- Deduplication
- Refetching
- Loading state
- Error state
- Invalidation
- Cancellation

Do not copy server data into global client state without a reason.

### URL state

Examples:

- Search query
- Filters
- Sort order
- Pagination
- Selected tab when shareable

Use the URL when the state should survive refresh, navigation, bookmarking, or sharing.

### Global client state

Use only for truly application-wide state:

- Authenticated identity
- Locale
- Theme
- Feature flags
- Global notifications

Do not place all application state into one global store.

### Derived state

Calculate derived values instead of synchronizing duplicated state.

Bad:

```text
items
itemCount
subtotal
formattedSubtotal
```

Prefer storing `items` and deriving the other values.

---

## 13. Data Fetching

Centralize transport concerns:

```text
services/api/
├── client.*
├── errors.*
├── interceptors.*
├── generated/
└── modules/
```

The API client should own:

- Base URL
- Authentication attachment
- Safe error conversion
- Request cancellation
- Timeouts
- Correlation identifiers
- Response parsing

Feature-level API modules should own:

- Endpoints
- Request and response types
- Domain mapping
- Cache keys
- Invalidation behavior

### Every request needs defined states

```text
Idle
Loading
Success
Empty
Error
Refreshing
```

Do not show blank pages during loading or failure.

### Prevent stale responses

Cancel or ignore obsolete requests when:

- Search terms change
- Routes change
- Components unmount
- A newer mutation supersedes an older one

### Mutations

For data-changing operations:

1. Prevent accidental duplicate submission.
2. Show progress.
3. Handle success.
4. Handle safe retries.
5. Invalidate or update affected data.
6. Restore controls after failure.
7. Preserve user input where appropriate.

Use optimistic updates only when rollback behavior is clear and safe.

---

## 14. API Type Boundaries

Do not use raw API response objects everywhere.

Separate:

```text
API response type
→ validation/parsing
→ frontend domain model
→ view model
```

Benefits:

- Backend changes remain localized
- Dates and money are normalized
- Nullable values are handled once
- Components receive predictable data
- Unsafe fields do not spread throughout the application

Treat API responses as untrusted input.

---

## 15. Forms

Every form must define:

- Initial values
- Validation rules
- Required fields
- Submission behavior
- Loading behavior
- Success feedback
- Error feedback
- Reset behavior
- Unsaved-change behavior
- Accessibility requirements

### Validation

Use:

- Client validation for immediate feedback
- Server validation as the authority

Display errors:

- Near the relevant field
- In understandable language
- Without removing entered values
- With focus moved to an error summary when appropriate

Do not validate only on every keystroke when it creates a hostile experience.

### Submission

- Prevent unintended duplicate submissions.
- Do not permanently disable submission after a recoverable error.
- Preserve user data after failure.
- Clearly communicate whether the operation succeeded.
- Require confirmation for destructive operations.
- Require reauthentication for highly sensitive operations when necessary.

---

## 16. Routing

Every route should define:

- Path
- Page
- Required authentication
- Required permissions
- Loading behavior
- Error behavior
- Page title
- Metadata where relevant
- Ownership

Recommended documentation:

```text
docs/frontend/route-index.md
```

Template:

```markdown
| Route          | Purpose           | Access | Page             | Owner    | Tests               |
| -------------- | ----------------- | ------ | ---------------- | -------- | ------------------- |
| `/sign-in`     | Authenticate user | Public | `SignInPage`     | Identity | `sign-in.e2e.*`     |
| `/orders`      | List user orders  | User   | `OrdersPage`     | Orders   | `orders.e2e.*`      |
| `/admin/users` | Manage users      | Admin  | `UsersAdminPage` | Identity | `admin-users.e2e.*` |
```

Client-side route guards improve experience but do not provide security. The server must enforce authorization.

---

## 17. Layout Standards

Use predictable layout primitives:

```text
Stack
Inline
Grid
Container
Section
SidebarLayout
CenteredLayout
```

Avoid scattered one-off margins.

### Spacing

Use a consistent spacing scale:

```text
0
4
8
12
16
24
32
48
64
```

The exact values may differ, but the scale must be intentional.

Prefer parent-controlled spacing using layout gaps rather than child components adding arbitrary external margins.

### Content width

Readable text should not stretch across extremely wide screens.

Use appropriate maximum widths for:

- Articles
- Forms
- Dashboards
- Tables
- Dialogs

### Alignment

Align related elements consistently. Do not center everything by default.

---

## 18. Responsive Design

Design mobile-first.

Start with the smallest supported viewport, then progressively enhance.

Use content-driven breakpoints rather than targeting specific devices.

Example conceptual breakpoints:

```text
Small: base styles
Medium: two-column opportunities
Large: expanded navigation and content
Extra-large: constrained centered layout
```

Test:

- Narrow mobile
- Wide mobile
- Tablet
- Laptop
- Large desktop
- Zoomed interfaces
- Landscape orientation

### Responsive rules

- Avoid fixed widths for primary layouts.
- Allow text to wrap.
- Preserve minimum touch-target sizes.
- Make tables scroll, reflow, or transform intentionally.
- Do not hide critical actions without an alternative.
- Avoid hover-only interaction.
- Never assume pointer precision.
- Respect safe areas where relevant.

---

## 19. Design Tokens

Centralize visual decisions as tokens.

```text
Color
Typography
Spacing
Radius
Border
Shadow
Motion
Layer
Breakpoint
Size
```

Example:

```text
color.background.default
color.background.surface
color.text.primary
color.text.secondary
color.border.default
color.action.primary
color.status.danger

space.1
space.2
space.3
space.4

radius.small
radius.medium
radius.large
```

Components should use semantic tokens rather than raw values.

Bad:

```text
color: #ff4f00
margin-top: 13px
z-index: 999999
```

Good:

```text
color: action.primary
margin-top: space.3
z-index: layer.modal
```

---

## 20. Color Standards

Color must have semantic meaning.

Define roles such as:

- Background
- Surface
- Primary text
- Secondary text
- Border
- Primary action
- Success
- Warning
- Danger
- Information
- Focus

Never communicate state using color alone.

Combine color with:

- Text
- Icons
- Patterns
- Labels
- Position

Check contrast in all themes and interaction states.

Do not use arbitrary shades outside the design tokens.

---

## 21. Typography

Define:

- Font families
- Font sizes
- Font weights
- Line heights
- Letter spacing
- Heading hierarchy
- Body styles
- Labels
- Captions
- Code styles

Recommended principles:

- Body text must remain comfortably readable.
- Use a consistent typographic scale.
- Keep line lengths manageable.
- Do not use tiny text for important information.
- Do not use font weight alone to communicate meaning.
- Preserve semantic heading order.
- Avoid excessive all-uppercase text.
- Support user zoom and text scaling.

Visual size and semantic heading level are separate concerns.

---

## 22. Icons

Icons should:

- Have consistent style and sizing
- Use accessible labels when interactive
- Be hidden from assistive technology when decorative
- Usually appear with text for unfamiliar actions
- Never be the only indicator of critical state

Do not use ambiguous icons without tooltips or labels.

Use one maintained icon system rather than unrelated icon styles.

---

## 23. Buttons and Actions

Use clear hierarchy:

- Primary: main action
- Secondary: alternative action
- Tertiary: low-emphasis action
- Destructive: dangerous action

A page should usually have one visually dominant primary action per context.

Buttons must have states:

```text
Default
Hover
Focus
Active
Disabled
Loading
```

Rules:

- Use buttons for actions.
- Use links for navigation.
- Do not use clickable non-interactive elements.
- Loading buttons should preserve enough width to avoid layout shifts.
- Disabled controls should not be the only explanation of unavailable actions.
- Destructive actions require clear wording and appropriate confirmation.

Prefer:

```text
Delete account
Cancel order
Save changes
```

Avoid:

```text
Yes
Proceed
Click here
Submit
```

when a more specific label is possible.

---

## 24. Feedback and Status

Every user action should produce understandable feedback.

Use:

- Inline messages for field-specific issues
- Banners for page-level conditions
- Toasts for temporary non-critical confirmation
- Dialogs for decisions requiring immediate attention
- Progress indicators for ongoing work

Do not use temporary toasts as the only place for critical errors.

Messages should explain:

1. What happened
2. Why, when safely known
3. What the user can do next

---

## 25. Loading States

Choose loading indicators based on context:

- Button spinner for an action
- Skeleton for structured content
- Inline progress for local loading
- Page loading state for route transitions
- Progress bar for measurable operations

Avoid:

- Full-screen spinners for small updates
- Fake progress percentages
- Constant layout shifts
- Removing existing content during background refresh
- Showing a spinner forever without timeout or error handling

For slower operations, explain what is happening.

---

## 26. Empty States

An empty state should explain:

- What is empty
- Why it may be empty
- What the user can do next

Examples:

```text
No orders yet.
Create your first order to see it here.
[Create order]
```

Differentiate:

- No data exists
- Search returned no results
- Filters removed all results
- User lacks access
- Data failed to load

Do not show the same empty state for all conditions.

---

## 27. Error States

Design errors deliberately.

### Field error

Placed near the field and associated programmatically.

### Section error

Affects one component or panel.

### Page error

Prevents the primary page task.

### Global error

Affects the entire application or session.

Provide recovery actions where possible:

```text
Retry
Return to dashboard
Sign in again
Contact support
Reset filters
```

Never expose stack traces, internal paths, tokens, or raw server errors.

Use an application error boundary for unexpected rendering failures, but do not use it as ordinary error handling.

---

## 28. Dialogs and Overlays

Use dialogs only when interruption is necessary.

Dialogs must:

- Have an accessible name
- Move focus inside when opened
- Trap focus appropriately
- Restore focus when closed
- Support keyboard dismissal when safe
- Prevent background interaction
- Remain usable on small screens

Avoid nested dialogs.

Do not use dialogs for content that deserves a page or ordinary inline section.

---

## 29. Navigation

Navigation should be:

- Predictable
- Consistent
- Keyboard accessible
- Responsive
- Clear about the current location

Use:

- Clear labels
- Active states
- Breadcrumbs for deep hierarchies
- Back navigation that matches user expectations
- Stable placement for primary navigation

Do not hide core navigation behind unclear icons on large screens.

Preserve meaningful route state when users return to listings.

---

## 30. Tables and Data-Dense Interfaces

Tables should support:

- Accessible headers
- Clear alignment
- Sorting indicators
- Loading states
- Empty states
- Error states
- Pagination
- Responsive behavior
- Keyboard interaction where needed

Rules:

- Align numeric data consistently.
- Keep actions discoverable.
- Do not overload rows with icons.
- Use confirmation for destructive bulk actions.
- Preserve selections intentionally across pagination.
- Communicate total and selected counts.
- Provide alternatives for narrow screens when necessary.

Use virtualization only for genuinely large datasets and test its accessibility.

---

## 31. Accessibility Standard

Target recognized accessibility requirements appropriate to the product and applicable law.

### Semantic HTML

Use native elements whenever possible:

```text
button
a
input
select
textarea
label
nav
main
header
footer
section
table
dialog
```

Do not recreate native behavior with generic containers unless necessary.

### Keyboard

All interactive functionality must work with a keyboard.

Test:

- Logical tab order
- Visible focus
- No keyboard traps
- Dialog focus management
- Menus
- Forms
- Custom controls
- Skip navigation
- Escape behavior where appropriate

### Focus

- Never remove focus indicators without an accessible replacement.
- Move focus only when the user benefits.
- Restore focus after overlays close.
- Focus the first meaningful error after failed submission when appropriate.

### Images

- Informative images require useful alternative text.
- Decorative images use empty alternative text.
- Complex visuals require equivalent descriptions.
- Do not repeat nearby visible text unnecessarily.

### Forms

- Every control has a label.
- Required state is communicated.
- Errors are programmatically associated.
- Instructions do not rely only on placeholders.
- Related controls use appropriate grouping.

### Motion

Respect reduced-motion preferences.

Avoid:

- Unnecessary parallax
- Flashing content
- Large unexpected movement
- Motion required to understand content

### Screen readers

Test critical workflows with screen-reader software, not automation alone.

---

## 32. Internationalization

Design for:

- Longer translated text
- Right-to-left layouts
- Locale-specific dates
- Locale-specific numbers
- Locale-specific currencies
- Pluralization
- Time zones
- Different name and address formats

Do not concatenate translated sentence fragments.

Bad:

```text
"You have " + count + " messages"
```

Use translation messages supporting grammar and plural rules.

Do not store user-visible strings throughout components when localization is required.

---

## 33. Themes and Dark Mode

Themes should use semantic design tokens.

Do not create dark mode by simply inverting colors.

Verify:

- Contrast
- Images and illustrations
- Shadows
- Borders
- Focus indicators
- Syntax highlighting
- Status colors
- Native form controls
- Loading skeletons

Respect system preference unless the user has explicitly selected a theme.

Persist preferences without causing visible theme flashing.

---

## 34. Motion and Animation

Animation should communicate:

- Relationship
- State change
- Progress
- Hierarchy
- Cause and effect

Motion should be:

- Brief
- Consistent
- Interruptible
- Non-essential
- Reduced when requested

Avoid decorative animation that delays tasks.

Use shared motion tokens:

```text
duration.fast
duration.normal
duration.slow
easing.standard
easing.enter
easing.exit
```

---

## 35. Performance

Define measurable performance budgets.

Monitor:

- Initial load
- Largest visible content
- Interaction responsiveness
- Layout stability
- JavaScript size
- Image weight
- Request count
- Rendering cost
- Memory usage

### Performance practices

- Ship only required code.
- Split code by route or meaningful feature.
- Lazy-load non-critical functionality.
- Optimize images.
- Use responsive image sizes.
- Preload only critical resources.
- Cache versioned static assets.
- Avoid unnecessary re-renders.
- Avoid large client-side dependencies.
- Paginate large datasets.
- Virtualize only when justified.
- Prevent layout shifts by reserving space.
- Cancel obsolete requests.
- Render critical content early.

Do not sacrifice accessibility or correctness for small benchmark improvements.

---

## 36. Images and Media

- Use the appropriate format.
- Compress assets.
- Provide responsive sizes.
- Lazy-load below-the-fold media.
- Reserve dimensions to prevent layout shifts.
- Provide text alternatives.
- Include captions or transcripts when required.
- Avoid autoplaying audio.
- Provide controls for motion and playback.

Do not ship desktop-sized images to narrow mobile screens.

---

## 37. Frontend Security

The frontend must:

- Escape and encode untrusted content
- Avoid unsafe HTML rendering
- Sanitize permitted rich text
- Avoid exposing secrets
- Avoid storing sensitive tokens insecurely
- Use secure authentication flows
- Avoid sensitive data in URLs
- Validate redirect destinations
- Avoid dynamic code evaluation
- Keep dependencies updated
- Use restrictive browser security policies
- Redact sensitive analytics data

Remember:

- Frontend authorization is not security enforcement.
- Hidden controls do not prevent API access.
- Environment variables bundled into frontend code are public.
- Obfuscation does not protect secrets.
- Client-side validation does not replace server validation.

---

## 38. Analytics and Privacy

Analytics must be intentional.

Document:

- Events collected
- Purpose
- Data fields
- Retention
- Owner
- Consent requirements
- Sensitive-data restrictions

Never capture:

- Passwords
- Authentication tokens
- Payment details
- Secret keys
- Sensitive form contents
- Unnecessary personal information

Avoid duplicate or inconsistent analytics events.

Use stable event names:

```text
checkout_started
checkout_completed
checkout_failed
```

Do not tightly couple product behavior to optional analytics delivery.

---

## 39. Testing Strategy

### Unit tests

Use for:

- Pure utilities
- Validation
- Formatters
- Reducers
- Domain transformations
- Complex hooks or state machines

### Component tests

Use for:

- Rendering behavior
- User interaction
- Accessibility
- Loading and error states
- Forms
- Permission-based presentation

Test through visible behavior rather than private implementation.

### Integration tests

Use for:

- Feature workflows
- Routing
- API interactions
- Provider integration
- Cache invalidation
- Error recovery

### End-to-end tests

Use for critical journeys:

- Registration
- Sign in
- Account recovery
- Checkout
- Payment
- Role-restricted actions
- Important data changes

### Visual regression tests

Use for:

- Design-system components
- Important pages
- Responsive layouts
- Theme variants
- Error and empty states

### Accessibility tests

Use both:

- Automated accessibility checking
- Manual keyboard and screen-reader testing

Automation cannot prove complete accessibility.

---

## 40. What Every Component Test Should Consider

Where applicable, test:

- Default rendering
- User interaction
- Keyboard interaction
- Loading
- Empty data
- Failure
- Disabled state
- Permission restrictions
- Long text
- Missing optional data
- Narrow viewport
- Theme variation
- Accessibility name and role

Avoid snapshots that record huge implementation trees without meaningful assertions.

---

## 41. Design System

Recommended structure:

```text
packages/design-system/
├── src/
│   ├── tokens/
│   ├── primitives/
│   ├── components/
│   ├── patterns/
│   ├── icons/
│   └── utilities/
├── docs/
├── tests/
└── index.*
```

Every design-system component should document:

- Purpose
- Anatomy
- Variants
- States
- Accessibility
- Keyboard behavior
- Responsive behavior
- Content guidance
- Examples
- Anti-patterns

Do not add a component merely because one screen needs a unique visual arrangement. Determine whether it is a reusable product pattern first.

---

## 42. Component Pointer Documentation

Maintain:

```text
docs/frontend/component-index.md
```

Template:

```markdown
| Component    | Category  | Purpose               | Source                   | Accessibility docs          | Examples               |
| ------------ | --------- | --------------------- | ------------------------ | --------------------------- | ---------------------- |
| Button       | Primitive | Trigger an action     | `shared/ui/button`       | `docs/components/button.md` | Design-system examples |
| Dialog       | Composite | Interruptive decision | `shared/ui/dialog`       | `docs/components/dialog.md` | Design-system examples |
| OrderSummary | Feature   | Display order totals  | `features/order-summary` | Feature README              | Checkout page          |
```

Also maintain:

```text
docs/frontend/
├── route-index.md
├── component-index.md
├── feature-index.md
├── state-map.md
├── api-map.md
├── analytics-events.md
├── accessibility.md
└── performance-budget.md
```

### Feature index template

```markdown
| Feature | Purpose            | Owner    | Route usage | API usage        | Source             | Tests           |
| ------- | ------------------ | -------- | ----------- | ---------------- | ------------------ | --------------- |
| Sign in | Authenticate users | Identity | `/sign-in`  | `POST /sessions` | `features/sign-in` | `sign-in.e2e.*` |
```

---

## 43. Documentation Requirements

Every frontend project should include:

```text
README.md
docs/frontend/
├── architecture.md
├── route-index.md
├── component-index.md
├── design-system.md
├── accessibility.md
├── testing.md
├── performance-budget.md
└── decisions/
```

The README should explain:

- Purpose
- Requirements
- Setup
- Environment configuration
- Running locally
- Testing
- Building
- Deployment
- Architecture
- Main routes
- Documentation links
- Ownership

Documentation changes belong in the same change as the behavior they describe.

---

## 44. Code Quality

### Functions

Functions should:

- Do one clear thing
- Use descriptive names
- Avoid hidden side effects
- Return predictable types
- Keep parameters understandable

### Hooks or reusable logic

Reusable hooks should represent coherent behavior.

Good:

```text
useCurrentUser
useOrderSearch
useUnsavedChangesWarning
```

Bad:

```text
useEverything
useHelper
useCommonLogic
```

Do not hide entire feature architectures inside giant hooks.

### Effects

Use effects for synchronization with external systems, not ordinary derived state.

Review every effect for:

- Dependencies
- Cleanup
- Request cancellation
- Repeated execution
- Race conditions
- Stale closures
- Strict-mode behavior
- Server-rendering compatibility

### Comments

Explain why, not what.

Document:

- Browser workarounds
- Accessibility constraints
- Performance tradeoffs
- Compatibility decisions
- Temporary fixes with tracking references

---

## 45. Styling Standards

Choose one primary styling strategy and apply it consistently.

Rules:

- Use design tokens.
- Keep feature-specific styles near the feature.
- Keep global styles minimal.
- Avoid excessive selector specificity.
- Avoid arbitrary `!important`.
- Avoid global element overrides from feature code.
- Avoid inline visual values when reusable tokens exist.
- Remove unused styles.
- Support all required themes.
- Test responsive and zoom behavior.

Use semantic component variants instead of scattered style overrides.

---

## 46. Layer and Z-Index Management

Use a documented layer scale:

```text
Base content
Sticky content
Dropdown
Overlay
Modal
Toast
Critical system message
```

Do not solve stacking problems using random enormous numbers.

Overlay components should use a shared layering system and predictable rendering location.

---

## 47. Browser and Device Support

Document supported browsers and devices based on actual users and business requirements.

Define:

- Minimum versions
- Mobile support
- Tablet support
- Touch support
- Required assistive technology support
- Degradation strategy

Use progressive enhancement.

Do not break the entire experience when a non-essential browser capability is unavailable.

Test on real browsers and devices for critical workflows.

---

## 48. Error Monitoring

Frontend monitoring should capture:

- Unexpected exceptions
- Failed API calls
- Route failures
- Resource loading failures
- Performance regressions
- Important workflow failures

Include safe context:

- Application version
- Route
- Browser class
- Correlation identifier
- Feature
- Sanitized error information

Never include:

- Tokens
- Passwords
- Full request bodies
- Sensitive user data
- Private page content

Source maps must be handled according to the organization’s exposure policy.

---

## 49. Release Standards

Before release:

- Formatting passes
- Linting passes
- Type checking passes
- Unit tests pass
- Component tests pass
- Critical end-to-end tests pass
- Accessibility checks pass
- Security checks pass
- Bundle budgets pass
- Visual changes are reviewed
- Responsive behavior is verified
- Browser support is verified
- Documentation is updated
- Monitoring is ready
- Rollback is understood

Use staged rollout or feature flags for high-risk changes.

Feature flags must have:

- Owner
- Purpose
- Default value
- Expiration plan
- Removal task

Do not allow permanent abandoned flags.

---

## 50. Pull Request Review Checklist

Review:

### Structure

- Is the code in the correct feature or layer?
- Are dependency boundaries respected?
- Is shared code genuinely reusable?
- Are imports using public APIs?

### Behavior

- Are requirements satisfied?
- Are all UI states handled?
- Are race conditions possible?
- Can actions execute twice accidentally?

### Design

- Are design tokens used?
- Is hierarchy clear?
- Is spacing consistent?
- Does it work at required viewport sizes?
- Are dark and light themes supported where required?

### Accessibility

- Is semantic HTML used?
- Does keyboard navigation work?
- Is focus managed?
- Are labels and error relationships correct?
- Is contrast sufficient?
- Is motion respectful?

### Performance

- Is unnecessary code shipped?
- Are expensive renders justified?
- Are images optimized?
- Are requests deduplicated or cancelled?
- Are layout shifts prevented?

### Security

- Is unsafe HTML introduced?
- Is sensitive data stored or logged?
- Is frontend-only authorization being mistaken for enforcement?
- Are redirects and URLs validated?
- Are dependencies trustworthy?

### Testing

- Is behavior tested?
- Are loading, empty, and failure states covered?
- Are critical user journeys protected?
- Are assertions based on user-visible outcomes?

---

## 51. Anti-Patterns

Avoid:

- One enormous application component
- One global state store for everything
- Business logic inside presentational components
- API calls scattered across arbitrary components
- Copy-pasted design values
- Giant “shared” folders
- Components with dozens of boolean props
- Deep prop drilling when a clear boundary exists
- Context providers for tiny local state
- Effects for derived values
- Unstable list keys
- Unbounded rendering of large datasets
- Clickable generic containers
- Placeholder-only form labels
- Color-only status indicators
- Hover-only functionality
- Tiny touch targets
- Random z-index values
- Permanent loading spinners
- Raw server error messages
- Unsafe HTML rendering
- Frontend-stored secrets
- Testing private implementation details
- Huge meaningless snapshots
- Premature micro-frontends
- Premature design-system abstractions
- Rebuilding native controls without need

---

## 52. Feature Development Workflow

For every frontend feature:

1. Understand the user goal.
2. Identify the owning feature and route.
3. Define all user states.
4. Define data requirements.
5. Define authorization assumptions.
6. Define responsive behavior.
7. Define keyboard and screen-reader behavior.
8. Reuse existing design-system components.
9. Implement the smallest coherent solution.
10. Add loading, empty, success, and error handling.
11. Add appropriate tests.
12. Review performance.
13. Review security and privacy.
14. Update route, feature, component, or analytics indexes.
15. Verify on required browsers and viewports.
16. Monitor after deployment.

---

## 53. Definition of Done

A frontend change is complete when applicable requirements are satisfied:

- User goal is achieved.
- Code is in the correct architectural layer.
- Component responsibilities are clear.
- All interface states are handled.
- API failures are recoverable where possible.
- Forms preserve user input after failure.
- Responsive behavior is verified.
- Keyboard navigation works.
- Focus behavior is correct.
- Screen-reader semantics are appropriate.
- Contrast requirements are met.
- Design tokens are used.
- Security and privacy are reviewed.
- Tests cover important behavior.
- Performance remains within budget.
- Monitoring is adequate.
- Documentation and pointer files are updated.
- Automated checks pass.
- Rollback or feature disablement is possible.

---

## 54. Instructions for an AI Frontend Engineer

When implementing frontend work:

1. Inspect the architecture and design system first.
2. Identify the correct feature, entity, page, and shared boundaries.
3. Reuse existing components before creating new ones.
4. Do not introduce dependencies without necessity.
5. Do not place secrets in frontend code.
6. Do not treat route guards as authorization.
7. Enumerate loading, empty, error, success, disabled, and unauthorized states.
8. Use semantic HTML before custom behavior.
9. Design keyboard interaction explicitly.
10. Use design tokens instead of arbitrary values.
11. Design mobile-first.
12. Keep business logic outside presentation components.
13. Keep server state separate from local UI state.
14. Place shareable navigation state in the URL.
15. Derive values instead of duplicating state.
16. Cancel or ignore obsolete requests.
17. Avoid unsafe HTML.
18. Test user-visible behavior.
19. Add accessibility tests and perform manual checks.
20. Measure performance before adding optimizations.
21. Update documentation indexes.
22. Explain architectural tradeoffs.
23. State uncertainty instead of inventing requirements.
24. Prefer maintainable, explicit solutions over clever code.

Before coding, produce:

```markdown
## Frontend Implementation Plan

### User goal

### Existing architecture

### Owning feature and route

### Components to reuse

### New components required

### Data and API requirements

### State model

### Interface states

### Responsive behavior

### Accessibility behavior

### Security and privacy considerations

### Testing plan

### Documentation updates
```

After coding, report:

```markdown
## Frontend Implementation Summary

### Changes made

### Architecture decisions

### States handled

### Accessibility verification

### Responsive verification

### Tests added

### Performance impact

### Security considerations

### Documentation updated

### Remaining risks
```

---

## 55. Final Rules

1. Build around user goals and product features.
2. Keep architecture appropriate for project size.
3. Keep components focused.
4. Use composition.
5. Keep state as local as possible.
6. Separate local, form, server, URL, and global state.
7. Derive values instead of duplicating state.
8. Treat API responses as untrusted.
9. Handle every meaningful UI state.
10. Design mobile-first.
11. Use semantic HTML.
12. Support keyboard and assistive technology.
13. Never remove visible focus without replacement.
14. Never communicate using color alone.
15. Use design tokens.
16. Maintain clear visual hierarchy.
17. Keep interfaces responsive and fast.
18. Protect privacy and never expose secrets.
19. Test behavior rather than implementation.
20. Document routes, features, components, and decisions.
21. Prefer stable public module APIs.
22. Avoid premature shared abstractions.
23. Measure performance.
24. Remove dead code.
25. Build interfaces that remain understandable without tribal knowledge.

```

```
