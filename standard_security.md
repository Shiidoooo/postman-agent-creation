````markdown
# GOATED GENIUS WEBSITE SECURITY STANDARD

> A defensive, technology-neutral standard for designing, building, testing, deploying, and operating secure websites and web APIs.

## 1. Mission

Security is part of architecture and implementation—not a final checklist.

The system must protect:

- Confidentiality: data is visible only to authorized parties.
- Integrity: data and behavior cannot be changed improperly.
- Availability: legitimate users can reliably access the system.
- Privacy: personal data is collected and processed responsibly.
- Accountability: important actions can be traced safely.
- Resilience: failures and attacks do not become total compromise.

No system is “100% secure.” Use layered defenses so one failure does not expose everything.

---

## 2. Authorization and Safety Rules

Security testing must only target systems explicitly authorized by the owner.

Before testing, define:

- In-scope domains, APIs, IP addresses, and applications
- Permitted test types
- Prohibited destructive actions
- Testing window
- Rate limits
- Test accounts and roles
- Data-handling requirements
- Emergency contact
- Stop conditions

Never:

- Test unrelated systems
- Access real user data unnecessarily
- Perform denial-of-service testing without explicit permission
- Persist access
- Modify or delete production data
- expose secrets or vulnerability details publicly

Use non-production environments and synthetic data whenever possible.

---

## 3. Security Principles

### 3.1 Deny by default

Access is forbidden unless explicitly granted.

### 3.2 Least privilege

Users, services, databases, CI jobs, and administrators receive only the permissions they require.

### 3.3 Defense in depth

Use multiple independent protections:

```text
Edge protections
→ application validation
→ authentication
→ authorization
→ business-rule enforcement
→ database constraints
→ monitoring and alerting
```
````

### 3.4 Never trust input

Treat all external data as hostile, including:

- Form fields
- JSON and XML
- Query and path parameters
- Headers and cookies
- File uploads
- Webhooks
- Third-party API responses
- Database content displayed in another context
- Queue messages
- Client-generated identifiers

### 3.5 Secure defaults

The default configuration should be the safest practical configuration.

Debug mode, public storage, permissive CORS, default passwords, and verbose errors must not be enabled by default.

### 3.6 Minimize attack surface

Remove:

- Unused endpoints
- Unused dependencies
- Old API versions
- Sample applications
- Default accounts
- Unnecessary network ports
- Unused cloud permissions
- Debugging interfaces

### 3.7 Assume breach

Design as though one credential, service, dependency, or account may eventually be compromised. Limit blast radius and make suspicious behavior observable.

---

## 4. Security Documentation

Maintain:

```text
docs/security/
├── security-overview.md
├── threat-model.md
├── authentication.md
├── authorization-matrix.md
├── data-classification.md
├── security-testing.md
├── security-findings.md
├── dependency-register.md
├── security-headers.md
├── secrets-management.md
├── incident-response.md
└── security-changelog.md
```

For larger systems, also maintain:

```text
docs/security/
├── asset-inventory.md
├── abuse-cases.md
├── trust-boundaries.md
├── third-party-risk.md
├── vulnerability-management.md
├── disaster-recovery.md
└── penetration-test-scope.md
```

Every document should identify its owner and last review date.

---

## 5. Threat Modeling

Perform threat modeling:

- During initial design
- Before introducing sensitive features
- Before adding external integrations
- When trust boundaries change
- After serious incidents
- Before major releases

Document:

1. Assets that require protection
2. Entry points
3. Actors and roles
4. Trust boundaries
5. Data flows
6. External dependencies
7. Expected misuse and abuse cases
8. Existing controls
9. Remaining risk

Review threats involving:

- Identity spoofing
- Data tampering
- Information disclosure
- Repudiation
- Denial of service
- Privilege escalation
- Automated abuse
- Business-logic manipulation
- Supply-chain compromise

Example abuse cases:

- A customer attempts to access another customer’s order.
- A user changes a price supplied by the client.
- An attacker repeatedly requests password resets.
- A compromised administrator exports excessive data.
- A malicious file is uploaded and served as executable content.
- A webhook is forged or replayed.
- A server-side URL-fetch feature accesses internal infrastructure.

---

## 6. Asset and Data Classification

Classify information, for example:

- Public
- Internal
- Confidential
- Restricted

