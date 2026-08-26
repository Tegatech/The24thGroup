# The 24th Group — Technical Standards

**Status:** Group Engineering Standard  
**Scope:** Software engineering, APIs, databases, security, testing, source control, deployment, observability and technical documentation across The 24th Group.

## 1. Purpose

This document establishes the default technical engineering standards used across The 24th Group.

Individual products may use different languages, frameworks and infrastructure.

These standards define the expected level of engineering quality rather than prescribing one universal technology stack.

## 2. Standards Language

- **MUST** — mandatory unless an approved exception exists.
- **SHOULD** — default expectation.
- **MAY** — optional depending on product requirements.

## 3. API Standards

### 3.1 API Style

Public and internal APIs SHOULD use clearly documented conventions.

REST is the default where no other protocol provides a material advantage.

GraphQL, RPC, event-driven interfaces or other patterns MAY be used where justified.

### 3.2 Versioning

Externally consumed APIs MUST have an explicit versioning strategy.

For REST APIs, path-based major versioning is preferred:

```text
/api/v1/...
```

Breaking changes MUST NOT silently alter an existing public contract.

### 3.3 Naming

REST resources SHOULD use:

- plural nouns
- predictable URL patterns
- consistent casing

A product MUST choose and consistently apply a request/response field naming convention.

### 3.4 API Documentation

Public APIs MUST be documented.

OpenAPI SHOULD be used for REST APIs where practical.

Documentation SHOULD include:

- endpoints
- authentication
- parameters
- payloads
- responses
- errors
- rate limits
- examples

### 3.5 Success Responses

Products SHOULD use a consistent response format.

Example:

```json
{
  "request_id": "req_123",
  "data": {},
  "meta": {}
}
```

### 3.6 Error Responses

Errors SHOULD use a consistent machine-readable structure.

Example:

```json
{
  "request_id": "req_123",
  "error": {
    "code": "AUTH_001",
    "message": "Authentication failed",
    "details": []
  }
}
```

Internal stack traces MUST NOT be exposed to public clients.

### 3.7 HTTP Status Codes

REST APIs SHOULD use standard HTTP semantics.

Typical expectations:

| Status | Usage |
|---|---|
| `200` | successful read/update |
| `201` | resource created |
| `202` | asynchronous processing accepted |
| `204` | successful operation with no response body |
| `400` | invalid request |
| `401` | unauthenticated |
| `403` | unauthorized |
| `404` | resource not found |
| `409` | conflict |
| `422` | domain/business validation failure |
| `429` | rate limit exceeded |
| `500` | internal failure |
| `503` | temporary service unavailability |

### 3.8 Rate Limiting

Externally exposed APIs SHOULD implement rate limiting where abuse or excessive load is possible.

Rate-limit behaviour SHOULD be documented.

### 3.9 Idempotency

Operations that may be retried or duplicated SHOULD support idempotency where duplicate execution could cause harm.

This is especially important for:

- payments
- webhooks
- provisioning
- destructive actions
- asynchronous jobs
- external callbacks

## 4. Database Standards

### 4.1 Database Selection

Database technology MUST be selected according to product requirements.

Relational databases SHOULD be preferred where the domain is strongly relational or query consistency matters.

Other database models MAY be used where appropriate.

### 4.2 Naming

Database naming SHOULD remain consistent.

For relational databases, the preferred baseline is:

```text
tables       plural_snake_case
columns      snake_case
foreign keys <entity>_id
```

### 4.3 Identifiers

Products SHOULD use identifiers appropriate for distributed and secure systems.

UUIDs or equivalent opaque identifiers are preferred for externally exposed resources unless there is a reason to use another strategy.

Sequential internal identifiers MAY still be used where appropriate.

### 4.4 Audit Columns

Business-critical tables SHOULD capture creation and modification metadata.

Typical fields include:

```text
created_at
updated_at
created_by
updated_by
```

Where soft deletion is used:

```text
deleted_at
deleted_by
```

### 4.5 Soft Delete

Soft deletion SHOULD be used where:

- recovery may be required
- records have audit significance
- relationships would be damaged by immediate deletion

Hard deletion SHOULD be used where privacy, security or retention policies require permanent deletion.

### 4.6 Structured vs Schemaless Data

Schemaless fields SHOULD NOT replace proper modelling for query-critical business data.

JSON or equivalent structures MAY be used for:

- metadata
- external payloads
- optional attributes
- event information

### 4.7 Migrations

Schema changes MUST be repeatable and version-controlled.

Production migrations SHOULD consider:

- backward compatibility
- rollback/recovery
- large-table impact
- zero-downtime requirements

