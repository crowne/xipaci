<!--
Sync Impact Report:
- Version change: initial → 1.0.0
- New constitution created for Xipaci project
- Principles defined: Repository Structure, Authentication & Database, Backend (Golang), Frontend (Flutter), Event-Driven Architecture, Development Workflow, Coding Standards & Linters, Compliance & Observability, Secret Management, Versioning Strategy, Identifier Safety & URL Hygiene
- Templates requiring updates: ✅ All templates compatible with new constitution
- Follow-up TODOs: None - all placeholders filled
-->

# Xipaci Constitution

## Core Principles

### I. Repository Structure
**Principle**: Enforce modularity, reproducibility, and separation of concerns.

The monorepo MUST maintain strict separation between backend-go, frontend-flutter, shared assets, and infrastructure components. Each component MUST be independently buildable and testable. The structure `/backend-go`, `/frontend-flutter`, `/shared`, and `/infra` is non-negotiable and ensures clear boundaries for development teams.

**Rationale**: Modularity prevents coupling issues, enables independent deployment, and supports parallel development across teams.

### II. Authentication & Database
**Principle**: Use a unified provider for authentication and database to reduce integration complexity.

Supabase MUST be used for both authentication (GoTrue) and database (PostgreSQL). The backend MUST use pgx driver for database access. Schema management MUST use Supabase migration tooling with schemas stored in `/shared/schemas`.

**Rationale**: Single provider reduces authentication-database integration complexity and ensures consistent security policies.

### III. Backend (Golang)
**Principle**: Begin with idiomatic Go and minimal dependencies. Introduce external libraries only when justified by complexity or compliance.

Go 1.25.3 MUST be used. HTTP routing MUST use Go standard library (net/http, http.ServeMux). SQL access MUST use pgx. Event transport MUST use PostgreSQL LISTEN/NOTIFY. Testing MUST use testify framework.

**Rationale**: Standard library approach reduces dependency bloat and ensures long-term maintainability while leveraging Go's built-in capabilities.

### IV. Frontend (Flutter)
**Principle**: Use a single codebase for mobile and web with predictable state management and testability.

Clean Architecture MUST be implemented. State management MUST use Riverpod. Authentication MUST use Supabase Flutter SDK. Testing MUST include flutter_test for unit/widget/integration tests and golden_toolkit for visual regression testing.

**Rationale**: Single codebase reduces maintenance overhead while Clean Architecture ensures testability and Riverpod provides predictable state management.

### V. Event-Driven Architecture
**Principle:** Use event-driven architecture to decouple services and enable asynchronous workflows where appropriate. It is the preferred model for domain-level interactions that benefit from loose coupling, auditability, and scalability. However, for latency-sensitive or transactional flows — such as user login, authentication, or direct API queries — a traditional request-response model is more suitable and SHOULD be used.

PostgreSQL LISTEN/NOTIFY MUST be used as transport layer. Events MUST be JSON format with required fields: event_id (uuid), event_type (string), timestamp (RFC3339), trace_id (uuid), data (object). Channel naming MUST follow `domain_event:<event_type>` pattern. EventPublisher and EventSubscriber interfaces MUST be defined for abstraction.

**Rationale**: Database-native events ensure ACID properties while maintaining auditability and enabling future transport layer changes.

### VI. Development Workflow
**Principle**: All changes must be test-driven, incremental, and CI-enforced.

Test-Driven Development (TDD) is MANDATORY. Trunk-based development MUST be used. GitHub Actions MUST enforce Lint → Test → Build → Deploy pipeline. Feature flags MUST be implemented using go-feature-flag (backend) and flutter_feature_flags (frontend).

**Rationale**: TDD ensures quality, trunk-based development reduces merge conflicts, and CI enforcement prevents regression.

### VII. Coding Standards & Linters
**Principle**: Enforce consistent style and static analysis across all subprojects.

