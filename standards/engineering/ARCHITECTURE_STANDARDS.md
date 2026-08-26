# The 24th Group — Architecture Standards

**Status:** Group Engineering Standard  
**Scope:** All software products, internal systems, APIs, services, automation and digital platforms built or maintained by The 24th Group.

## 1. Purpose

This document defines the architectural principles that apply across The 24th Group.

The objective is to ensure systems are:

- maintainable
- modular
- secure
- testable
- observable
- appropriately scalable
- portable where portability provides material value
- capable of evolving without unnecessary rewrites

Individual products may use different technologies, providers and deployment models, but should inherit these architectural principles unless an approved exception exists.

## 2. Standards Language

The following terms are used throughout this document:

- **MUST** — mandatory unless an approved exception exists.
- **SHOULD** — expected default, but may be changed where there is a documented reason.
- **MAY** — optional and dependent on product requirements.

## 3. Core Architecture Principles

### 3.1 Separation of Concerns

Applications SHOULD separate responsibilities into clear architectural boundaries.

A typical structure may include:

1. Presentation Layer
2. Application Layer
3. Domain Layer
4. Infrastructure Layer

Exact implementation may vary by product.

Business rules SHOULD remain separate from UI, persistence and external integration concerns.

### 3.2 Dependency Inversion

Core business logic SHOULD depend on abstractions rather than concrete infrastructure implementations.

Infrastructure-specific SDKs, APIs and provider logic SHOULD remain at application boundaries.

Where practical:

```text
Core Business Logic
        ↓
Interface / Port
        ↓
Infrastructure Adapter
```

This is especially important for:

- databases
- authentication providers
- file storage
- queues
- payment services
- email providers
- third-party APIs
- AI providers
- infrastructure platforms

### 3.3 Ports and Adapters

Products SHOULD use ports-and-adapters or equivalent architectural patterns where external dependencies would otherwise become tightly coupled to application logic.

Adapters MAY differ between:

- local development
- testing
- production
- cloud providers
- regional deployments

Products are not required to abstract every dependency unnecessarily.

Provider abstraction SHOULD be introduced where it improves:

- testability
- maintainability
- vendor independence
- runtime portability
- resilience
- future migration capability

## 4. Product Architecture Profiles

Each significant product SHOULD maintain a product-specific implementation profile or baseline.

This should define:

- technology stack
- hosting/runtime platform
- databases
- infrastructure providers
- authentication model
- major integrations
- background processing architecture
- storage systems
- external dependencies
- product-specific architectural constraints

Group standards define principles.

Product profiles define implementation choices.

## 5. Application Boundaries

Application modules SHOULD have clearly defined responsibilities.

Modules SHOULD expose stable interfaces rather than allowing unrelated parts of the system to directly manipulate internal implementation details.

Shared logic SHOULD be extracted where meaningful, but premature abstraction SHOULD be avoided.

Architecture SHOULD prioritise understandable boundaries over unnecessary complexity.

## 6. Data Architecture

Data models SHOULD reflect the actual business domain.

Query-critical data SHOULD normally use structured schemas rather than uncontrolled schemaless storage.

Schemaless or semi-structured storage MAY be used for:

- metadata
- integration payloads
- flexible attributes
- audit information
- event payloads

Data ownership SHOULD be clear.

Where multiple products share data, one authoritative source SHOULD be defined.

## 7. Multi-Tenancy

Products supporting multiple organisations, workspaces or customers MUST implement explicit tenant isolation.

Tenant context SHOULD be enforced at:

- application boundaries
- authorization checks
- data access layers

Where appropriate, database-level isolation controls SHOULD also be used.

Cross-tenant access MUST NOT occur without explicit privileged authorization.

## 8. Authentication and Authorization

Authentication and authorization MUST be treated as separate concerns.

Products MUST define:

- supported authentication methods
- role models
- permission models
- privileged operations
- account recovery behaviour
- session or token management

Authorization SHOULD be capability or policy driven rather than relying only on scattered role checks.

Example:

```text
Role
  ↓
Permissions / Capabilities
  ↓
Policy Evaluation
  ↓
Action
```

## 9. Privileged and High-Risk Operations

Sensitive actions MUST receive stronger controls.

Examples include:

- financial adjustments
- refunds
- payouts
- ownership changes
- destructive operations
- permission escalation
- account impersonation
- security configuration
- deletion of sensitive records

Controls MAY include:

- re-authentication
- elevated roles
- dual approval
- reason capture
- confirmation steps
- additional audit logging

The exact control level should match the risk.

## 10. Auditability

Sensitive and state-changing operations MUST be auditable where the product requires accountability or traceability.

Audit records SHOULD capture as applicable:

- actor
- tenant or workspace
- action
- resource
- resource identifier
- before state
- after state
- request identifier
- timestamp
- outcome
- reason or note

Audit logs SHOULD be append-only wherever practical.

