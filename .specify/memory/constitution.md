<!--
Sync Impact Report
- Version change: N/A → 1.0.0
- Modified principles: replaced template placeholders with microservices/Saga principles.
- Added sections: "Additional Constraints & Security" and "Development Workflow & Quality Gates".
- Removed sections: N/A (initial constitution).
- Templates requiring updates:
  - .specify/templates/plan-template.md ✅ updated
  - .specify/templates/spec-template.md ✅ updated
  - .specify/templates/tasks-template.md ✅ updated
- Follow-up TODOs:
  - Reference constitution principles in README.md.
-->

# Pluribus Constitution

## Core Principles

### I. Saga-Orchestrated Transactions (NON-NEGOTIABLE)
All cross-service business processes MUST be modeled as Sagas with a central orchestrator. Each saga step MUST define its forward action, compensation action, idempotency guarantees, timeout, and retry/backoff policy. Orchestrator state MUST be durable, traceable, and recoverable, supporting resume-on-restart. Message delivery MUST be at-least-once with deduplication keys; handlers MUST be idempotent. Dead-letter handling and manual intervention procedures MUST be documented.

Rationale: Sagas provide consistency across distributed boundaries without global transactions. Explicit compensation and idempotency make failures safe and operationally manageable.

### II. Microservice Boundaries & Contracts
Bounded contexts MUST be explicit. Service APIs are contracts: inputs, outputs, error codes, and invariants MUST be documented and versioned. Backward compatibility MUST be preserved for MINOR releases; breaking changes require MAJOR versions with migration guides. Schemas/events MUST evolve safely (e.g., additive changes, defaulting). Cross-service calls MUST be mediated via stable contracts (HTTP/JSON or events) with strict validation.

Rationale: Clear boundaries and versioned contracts enable independent delivery and reduce coupling.

### III. Code Quality & Consistency
Code MUST pass formatting and linting gates, adhere to agreed conventions, and be organized consistently (modules, naming, dependency hygiene). ADRs (Architecture Decision Records) SHOULD capture significant design choices. Unsafe or complex patterns MUST include justification and tests. Clojure code MUST prefer pure functions, explicit side-effects, and minimal shared mutable state.

Rationale: Consistent, clean code reduces defects and accelerates change.

### IV. Testing Discipline
Tests are mandatory at multiple layers:
- **Unit tests**: cover pure logic and edge cases.
- **Contract tests**: verify each service API/event contract (provider and consumer) against documented schemas.
- **Integration tests**: validate saga flows end-to-end, including compensation paths and failure modes.
- **Property-based tests**: apply where invariants are well-defined (e.g., idempotency, ordering, commutativity) using tools like `test.check`.

CI MUST run tests deterministically with reproducible data. Minimal coverage thresholds: 80% unit coverage per module; all contracts and saga paths MUST have tests.

Rationale: Contracts and flows are the system’s truth; testing them guards against regressions.

### V. Performance & Observability
Services MUST publish metrics (latency p50/p95/p99, throughput, error rates), logs (structured, correlation IDs), and traces (OpenTelemetry compatible). Performance budgets MUST be defined per API: target ≤ p95 200ms for synchronous calls unless justified; backpressure, timeouts, circuit breakers, and concurrency limits MUST be implemented where applicable. Load tests MUST validate SLOs before release.

Rationale: Measured systems perform predictably; observability enables fast diagnosis and reliable operations.

## Additional Constraints & Security
Security posture MUST follow least-privilege and defense-in-depth:
- Secrets management via environment/secure vault; never in code.
- Mutual TLS or signed tokens for inter-service auth; RBAC for admin ops.
- Data-in-transit encryption; sensitive data at-rest encryption where applicable.
- Audit trails for saga state changes and administrative actions.

Consistency mode MUST be documented per operation (read-your-writes, eventual, monotonic). Exactly-once semantics MUST NOT be assumed; use idempotency keys and deduplication.

## Development Workflow & Quality Gates
- **Branching**: trunk-based or short-lived feature branches with frequent integration.
- **Reviews**: at least one peer review; architectural changes require ADR.
- **CI Gates**: format/lint, unit, contract, integration tests, vulnerability scan, performance smoke tests.
- **Releases**: semantic versioning; MAJOR requires migration guide and rollout plan (canary recommended). Rollbacks MUST be documented and tested.
- **Runtime Guidance**: see project README for setup, tracing, and operations.

## Governance
This constitution supersedes conflicting practices. Amendments require an RFC with rationale, impact analysis, and migration plan. Compliance is verified in PR reviews via Quality Gates. Versioning policy:
- **MAJOR**: Backward-incompatible governance changes or removals.
- **MINOR**: New principle/section added or materially expanded guidance.
- **PATCH**: Clarifications, wording, typo fixes, non-semantic refinements.

Quarterly compliance reviews assess adherence and propose improvements.

**Version**: 1.0.0 | **Ratified**: 2026-01-15 | **Last Amended**: 2026-01-15




