Restricted data may include:

- Passwords and authentication secrets
- Session tokens
- Private keys
- Financial information
- Government identifiers
- Health information
- Sensitive personal information

For every data category define:

- Purpose
- Owner
- Storage location
- Authorized roles
- Encryption requirements
- Retention period
- Deletion process
- Logging restrictions
- Backup treatment
- Third-party access

Collect only necessary data and retain it only as long as required.

---

## 7. Authentication

Use established authentication libraries and protocols. Do not invent cryptography or custom authentication schemes.

### Passwords

- Hash passwords using a modern adaptive password-hashing algorithm.
- Use a unique salt automatically provided by the algorithm.
- Configure work factors appropriate for current infrastructure.
- Never encrypt passwords for later recovery.
- Never log or email passwords.
- Permit password managers and password pasting.
- Support long passphrases.
- Reject known compromised passwords where practical.
- Avoid arbitrary periodic password rotation unless compromise is suspected.
- Rate-limit login attempts using multiple signals.
- Do not reveal whether an account exists.

### Multi-factor authentication

Require or strongly support MFA for:

- Administrators
- Privileged operations
- Sensitive data access
- Account recovery
- High-risk sign-ins

Prefer phishing-resistant methods where practical.

### Account recovery

Account recovery must not be weaker than ordinary authentication.

Recovery tokens must be:

- Cryptographically random
- Single-use
- Short-lived
- Stored securely
- Invalidated after successful use
- Protected against account enumeration

Notify users of important account changes without including secrets.

### Login protections

Consider:

- Rate limiting
- Progressive delay
- Suspicious-login detection
- Device and location notifications
- Credential-stuffing protections
- Risk-based verification
- Administrative alerts

Avoid permanent account lockouts that attackers can abuse to deny service.

---

## 8. Sessions and Tokens

### Cookie sessions

Authentication cookies should generally use:

```text
Secure
HttpOnly
SameSite=Lax or Strict
```

Use `SameSite=None` only when cross-site behavior is required, and pair it with `Secure`.

Additionally:

- Rotate the session identifier after login and privilege changes.
- Invalidate sessions after logout.
- Apply idle and absolute expiration.
- Allow users to review and revoke active sessions.
- Do not place session identifiers in URLs.
- Do not store sensitive authentication tokens in browser storage unless the threat model explicitly accepts the risk.
- Restrict cookie domain and path as narrowly as practical.

### API tokens

- Use short-lived access tokens.
- Protect refresh tokens more strongly.
- Rotate refresh tokens where appropriate.
- Detect refresh-token reuse.
- Validate issuer, audience, signature, expiration, and allowed algorithms.
- Never accept an algorithm based solely on untrusted token input.
- Do not include unnecessary sensitive information in token payloads.
- Revoke tokens after compromise or critical account changes.

Token possession does not replace authorization.

---

## 9. Authorization

Authorization must be enforced server-side for every protected action and object.

Check:

1. Is the requester authenticated?
2. Does the role permit this action?
3. May this requester access this specific resource?
4. Is the operation allowed in the resource’s current state?
5. Is extra verification required for this sensitive action?

Never trust:

- Hidden form fields
- Disabled buttons
- Client-side route guards
- Client-provided roles
- Client-provided prices
- Ownership IDs supplied by the browser

Test horizontal and vertical access controls:

- Horizontal: one user accesses another user’s resource.
- Vertical: an ordinary user performs an administrator action.

Maintain an authorization matrix:

```markdown
| Resource  | Action   | Guest | User | Manager | Admin | Additional rule       |
| --------- | -------- | ----: | ---: | ------: | ----: | --------------------- |
| Profile   | Read own |    No |  Yes |     Yes |   Yes | Owner only            |
| Profile   | Read any |    No |   No | Limited |   Yes | Logged                |
| Order     | Cancel   |    No |  Yes |     Yes |   Yes | Owner and valid state |
| Audit log | Export   |    No |   No |      No |   Yes | MFA required          |
```

Use unpredictable identifiers only as defense in depth. An unguessable identifier is not authorization.

---

## 10. Input Validation

Validate input at the server boundary using allowlists.

Validate:

- Type
- Required presence
- Length
- Numeric range
- Character set
- Format
- Enum membership
- Array size
- Object depth
- File size
- Business constraints