## 11. External Integrations

External systems SHOULD be treated as unreliable dependencies.

Integrations SHOULD consider:

- authentication failure
- rate limiting
- network failure
- service outage
- schema changes
- duplicate events
- delayed events
- malformed responses

Writes triggered through integrations SHOULD be idempotent where duplicate execution is possible.

## 12. Background Processing

Long-running, asynchronous or retryable workloads SHOULD be moved outside synchronous request paths where appropriate.

Examples include:

- bulk processing
- document processing
- email delivery
- large imports
- exports
- AI processing
- webhook retries
- scheduled jobs

Products MAY use queues, workers, schedulers or managed equivalents.

The architectural requirement is reliable execution, not a specific technology.

## 13. Resilience

Systems SHOULD fail predictably.

Where appropriate, applications SHOULD implement:

- retries
- exponential backoff
- timeouts
- idempotency
- graceful degradation
- circuit-breaker behaviour
- dead-letter handling
- recovery procedures

Retry logic MUST avoid uncontrolled retry loops.

## 14. Health and Operational Readiness

Production systems SHOULD expose mechanisms for determining operational health.

Health reporting MAY include:

- application health
- database connectivity
- cache
- queue
- storage
- integrations
- background workers

Where useful, services SHOULD distinguish between:

- healthy
- degraded
- unhealthy

Liveness and readiness SHOULD be treated separately where the deployment platform supports them.

## 15. Observability

Production systems MUST provide sufficient observability to diagnose failures.

This SHOULD include:

- structured logs
- request or correlation identifiers
- error context
- latency information
- service/provider context
- actor and tenant context where appropriate

Sensitive information MUST NOT be exposed in logs.

Metrics and tracing SHOULD be introduced where operational complexity warrants them.

## 16. Security Architecture

Security MUST be considered during architecture design rather than added after implementation.

Products SHOULD apply:

- least privilege
- secure defaults
- secret isolation
- encryption where appropriate
- authorization at trusted boundaries
- input validation
- output encoding
- dependency management
- environment isolation

Secrets MUST NOT be embedded directly in application source code.

## 17. Privacy Architecture

Products handling personal or sensitive information SHOULD define:

- what data is collected
- why it is collected
- where it is stored
- who can access it
- how long it is retained
- how it is deleted
- whether it leaves the product boundary

Data minimisation SHOULD be preferred.

## 18. Environment Separation

Development, testing, staging and production SHOULD be logically separated.

Production credentials and production data MUST NOT be routinely used for local development.

Products SHOULD support reproducible development environments.

The mechanism may include:

- containers
- environment templates
- development scripts
- managed preview environments

## 19. Runtime Portability

Vendor independence is not an objective by itself.

Products SHOULD avoid unnecessary vendor lock-in where:

- migration risk is meaningful
- infrastructure costs may change significantly
- business continuity depends on portability
- testing requires alternative implementations
- multiple deployment targets are expected

Small or low-risk products MAY use direct provider integrations where abstraction would add more complexity than value.

## 20. Testing Architecture

Systems SHOULD be architected to allow automated testing without requiring production infrastructure.

Core business logic SHOULD be testable independently.

External dependencies SHOULD be replaceable with:

- mocks
- test adapters
- emulators
- sandbox environments

Critical system journeys SHOULD have end-to-end coverage.

## 21. Scalability

Products SHOULD be designed for their realistic expected scale rather than hypothetical extreme scale.

Architecture SHOULD avoid obvious scaling bottlenecks while also avoiding premature distributed-system complexity.

Scaling decisions SHOULD be based on:

- measured usage
- forecast demand
- service constraints
- cost
- reliability requirements

## 22. Architecture Decisions

Material architecture decisions SHOULD be documented using Architecture Decision Records or equivalent documentation.

Examples include:

- database changes
- authentication provider changes
- major infrastructure migration
- new architectural patterns
- event-driven architecture adoption
- significant vendor dependencies

A decision record SHOULD capture:

- context
- decision
- rationale
- consequences
- alternatives considered

## 23. Exceptions

Deviation from these standards is permitted where there is a valid product or engineering reason.

Material deviations SHOULD document:

- the relevant standard
- the reason for deviation
- impact
- risk
- mitigation
- approving owner

## 24. Architecture Definition of Done

A significant architectural change is complete when:

1. boundaries and responsibilities are clear
2. security implications have been considered
3. failure behaviour has been considered
4. observability requirements are addressed
5. testing approach is defined
6. infrastructure dependencies are appropriately isolated
7. documentation is updated
8. material decisions are recorded
9. operational impact is understood

## 25. Governance

These standards are owned at Group level.

Product teams inherit them by default.

Product-specific standards may extend these requirements but should not silently weaken mandatory Group controls.

Architecture should remain pragmatic.

The purpose of these standards is to improve engineering quality and long-term maintainability, not to introduce unnecessary bureaucracy.