Each subproject MUST use specified linters: backend-go (golangci-lint, go fmt), frontend-flutter (dart analyze, dart format), shared/scripts (shellcheck, eslint), infra (yamllint, jsonlint). Pre-commit hooks MUST use lefthook. EditorConfig MUST be enforced.

**Rationale**: Consistent style reduces cognitive load and improves code review efficiency while static analysis catches errors early.

### VIII. Compliance & Observability
**Principle**: All systems must be observable, traceable, and privacy-compliant.

Logging MUST use zerolog (backend) and logger (frontend). Tracing MUST use OpenTelemetry. No PII MUST appear in logs. Pseudonymization MUST be applied where applicable. Supabase RLS MUST control data access.

**Rationale**: Observability enables debugging and monitoring while privacy compliance is legally required and builds user trust.

### IX. Secret Management
**Principle**: Secrets must never be committed to source control.

.gitignore MUST include .env, .supabase, .key, .pem extensions. CI MUST scan via gitleaks. Pre-commit hooks via lefthook MUST prevent secret commits. Secrets MUST be stored in environment variables or Supabase config. Only .env.example files are permitted for documentation.

**Rationale**: Secret exposure creates security vulnerabilities and compliance violations that can compromise the entire system.

### X. Versioning Strategy
**Principle**: All components must follow a predictable, auditable versioning scheme.

Semantic Versioning (MAJOR.MINOR.PATCH) MUST be used per semver.org. All public API endpoints MUST include major version in URL path (e.g., /v1/users). Internal services MAY use header-based or schema-based versioning if justified.

**Rationale**: Predictable versioning enables safe API evolution and clear upgrade paths for clients.

### XI. Identifier Safety & URL Hygiene
**Principle**: Identifiers must be unguessable, privacy-safe, and non-sequential.

Auto-incrementing integers are PROHIBITED for sensitive entities (user_id, order_id). UUIDs MUST be used for all exposed identifiers. XID format is PREFERRED (compact, sortable UUID variant). All entity schemas MUST define primary keys as xid or uuid. All API routes MUST use UUIDs in path parameters. CI MUST include schema linting to detect unsafe key definitions.

**Rationale**: Sequential IDs enable enumeration attacks and information leakage while UUIDs provide privacy and security through unpredictability.

## Technology Stack Requirements

**Backend**: Go 1.25.3, PostgreSQL via pgx, Supabase GoTrue, net/http, testify
**Frontend**: Flutter with Riverpod, Supabase Flutter SDK, flutter_test, golden_toolkit
**Database**: PostgreSQL (Supabase), schema versioning via Supabase migrations
**Authentication**: Supabase GoTrue with RLS policies
**Event System**: PostgreSQL LISTEN/NOTIFY, JSON payloads, structured logging
**CI/CD**: GitHub Actions, golangci-lint, dart analyze, gitleaks, lefthook

## Development Standards

**Testing**: TDD mandatory, contract tests for service boundaries, visual regression for shared UI
**Code Style**: EditorConfig enforced, language-specific linters in CI, pre-commit hooks
**Branching**: Trunk-based development, feature flags for incomplete features
**Documentation**: API versioning in URLs, schema definitions in `/shared/schemas`
**Security**: No secrets in source control, PII pseudonymization, Supabase RLS enforcement

## Governance

This constitution supersedes all other development practices and MUST be adhered to by all contributors. 

**Amendment Process**: Constitution changes require version bump with impact analysis and template synchronization. MAJOR version for principle removals/redefinitions, MINOR for new principles, PATCH for clarifications.

**Compliance Review**: All pull requests MUST verify constitution compliance. Violations require explicit justification and approval. Complexity introductions MUST be documented with migration plans.

**Template Alignment**: Templates in `.specify/templates/` MUST align with constitutional principles. Plan templates MUST include constitution checks, spec templates MUST enforce versioning requirements, task templates MUST reflect principle-driven categorization.

**Version**: 1.0.0 | **Ratified**: 2025-10-19 | **Last Amended**: 2025-10-19