Reject unexpected fields when appropriate.

Normalize input carefully and validate the normalized representation. Avoid inconsistent interpretation between proxies, application servers, frameworks, and databases.

Use request body, header, and URL-size limits to prevent resource exhaustion.

---

## 11. Injection Prevention

### Database queries

- Use parameterized queries or safe query builders.
- Never concatenate untrusted input into queries.
- Allowlist client-selectable sort columns and operators.
- Use a minimally privileged database account.
- Apply database constraints for critical integrity rules.

### Operating-system commands

Avoid executing shell commands with user-controlled content.

If unavoidable:

- Use a safe process API without a shell.
- Pass arguments separately.
- Allowlist permitted operations.
- Use an isolated, unprivileged environment.
- Enforce time and resource limits.

### Templates and expressions

Do not evaluate user-controlled content as:

- Source code
- Templates
- Regular expressions without limits
- Query expressions
- Serialization instructions
- Dynamic imports

---

## 12. Cross-Site Scripting Prevention

Prevent reflected, stored, and DOM-based XSS.

- Use frameworks with automatic context-aware escaping.
- Encode output for its exact destination: HTML, attribute, URL, CSS, or JavaScript.
- Avoid rendering raw HTML.
- Sanitize rich text with a maintained allowlist sanitizer.
- Avoid unsafe DOM APIs.
- Never place untrusted input into executable script contexts.
- Validate URLs and permitted schemes.
- Use Content Security Policy as defense in depth.

Do not rely on input filtering alone. Safe output handling is essential.

---

## 13. Content Security Policy

Start restrictive and expand only for documented requirements.

Example baseline:

```text
Content-Security-Policy:
  default-src 'self';
  base-uri 'self';
  object-src 'none';
  frame-ancestors 'none';
  form-action 'self';
  script-src 'self' 'nonce-<per-request-value>';
  style-src 'self';
  img-src 'self' data:;
  connect-src 'self';
  upgrade-insecure-requests
```

Adapt this to the application.

Guidelines:

- Prefer nonces or hashes over broad inline-script permission.
- Avoid unsafe evaluation.
- Avoid unrestricted wildcard sources.
- Report violations to a monitored endpoint.
- Test in report-only mode before enforcement when migrating an existing application.

CSP is not a replacement for output encoding.

---

## 14. Cross-Site Request Forgery

For cookie-authenticated state-changing requests:

- Use framework-provided CSRF protection.
- Use unpredictable CSRF tokens.
- Validate origin information where appropriate.
- Use restrictive `SameSite` cookies.
- Do not use GET for state-changing operations.
- Require reauthentication for highly sensitive changes.

An API is not automatically CSRF-safe merely because it uses JSON.

---

## 15. CORS

CORS is a browser access policy, not authentication.

- Allow only required origins.
- Avoid reflecting arbitrary origins.
- Do not combine wildcard origins with credentials.
- Restrict methods and headers.
- Cache preflight responses carefully.
- Keep development origins out of production.
- Test null, malformed, and unexpected origins.

Server-side authorization remains mandatory.

---

## 16. Security Headers

Review and configure:

```text
Strict-Transport-Security
Content-Security-Policy
X-Content-Type-Options: nosniff
Referrer-Policy
Permissions-Policy
Cross-Origin-Opener-Policy
Cross-Origin-Resource-Policy
Cross-Origin-Embedder-Policy, when compatible
```

Prevent framing through CSP `frame-ancestors`.

Avoid treating obsolete headers as substitutes for modern controls.

Create `docs/security/security-headers.md` describing each header, its value, owner, compatibility considerations, and test coverage.

---

## 17. TLS and Transport Security

- Use HTTPS for all application traffic.
- Redirect HTTP to HTTPS.
- Enable HSTS after validating HTTPS coverage.
- Use supported TLS versions and cipher configurations.
- Automate certificate issuance and renewal.
- Protect traffic between internal services when required by the threat model.
- Verify certificates for outbound connections.
- Never disable certificate verification to “fix” connectivity.

Do not include credentials or sensitive data in URLs because URLs may enter logs, history, and referrer data.

---

## 18. File Uploads and Downloads

For uploads:

