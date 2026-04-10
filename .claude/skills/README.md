# AgentForge Skill Library

## Purpose
This file documents the skill configurations that each agent is loaded with.
Skills are reusable capability bundles referenced by agent commands.

---

## Skill: code-generation

**Used by:** Frontend Agent, Backend Agent, Database Agent

```yaml
skill: code-generation
capabilities:
  - typescript_strict_mode: true
  - prefer_functional_components: true
  - always_type_props: true
  - prefer_zod_over_joi: true
  - prefer_prisma_over_raw_sql: true
  - error_handling: explicit_try_catch_or_error_boundaries
  - naming_conventions:
      files: kebab-case
      components: PascalCase
      functions: camelCase
      constants: SCREAMING_SNAKE_CASE
      types: PascalCase
      db_tables: snake_case
```

---

## Skill: api-design

**Used by:** Architect Agent, Backend Agent

```yaml
skill: api-design
conventions:
  base_path: /api/v1
  response_envelope:
    success: { data: T, success: true }
    error: { error: { message: string, code: string }, success: false }
    list: { data: T[], meta: { total, page, limit, totalPages }, success: true }
  http_status_codes:
    200: success GET/PUT/PATCH
    201: success POST (created)
    204: success DELETE (no content)
    400: bad request / validation error
    401: unauthenticated
    403: unauthorized (authenticated but no permission)
    404: not found
    409: conflict (duplicate)
    422: unprocessable entity
    429: rate limited
    500: internal server error
  auth_header: "Authorization: Bearer <token>"
  pagination_params: { page: number, limit: number, cursor?: string }
```

---

## Skill: database-design

**Used by:** Database Agent, Architect Agent

```yaml
skill: database-design
conventions:
  id_strategy: uuid (not auto-increment)
  soft_deletes: add deletedAt DateTime? to entities where data retention matters
  audit_fields: createdAt, updatedAt on every table
  foreign_key_naming: "[referencedTable]Id"
  index_strategy:
    - all foreign keys get an index
    - fields in WHERE clauses of frequent queries
    - compound indexes for multi-field filters
  enum_storage: PostgreSQL native enums (not string columns)
  json_columns: use for flexible/schemaless data, never for queryable data
```

---

## Skill: security

**Used by:** All agents (especially Backend, QA)

```yaml
skill: security
requirements:
  passwords: bcrypt with cost factor 12+
  jwt:
    algorithm: HS256
    access_token_ttl: 15m (short-lived)
    refresh_token_ttl: 30d
    refresh_tokens_stored_in_db: true (for revocation)
  cors:
    allowed_origins: from FRONTEND_URL env var only
    credentials: true
  rate_limiting:
    auth_routes: 5 requests per minute
    api_routes: 100 requests per minute
    per: ip + user_id
  input_sanitization:
    - strip HTML from string inputs
    - validate UUIDs before DB queries
    - max body size: 10mb
  headers:
    - X-Content-Type-Options: nosniff
    - X-Frame-Options: DENY
    - Strict-Transport-Security (production)
  never:
    - expose stack traces in production errors
    - log passwords or tokens
    - return password hashes in API responses
    - use eval() or Function()
    - trust user-provided IDs for ownership checks
```

---

## Skill: frontend-ux

**Used by:** Frontend Agent

```yaml
skill: frontend-ux
requirements:
  loading_states:
    - skeleton screens for content areas (not spinners)
    - button loading states during async actions
    - optimistic updates for mutations
  error_states:
    - form field errors shown inline below the field
    - toast notifications for API errors
    - error boundaries for unexpected crashes
    - empty states with clear CTAs (not just "No data")
  accessibility:
    - all images have alt text
    - form inputs have labels (not just placeholders)
    - focus management on dialogs/drawers
    - keyboard navigable menus
    - sufficient color contrast (WCAG AA)
  responsive:
    - mobile-first (375px base)
    - tablet breakpoint: 768px
    - desktop breakpoint: 1024px
    - sidebar collapses on mobile
  performance:
    - images use next/image
    - heavy components are lazy loaded
    - lists are virtualized if >100 items
```