Manual production schema changes SHOULD be avoided.

## 5. Authentication Standards

Authentication mechanisms SHOULD be selected according to client type.

Typical patterns include:

- secure browser sessions
- bearer tokens
- API keys
- OAuth/OIDC
- service credentials

Authentication credentials MUST be handled securely.

Browser authentication tokens SHOULD NOT be stored in insecure client-side storage where secure cookie-based alternatives are appropriate.

## 6. Authorization Standards

Authorization MUST be enforced on trusted server-side boundaries.

Products SHOULD define:

- roles
- permissions
- capabilities
- ownership rules
- privileged actions

Capability-driven policies are preferred to scattered role-name checks.

Tenant or workspace boundaries MUST be enforced consistently.

## 7. API Key Standards

Products issuing API keys SHOULD:

- identify the product
- identify environment where useful
- generate sufficient entropy
- display full secrets only when necessary
- store only hashes where practical
- support revocation
- support rotation

Example pattern:

```text
<product>_<environment>_<secret>
```

Exact prefixes are product-specific.

## 8. Secret Management

Secrets MUST NOT be committed to source control.

Secrets SHOULD be stored using:

- managed secret stores
- environment configuration
- cloud secret management
- encrypted development storage

Production secret access SHOULD follow least privilege.

## 9. Encryption

Sensitive information SHOULD be encrypted in transit and at rest where risk warrants it.

Industry-standard cryptography MUST be used.

Custom cryptographic algorithms MUST NOT be invented.

Production encryption keys SHOULD be stored separately from encrypted application data.

## 10. Privacy and Sensitive Data

Logs MUST NOT contain:

- passwords
- raw authentication secrets
- payment card information
- unnecessary personal information

Sensitive data SHOULD be:

- minimised
- masked
- redacted
- encrypted
- retained only where needed

Retention requirements SHOULD be documented for sensitive data classes.

## 11. Input Validation

All external input MUST be considered untrusted.

Applications MUST validate relevant:

- request bodies
- query parameters
- uploaded files
- webhook payloads
- integration responses
- user-generated content

Server-side validation remains authoritative even where client validation exists.

## 12. File Upload Standards

Products accepting files SHOULD define:

- allowed types
- allowed MIME types
- maximum size
- scanning requirements where appropriate
- storage policy
- access controls
- retention/deletion policy

User-provided filenames MUST NOT be trusted as storage paths.

## 13. Testing Standards

### 13.1 Test Strategy

Products SHOULD use a combination of:

- unit tests
- integration tests
- end-to-end tests

The balance may vary by system.

### 13.2 Coverage

Changed business logic SHOULD normally target at least **80% automated coverage** where coverage measurement is meaningful.

Critical paths SHOULD receive stronger coverage.

Examples include:

- authentication
- authorization
- financial operations
- destructive actions
- billing
- idempotency
- tenant isolation

### 13.3 Test Data

Production personal data MUST NOT be copied into test environments without appropriate anonymisation and authorization.

Synthetic or anonymised datasets are preferred.

### 13.4 Test Isolation

Automated tests SHOULD avoid dependence on shared mutable environments.

Integration test environments SHOULD be reproducible.

### 13.5 CI Quality Gates

Products with CI MUST block merge or deployment when mandatory checks fail.

Checks SHOULD include as relevant:

- compilation
- type checking
- linting
- tests
- security checks
- build validation

## 14. Code Quality Standards

### 14.1 Type Safety

Strong typing SHOULD be enabled where the language supports it.

For TypeScript projects, strict mode SHOULD be the default.

Unchecked `any` SHOULD be avoided.

### 14.2 Code Readability

Code SHOULD optimise for clarity.

Developers SHOULD avoid:

- excessive nesting
- oversized functions
- hidden side effects
- unexplained magic values
- unnecessary abstractions

Small deviations are acceptable where they improve readability.

### 14.3 Complexity

Functions SHOULD remain focused.

A practical review threshold is approximately:

- 60 lines per function
- cyclomatic complexity around 10
- nesting depth around 3

These are review signals rather than absolute limits.

## 15. Formatting and Linting

Projects SHOULD use automated formatting and linting.

Formatting SHOULD be enforced consistently through tooling rather than individual developer preference.

CI SHOULD enforce agreed formatting where practical.

## 16. Dependency Management

Dependencies SHOULD be intentionally selected.

Before adding a dependency, consider:

- maintenance activity
- security history
- licensing
- bundle/runtime cost
- project maturity
- necessity

Dependencies SHOULD be updated regularly.

Known critical vulnerabilities MUST be addressed promptly.

## 17. Error Handling

Products SHOULD define clear error categories.