- Allowlist permitted extensions and verified content types.
- Verify file signatures when practical.
- Generate server-side filenames.
- Enforce size and quantity limits.
- Store files outside executable application directories.
- Prevent path traversal.
- Scan risky files.
- Remove unnecessary metadata where appropriate.
- Process files in isolated, restricted workers.
- Serve uploads from a separate origin when practical.
- Require authorization for private files.

Do not trust:

- Filename
- Extension
- Browser-provided content type
- Image dimensions
- Archive structure

Protect archive extraction against traversal, decompression bombs, and excessive file counts.

For downloads:

- Enforce object-level authorization.
- Set safe content types and disposition.
- Prevent user-controlled response headers.
- Avoid exposing internal storage paths.

---

## 19. Server-Side Request Forgery

Any feature that fetches a user-provided URL requires special protection.

- Prefer predefined destinations.
- Allowlist schemes, hosts, and ports.
- Resolve and validate addresses safely.
- Block loopback, private, link-local, metadata, and internal ranges unless explicitly required.
- Revalidate redirects.
- Limit redirect count.
- Apply strict timeouts and response-size limits.
- Restrict outbound network access at the infrastructure layer.
- Do not forward credentials automatically.
- Parse URLs using maintained libraries.

Application validation alone is insufficient. Use network-level egress controls.

---

## 20. Redirects and URL Handling

For redirects:

- Prefer application-owned relative paths.
- Use an allowlist for external destinations.
- Do not trust a `returnUrl` without validation.
- Reject dangerous and ambiguous schemes.
- Normalize before validating.
- Test encoded and alternate URL representations.

Prevent open redirects from becoming phishing or token-leakage mechanisms.

---

## 21. API Security

Every API endpoint must define:

- Authentication
- Authorization
- Input schema
- Output schema
- Request-size limit
- Rate limit
- Error behavior
- Idempotency behavior
- Data sensitivity
- Audit requirements
- Owner

Protect against:

- Object-level authorization failures
- Function-level authorization failures
- Excessive data exposure
- Mass assignment
- Resource exhaustion
- Inventory drift
- Unsafe third-party API consumption
- Automated business abuse

Use explicit response models rather than serializing database entities directly.

For updates, allowlist writable fields. Never permit clients to set privileged fields such as:

```text
role
isAdmin
ownerId
accountBalance
verificationStatus
internalStatus
createdBy
```

---

## 22. Business-Logic Security

Automated scanners cannot fully detect business abuse.

Test whether users can:

- Skip required workflow steps
- Repeat one-time actions
- Reuse expired discounts
- Purchase with modified prices
- Refund more than was paid
- Transfer negative amounts
- Exceed account limits through concurrency
- Reserve the same resource twice
- Abuse invitation or referral systems
- Enumerate private records
- Bypass approval workflows
- Exploit race conditions
- Replay requests
- Trigger side effects without completing payment

Enforce critical rules atomically and server-side.

Use idempotency keys and database constraints where duplicate execution would cause harm.

---

## 23. Rate Limiting and Abuse Prevention

Apply controls based on operation risk, not only IP address.

Protect:

- Login
- Registration
- Password reset
- MFA verification
- Search
- Expensive reports
- File processing
- Invitations
- Messaging
- Payments
- Promotional claims
- Data exports

Possible signals include:

- Account
- Session
- IP range
- Device
- API credential
- Organization
- Resource
- Behavioral patterns

Return consistent responses without leaking account existence.

Monitor distributed low-rate abuse, not only obvious bursts.

---

## 24. Secrets Management

Secrets include:

- Passwords
- API keys
- Private keys
- Database credentials
- Signing keys
- Webhook secrets
- Encryption keys
- Session secrets

Requirements:

- Store secrets in an approved secret-management system.
- Never commit secrets to source control.
- Separate secrets by environment.
- Scope secrets to minimum privileges.
- Rotate secrets.
- Audit access.
- Prevent secrets from entering logs and build artifacts.
- Scan repositories and CI output for accidental exposure.
- Treat exposed secrets as compromised even if later removed from Git.

Provide safe placeholders in `.env.example`.

---

## 25. Cryptography

