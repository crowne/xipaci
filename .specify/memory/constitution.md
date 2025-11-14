<!--
Sync Impact Report:
- Version change: 1.0.0 → 2.0.0
- MAJOR version bump: Fundamental architecture change from Go+Flutter to SvelteKit PWA
- Modified principles: Repository Structure (monorepo → SvelteKit project), Backend (Go → SvelteKit+Kysely), Frontend (Flutter → SvelteKit PWA), Event-Driven Architecture (simplified for MVP), Coding Standards (Go/Dart → TypeScript/Svelte)
- Removed principles: None (adapted for new stack)
- Added sections: Capacitor mobile distribution requirements
- Templates requiring updates: ✅ Updated plan-template.md and tasks-template.md
- Follow-up TODOs: None - all placeholders updated for new architecture
-->

# Xipaci Constitution

## Core Principles

### I. Repository Structure
**Principle**: Enforce modularity, reproducibility, and separation of concerns within a fullstack SvelteKit application.

The SvelteKit project MUST maintain clear separation between frontend components, server-side routes, database schemas, and mobile configuration. The structure MUST follow SvelteKit conventions with `/src/routes` for API and pages, `/src/lib` for shared components and utilities, `/src/app.html` for app shell, and `/capacitor` for mobile builds.

**Rationale**: SvelteKit's file-based routing and component structure provides natural modularity while enabling rapid MVP development with a single codebase for web and mobile.

### II. Authentication & Database
**Principle**: Use a unified provider for authentication and database to reduce integration complexity.

Supabase MUST be used for both authentication (GoTrue) and database (PostgreSQL). The SvelteKit backend MUST use Kysely as the type-safe SQL query builder for database access. Schema management MUST use Supabase migration tooling with type definitions generated for Kysely.

**Rationale**: Single provider reduces authentication-database integration complexity while Kysely provides type safety and excellent TypeScript integration for rapid development.

### III. SvelteKit Backend
**Principle**: Leverage SvelteKit's fullstack capabilities with minimal external dependencies for rapid MVP development.

SvelteKit MUST be used for both frontend and backend (server routes). TypeScript MUST be used for type safety. Database access MUST use Kysely for type-safe queries. HTTP API routes MUST follow RESTful conventions in `/src/routes/api`. Testing MUST use Vitest framework.

**Rationale**: SvelteKit's unified development model accelerates MVP delivery while TypeScript and Kysely ensure type safety. Minimal dependencies reduce complexity for initial release.

### IV. PWA & Mobile Distribution
**Principle**: Use a single SvelteKit codebase for web, mobile web, and native mobile distribution.

The application MUST be built as a Progressive Web App (PWA) with service worker for offline capabilities. Mobile distribution MUST use Capacitor to package the PWA for iOS and Android app stores. Authentication MUST use Supabase JavaScript SDK. State management MUST use Svelte stores for simplicity.

**Rationale**: PWA approach with Capacitor enables rapid deployment across web and mobile platforms from a single codebase, reducing development time for MVP while maintaining native mobile distribution capabilities.

### V. Event-Driven Architecture
**Principle**: Implement simple event patterns appropriate for MVP scope with future extensibility.

For MVP, events SHOULD be handled through Supabase real-time subscriptions and database triggers. Complex event sourcing is DEFERRED until post-MVP. When events are needed, they MUST include event_id (uuid), event_type (string), timestamp (RFC3339), and data (object). Event handlers MUST be implemented as SvelteKit server-side functions.

**Rationale**: Simplified event handling reduces MVP complexity while Supabase real-time provides sufficient pub-sub capabilities for initial requirements. Full event architecture can be added later.

### VI. Development Workflow
**Principle**: All changes must be test-driven, incremental, and CI-enforced.

Test-Driven Development (TDD) is MANDATORY. Trunk-based development MUST be used. GitHub Actions MUST enforce Lint → Test → Build → Deploy pipeline. Feature flags MUST be implemented using environment variables and SvelteKit's conditional rendering for MVP simplicity.

**Rationale**: TDD ensures quality, trunk-based development reduces merge conflicts, and CI enforcement prevents regression. Simple feature flagging sufficient for MVP scope.

### VII. Coding Standards & Linters
**Principle**: Enforce consistent style and static analysis across the SvelteKit project.

The project MUST use ESLint for JavaScript/TypeScript linting, Prettier for code formatting, svelte-check for Svelte component analysis, and TypeScript compiler for type checking. Pre-commit hooks MUST use lefthook. EditorConfig MUST be enforced. Kysely type generation MUST be automated.

**Rationale**: Consistent tooling across the TypeScript/Svelte ecosystem reduces cognitive load and improves code review efficiency while automated type generation ensures database schema synchronization.

### VIII. Compliance & Observability
**Principle**: All systems must be observable, traceable, and privacy-compliant.

Logging MUST use console logging with structured format for SvelteKit server routes and browser console for client-side. For production, logging SHOULD integrate with Supabase Edge Functions or external service. No PII MUST appear in logs. Pseudonymization MUST be applied where applicable. Supabase RLS MUST control data access.

**Rationale**: Simple logging approach suitable for MVP while maintaining privacy compliance. Supabase RLS provides robust access control for early-stage application.

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

**Fullstack Framework**: SvelteKit with TypeScript, Vite build system
**Database**: PostgreSQL (Supabase) with Kysely type-safe query builder
**Authentication**: Supabase GoTrue with JavaScript SDK
**Mobile**: Capacitor for iOS/Android distribution, PWA for mobile web
**Testing**: Vitest for unit/integration tests, Playwright for E2E tests
**Event System**: Supabase real-time subscriptions, database triggers for MVP
**CI/CD**: GitHub Actions, ESLint, Prettier, svelte-check, TypeScript compiler, gitleaks, lefthook

## Development Standards

**Testing**: TDD mandatory, API route testing with supertest-style tests, component testing with Testing Library
**Code Style**: EditorConfig enforced, ESLint + Prettier in CI, TypeScript strict mode, pre-commit hooks
**Branching**: Trunk-based development, environment-based feature flags for incomplete features
**Documentation**: API versioning in URLs, Kysely schema types auto-generated from database
**Security**: No secrets in source control, PII pseudonymization, Supabase RLS enforcement
**Mobile**: PWA manifest for installability, Capacitor plugins for native features as needed

## Governance

This constitution supersedes all other development practices and MUST be adhered to by all contributors. 

**Amendment Process**: Constitution changes require version bump with impact analysis and template synchronization. MAJOR version for principle removals/redefinitions, MINOR for new principles, PATCH for clarifications.

**Compliance Review**: All pull requests MUST verify constitution compliance. Violations require explicit justification and approval. Complexity introductions MUST be documented with migration plans.

**Template Alignment**: Templates in `.specify/templates/` MUST align with constitutional principles. Plan templates MUST include constitution checks, spec templates MUST enforce versioning requirements, task templates MUST reflect principle-driven categorization.

**Version**: 2.0.0 | **Ratified**: 2025-10-19 | **Last Amended**: 2025-11-14