Example categories:

```text
CLIENT_
AUTH_
POLICY_
RESOURCE_
CONFLICT_
RATE_
INTERNAL_
UPSTREAM_
```

Error codes SHOULD be stable enough for logs, support and API consumers.

Errors SHOULD include a request or correlation identifier where appropriate.

## 18. Logging

Production logging SHOULD be structured.

Useful fields may include:

```text
timestamp
level
service
request_id
user_id
tenant_id
action
duration_ms
status
error
```

Logs SHOULD provide useful diagnostic context without exposing sensitive data.

## 19. Observability

Systems SHOULD provide sufficient observability for their operational risk.

This MAY include:

- logs
- metrics
- traces
- dashboards
- alerts
- uptime monitoring

Critical production services SHOULD have actionable failure monitoring.

## 20. Performance

Performance requirements SHOULD be based on expected product usage.

Teams SHOULD monitor meaningful indicators such as:

- response latency
- error rate
- database query performance
- queue latency
- resource utilisation
- external dependency latency

Obvious unbounded queries and uncontrolled processing MUST be avoided.

## 21. Caching

Caching MAY be used to improve:

- latency
- availability
- infrastructure cost
- external API usage

Cache behaviour SHOULD define:

- key strategy
- expiry
- invalidation
- fallback behaviour

Sensitive or user-specific data MUST NOT be cached without appropriate isolation.

## 22. Background Jobs

Background jobs SHOULD be:

- observable
- retry-safe
- idempotent where appropriate
- recoverable
- bounded in retry behaviour

Failed jobs SHOULD not disappear silently.

## 23. Source Control

Git is the default source-control system unless an approved alternative exists.

Repositories SHOULD use understandable branch and commit practices.

Conventional Commits are preferred:

```text
feat:
fix:
docs:
chore:
test:
refactor:
```

Suggested branch conventions:

```text
feature/<slug>
fix/<slug>
chore/<slug>
```

## 24. Pull Requests

Pull requests SHOULD remain focused and reviewable.

Approximately 400 changed lines is a useful target, excluding:

- generated files
- lockfiles
- migrations
- bulk content

Large PRs MAY be appropriate but SHOULD be intentionally structured.

Higher-risk changes SHOULD receive stronger review.

Examples:

- security
- authentication
- billing
- financial logic
- data migrations
- architecture changes

## 25. Code Review

Code review SHOULD verify more than whether the code runs.

Review SHOULD consider:

- correctness
- security
- architecture
- tests
- error handling
- maintainability
- product impact
- migration impact
- documentation

## 26. CI/CD

Build and deployment processes SHOULD be automated where practical.

Production deployment SHOULD be repeatable.

Products SHOULD avoid undocumented manual deployment steps.

Deployment processes SHOULD support rollback or recovery appropriate to product risk.

## 27. Environments

Products SHOULD maintain appropriate separation between:

```text
development
test
staging / preview
production
```

Not every small product requires every environment.

Production SHOULD remain isolated from routine development.

## 28. Configuration

Environment-specific behaviour SHOULD be configuration-driven.

Configuration SHOULD NOT require source-code changes between environments.

Required configuration SHOULD be documented through templates such as:

```text
.env.example
```

Secrets MUST NOT be included in those templates.

## 29. Documentation

Every maintained product SHOULD contain sufficient documentation for another competent engineer to understand and operate it.

Relevant documentation MAY include:

- README
- local setup
- architecture
- API documentation
- environment configuration
- deployment
- integrations
- operational runbooks
- data model
- testing
- known limitations

## 30. Architecture Decision Records

Architecture-impacting changes SHOULD create or update an ADR.

ADRs SHOULD be lightweight and useful rather than ceremonial.

## 31. Definition of Done

A software change SHOULD NOT be considered complete solely because it works in the developer's environment.

As applicable, completion should include:

1. requirements implemented
2. acceptance criteria met
3. type/build checks passing
4. tests passing
5. error handling implemented
6. permissions validated
7. security considered
8. observability added where required
9. documentation updated
10. deployment impact considered
11. migrations tested
12. no unresolved critical defects introduced

## 32. Exceptions

Technical standards MAY be overridden where a product has a legitimate requirement.

Material exceptions SHOULD document:

- standard being overridden
- reason
- impact
- risk
- mitigation
- approval

## 33. Guiding Principle

The Group technical standard should create consistency without forcing unnecessary uniformity.

Teams should prefer:

- simple over clever
- explicit over implicit
- maintainable over fashionable
- tested over assumed
- secure defaults over convenience
- measured scale over premature complexity
- documented decisions over tribal knowledge