- Use maintained cryptographic libraries.
- Never design custom cryptographic algorithms.
- Use authenticated encryption for sensitive stored data.
- Use cryptographically secure randomness for tokens.
- Separate encryption keys from encrypted data.
- Version encrypted formats and keys.
- Plan key rotation.
- Restrict key access.
- Never use password-hashing algorithms for general encryption or fast hashes for password storage.
- Compare security-sensitive values using constant-time functions where relevant.

Document what is encrypted, why, where keys reside, and how rotation works.

---

## 26. Dependencies and Supply Chain

For every dependency:

- Confirm it is necessary.
- Review maintenance and provenance.
- Pin or lock versions appropriately.
- Scan for known vulnerabilities.
- Review licenses.
- Minimize transitive dependency exposure.
- Remove unused packages.
- Monitor security advisories.
- Update safely and regularly.

Protect the build pipeline:

- Restrict CI permissions.
- Protect release credentials.
- Pin third-party automation components.
- Require review for workflow changes.
- Separate untrusted pull-request jobs from secret-bearing jobs.
- Produce verifiable build artifacts where practical.
- Generate a software bill of materials for higher-risk systems.

A vulnerable development or CI environment can compromise production without exploiting the application.

---

## 27. Infrastructure Security

- Separate development, testing, staging, and production.
- Deny public access unless required.
- Use network segmentation.
- Use least-privilege service identities.
- Disable default accounts.
- Patch operating systems and runtimes.
- Encrypt supported storage.
- Protect backups.
- Restrict administrative interfaces.
- Centralize audit logs.
- Detect configuration drift.
- Review cloud storage and database exposure.
- Apply resource limits.
- Use infrastructure-as-code and review changes.

Do not rely on secret URLs or uncommon ports for protection.

---

## 28. Database Security

- Use separate credentials per application or service.
- Grant only required operations.
- Parameterize queries.
- Encrypt connections.
- Restrict network access.
- Audit privileged operations.
- Mask sensitive data outside production.
- Do not copy unrestricted production datasets into development.
- Apply integrity constraints.
- Back up and test restoration.
- Define retention and deletion behavior.

Applications should not normally connect as database administrators.

---

## 29. Logging, Monitoring, and Audit

Security logs should capture:

- Successful and failed authentication
- MFA changes
- Password and recovery changes
- Authorization failures
- Privileged actions
- Role changes
- Sensitive exports
- Secret and key operations
- Security configuration changes
- Suspicious rate-limit events
- Webhook verification failures
- Administrative access

Each relevant event should include:

- Timestamp
- Event type
- Actor
- Target
- Outcome
- Correlation identifier
- Safe contextual metadata

Never log:

- Passwords
- Full tokens
- Session cookies
- Private keys
- Full payment data
- Unnecessary personal information
- Sensitive request bodies

Protect logs against unauthorized modification and access. Define retention and alerting.

An audit trail should identify actions without becoming a second sensitive-data leak.

---

## 30. Error Handling

Production errors must not expose:

- Stack traces
- SQL
- Filesystem paths
- Environment variables
- Framework versions
- Internal hostnames
- Secrets
- Detailed dependency failures

Return a safe error envelope:

```json
{
  "error": {
    "code": "ACCESS_DENIED",
    "message": "You are not permitted to perform this operation.",
    "requestId": "correlation-id"
  }
}
```

Log internal diagnostics securely using the same correlation identifier.

Fail closed for security decisions. If authorization cannot be evaluated safely, deny the request.

---

## 31. Webhooks

For incoming webhooks:

- Verify a signature using the raw request body.
- Verify timestamp freshness.
- Prevent replay.
- Use a dedicated secret.
- Apply request-size limits.
- Return quickly and process asynchronously where appropriate.
- Treat payloads as untrusted.
- Make processing idempotent.
- Monitor signature failures.

For outgoing webhooks:

- Use HTTPS.
- Sign payloads.
- Document retries.
- Bound retries.
- Protect against SSRF when customers configure destinations.
- Avoid including unnecessary sensitive data.
- Provide event identifiers for deduplication.

---

## 32. Administrative Interfaces

Administrative capabilities require stronger protection:

- MFA
- Separate authorization
- Shorter session lifetime
- Reauthentication for critical actions
- Detailed audit logging
- Restricted network access where practical
- Alerts for unusual activity
- Separation of duties for high-impact operations
- Confirmation for destructive actions

Do not hide ordinary endpoints in the UI and assume they are administrator-only. Enforce permissions server-side.

---

## 33. Privacy

- Collect only required information.
- State why data is collected.
- Restrict internal access.
- Define retention periods.
- Support correction and deletion where applicable.
- Review third-party processors.
- Avoid sensitive data in analytics.
- Obtain required consent.
- Protect exports.
- Verify identity before fulfilling privacy requests.

Security preserves data; privacy governs whether the data should exist and how it may be used.

---

## 34. Security Testing Strategy

Use multiple testing layers.

### Automated on every change

- Formatting and linting
- Type checking
- Unit tests
- Security-focused unit tests
- Dependency scanning
- Secret scanning
- Static security analysis
- Infrastructure configuration scanning
- API contract validation

### Automated regularly

- Dynamic testing in an authorized environment
- Container and image scanning
- TLS and security-header validation
- External exposure review
- Dependency re-evaluation
- Authenticated API security tests

### Manual testing

Required for:

- Authorization
- Business logic
- Workflow bypasses
- Race conditions
- Account recovery
- Administrative functions
- Complex file processing
- Third-party trust boundaries
- Sensitive releases

### Independent assessment

Use periodic independent review for high-risk systems or major architecture changes.

Automated scanners support security testing; they do not replace human reasoning.

---

## 35. Security Test Checklist

### Discovery and inventory

Verify:

- All hosts and endpoints are known.
- Old versions are retired.
- Debug routes are disabled.
- Administrative interfaces are restricted.
- API documentation does not expose unintended operations.
- Test systems are not using production data or credentials.

### Authentication

Test:

- Invalid credentials
- Account enumeration
- Rate limiting
- Session fixation
- Session rotation
- Logout invalidation
- Expiration
- Password reset
- Recovery-token reuse
- MFA enrollment, removal, recovery, and bypass resistance
- Existing sessions after password changes
- Disabled-account behavior

### Authorization

For every endpoint and action, test:

- Unauthenticated access
- Wrong role
- Another user’s identifier
- Another organization’s identifier
- Direct endpoint access without UI
- Writable privileged fields
- Archived or disabled resources
- Bulk operations
- Export operations

### Input handling

Test:

- Missing fields
- Extra fields
- Wrong types
- Empty values
- Very large values
- Boundary values
- Unexpected encodings
- Duplicate parameters
- Malformed JSON
- Deeply nested objects
- Oversized arrays
- Special characters
- Unsafe filenames and paths

### Browser security

Verify:

- Output encoding
- Rich-text sanitization
- CSP behavior
- CSRF protection
- Cookie flags
- CORS policy
- Frame protection
- Referrer policy
- No sensitive browser storage
- No sensitive URL parameters
- No secrets in source maps or frontend bundles

### API security

Verify:

- Object-level authorization
- Function-level authorization
- Response-field allowlists
- Update-field allowlists
- Pagination limits
- Rate limits
- Request-size limits
- Idempotency
- Replay resistance
- Safe error responses
- Retired API versions are unavailable

### File handling

Test:

- Invalid file types
- Mismatched extension and content
- Oversized files
- Excessive file counts
- Dangerous archive structures
- Unauthorized downloads
- Metadata leakage
- Executable serving
- Storage path exposure

### Business logic

Test:

- Step skipping
- Reordered operations
- Repeated operations
- Concurrent operations
- Modified prices and totals
- Negative and extreme quantities
- Discount reuse
- Limit bypass
- Refund and cancellation constraints
- Approval bypass
- Replay of financial actions

### Infrastructure

Verify:

- No unnecessary public services
- Storage is private by default
- Database access is restricted
- Default credentials are absent
- Debug mode is disabled
- TLS is correctly configured
- Backups are protected
- CI secrets are isolated
- Production permissions follow least privilege

### Logging and monitoring

Verify:

- Important events are logged.
- Secrets are redacted.
- Correlation identifiers work.
- Alerts reach an owner.
- Failed-login abuse is detectable.
- Privileged changes are auditable.
- Clock synchronization is reliable.

---

## 36. What Reviewers Should Look For in Code

Search for indicators such as:

```text
Raw SQL construction
Dynamic command execution
Unsafe HTML rendering
Disabled TLS verification
Wildcard CORS
Hard-coded secrets
Debug mode
Unprotected routes
Client-controlled role or ownership values
Deserialization of untrusted objects
Unbounded queries
Unbounded file reads
Missing request timeouts
Infinite or unsafe retries
Sensitive logging
Weak random-token generation
Authorization only in the frontend
Path construction using user input
Remote URL fetching
Mass assignment
Custom cryptography
Error swallowing
```

Also inspect:

- Every trust boundary
- Every state-changing operation
- Every privileged workflow
- Every external integration
- Every place data changes ownership
- Every operation that transfers money or value
- Every concurrency-sensitive invariant

A suspicious pattern is not automatically a vulnerability. Trace the full data flow and confirm existing controls.

---

## 37. Finding Validation

Before reporting a vulnerability:

1. Confirm it is within scope.
2. Reproduce it safely.
3. Verify that existing controls do not prevent impact.
4. Identify affected roles and resources.
5. Determine required prerequisites.
6. Estimate realistic impact.
7. Preserve minimal evidence.
8. Avoid accessing unnecessary data.
9. Recommend root-cause remediation.
10. Add a regression test after fixing.

Do not report scanner output as confirmed fact without validation.

---

## 38. Security Finding Template

```markdown
## Finding: Clear vulnerability title

- **Identifier:** SEC-YYYY-NNN
- **Status:** Open | Accepted | Fixed | Verified
- **Severity:** Critical | High | Medium | Low | Informational
- **Affected component:** Component or endpoint
- **Owner:** Team
- **Discovered:** YYYY-MM-DD
- **Target remediation:** YYYY-MM-DD

### Summary

A concise description of the weakness.

### Preconditions

Required account, role, configuration, or system state.

### Safe reproduction

Minimal authorized steps using synthetic data.

### Expected behavior

What the application should do.

### Actual behavior

What happened.

### Impact

Realistic effect on confidentiality, integrity, availability, or privacy.

### Evidence

Redacted requests, responses, logs, or screenshots.

### Root cause

The design or implementation defect—not merely the symptom.

### Remediation

Specific corrective action and defense-in-depth improvements.

### Regression test

Automated or manual test preventing recurrence.

### Verification

How the fix was independently confirmed.
```

Redact credentials, tokens, personal data, and infrastructure secrets.

---

## 39. Severity Guidance

### Critical

Likely widespread compromise with limited prerequisites, such as major authentication bypass, broad sensitive-data exposure, or remote control of critical systems.

### High

Serious compromise requiring some conditions, such as privilege escalation, significant cross-account access, or high-impact stored script execution.

### Medium

Meaningful but constrained impact requiring additional privileges, user interaction, or limited scope.

### Low

Limited impact or strong mitigating conditions.

### Informational

Hardening opportunity without a demonstrated security impact.

Severity should consider:

- Exploitability
- Required access
- User interaction
- Scope
- Data sensitivity
- Business impact
- Existing mitigations
- Detectability
- Recovery difficulty

Do not inflate severity to accelerate attention.

---

## 40. Remediation Priorities

Fix root causes in this order:

1. Remove or isolate the vulnerable behavior.
2. Enforce missing authorization or validation.
3. Correct unsafe architecture or data flow.
4. Add defense-in-depth controls.
5. Add regression tests.
6. Improve detection and alerting.
7. Update documentation and threat models.

Avoid superficial fixes such as blocking one known payload while leaving the underlying unsafe behavior intact.

---

## 41. Release Security Gates

A release must not proceed when it contains:

- Known critical vulnerabilities
- Unresolved high-risk authorization failures
- Exposed production secrets
- Broken authentication
- Unsafe database migrations
- Unreviewed public administrative access
- Disabled transport security
- Missing controls for sensitive new data
- Unverified critical dependency vulnerabilities

Before release verify:

- Threat model updated
- Authorization reviewed
- Security tests pass
- Dependencies scanned
- Secrets scanned
- Logging and alerts ready
- Rollback strategy defined
- Security documentation updated
- High-risk functionality manually tested

Exceptions require documented ownership, compensating controls, expiration, and formal risk acceptance.

---

## 42. Incident Response

Prepare before an incident.

### Phases

1. Detect
2. Triage
3. Contain
4. Preserve evidence
5. Eradicate root cause
6. Recover
7. Monitor
8. Communicate
9. Review
10. Improve

During an incident:

- Assign an incident lead.
- Preserve relevant logs and timestamps.
- Rotate compromised credentials.
- Revoke affected sessions.
- Limit blast radius.
- Avoid destroying evidence.
- Record decisions.
- Follow legal and notification requirements.
- Communicate verified facts only.

Afterward:

- Document timeline and impact.
- Identify contributing controls and failures.
- Add tests and detections.
- Update threat models and runbooks.
- Track corrective actions to completion.

Use a blameless review focused on system improvement.

---

## 43. Backups and Recovery

- Encrypt backups.
- Restrict backup access.
- Separate backup credentials from application credentials.
- Define retention.
- Protect against deletion and ransomware.
- Test restoration regularly.
- Measure recovery time and recovery point objectives.
- Verify restored data integrity.
- Avoid retaining deleted sensitive data indefinitely.

An untested backup is not a recovery plan.

---

## 44. Secure Development Workflow

For every change:

1. Identify assets and trust boundaries.
2. Determine authentication requirements.
3. Define authorization rules.
4. Define accepted input and output.
5. Consider abuse and failure cases.
6. Identify sensitive data.
7. Select established security controls.
8. Implement least privilege.
9. Add security-focused tests.
10. Review dependencies.
11. Review logs for secret exposure.
12. Update security documentation.
13. Verify monitoring.
14. Plan rollback.
15. Confirm behavior after deployment.

---

## 45. Definition of Secure Enough to Release

A feature is ready only when applicable requirements are satisfied:

- Threats and abuse cases were considered.
- Authentication is implemented correctly.
- Authorization is enforced server-side.
- Object ownership is verified.
- Input is validated.
- Output is encoded safely.
- Sensitive data is minimized.
- Secrets are managed properly.
- External calls have timeouts.
- Retried operations are safe.
- Security events are logged.
- Errors do not leak internals.
- Dependencies were reviewed.
- Automated checks pass.
- Manual business-logic testing is complete.
- Documentation is updated.
- Incident detection and rollback are possible.

Passing a scanner alone does not make a feature secure.

---

## 46. Instructions for an AI Security Reviewer

When reviewing a system:

1. Operate only within explicit authorization and scope.
2. Begin with architecture, assets, actors, and trust boundaries.
3. Build an inventory of routes, roles, data flows, and integrations.
4. Prioritize authentication, authorization, sensitive data, and business logic.
5. Trace untrusted input from source to sink.
6. Review both successful and failure paths.
7. Check every endpoint independently; never assume shared middleware applies.
8. Confirm findings before labeling them vulnerabilities.
9. Prefer safe tests using synthetic data.
10. Never expose, retain, or reproduce unnecessary secrets.
11. Explain the root cause and realistic impact.
12. Recommend framework-supported, maintainable fixes.
13. Add or propose regression tests.
14. Separate confirmed findings from hypotheses.
15. State uncertainty and missing evidence.
16. Stop if testing could damage data, availability, or unrelated systems.
17. Do not weaken security merely to make tests pass.
18. Never claim the system is completely secure.

Output results as:

```markdown
## Security Review Summary

### Scope

### Architecture and trust boundaries

### Confirmed protections

### Confirmed findings

### Unconfirmed concerns requiring investigation

### Missing security controls

### Recommended tests

### Release blockers

### Remediation priorities

### Residual risks
```

---

## 47. Final Rules

1. Never trust client-controlled data.
2. Authenticate identities securely.
3. Authorize every protected action and object.
4. Deny access by default.
5. Apply least privilege everywhere.
6. Parameterize queries.
7. Encode output for its destination.
8. Protect state-changing requests.
9. Keep secrets out of code and logs.
10. Use HTTPS and secure cookies.
11. Restrict CORS intentionally.
12. Set modern security headers.
13. Limit requests, files, queries, and retries.
14. Give every network call a timeout.
15. Make sensitive operations idempotent where required.
16. Treat uploads and remote URLs as high risk.
17. Enforce business rules atomically.
18. Patch and minimize dependencies.
19. Log important security events safely.
20. Test authorization and business logic manually.
21. Confirm automated findings before reporting them.
22. Fix root causes and add regression tests.
23. Prepare incident response and recovery before failure.
24. Document accepted risk.
25. Assume one control will eventually fail and design the next layer.

```

```
