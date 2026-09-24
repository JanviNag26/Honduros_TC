# AuraCommerce Engineering Constitution

**Version:** 1.0.0

> **Supreme Authority:** This constitution is the supreme engineering authority for the AuraCommerce project. All code, architecture decisions, and automated agents must strictly adhere to these rules. Any deviation requires a formal amendment.

*(Assumption: As the project profile, name, and brief were delegated, this constitution assumes a production-grade, full-stack e-commerce web application named "AuraCommerce" built on a modern TypeScript/Next.js stack.)*

## Mission
To deliver a highly performant, accessible, and scalable e-commerce platform that provides a seamless shopping experience for users while maintaining rigorous standards for security, data integrity, and developer ergonomics.

## Core Values
*   **Correctness over speed:** It is better to ship late than to ship broken.
*   **Security over convenience:** Security boundaries are absolute and non-negotiable.
*   **Simplicity over cleverness:** Code must be readable and maintainable by junior engineers.
*   **Maintainability over shortcuts:** Technical debt must be explicitly tracked and minimized.
*   **Observability over assumptions:** If it is not monitored, it does not work.
*   **Explicitness over magic:** Prefer explicit configuration and data flow over implicit behaviors.
*   **Automation over manual processes:** CI/CD, linting, and testing must be fully automated.
*   **Testing over trust:** All logic must be verified by automated tests.

## Technology Stack

*(Assumption: Delegated languages and required technologies resolved to a standard Next.js/TypeScript full-stack ecosystem appropriate for a modern e-commerce platform.)*

### Required Technologies
| Category | Technology | Purpose |
| :--- | :--- | :--- |
| **Language** | TypeScript (Strict) | Primary language for all application code. |
| **Framework** | Next.js (App Router) | Full-stack React framework for routing and rendering. |
| **Styling** | Tailwind CSS | Utility-first CSS framework for all UI components. |
| **Database** | PostgreSQL | Primary relational database. |
| **ORM** | Prisma | Type-safe database access. |
| **Validation** | Zod | Schema declaration and runtime data validation. |

### Forbidden Technologies & Practices
*   **Plain JavaScript:** Forbidden in application code; TypeScript is mandatory.
*   **Unmaintained Dependencies:** Libraries without updates in the last 12 months are forbidden.
*   **Experimental Libraries:** Forbidden in production without explicit architectural approval.
*   **Direct DOM Manipulation:** Forbidden; use React state and refs.
*   **Client-Side Secrets:** Never expose API keys or secrets in client-side bundles.

## Repository Structure

*(Assumption: Delegated repository structure resolved to a standard Next.js App Router layout.)*

```text
auracommerce/
├── src/
│   ├── app/              # Next.js App Router (pages, layouts, API routes)
│   ├── components/       # Reusable React components (UI and business logic)
│   ├── lib/              # Utility functions, shared logic, and API clients
│   ├── server/           # Server-only logic (actions, services, DB access)
│   └── types/            # Global TypeScript type definitions
├── prisma/               # Database schema and migrations
├── public/               # Static assets
├── tests/                # E2E and integration tests
└── docs/                 # Architecture Decision Records (ADRs) and documentation
```

## Language/Code Standards

### TypeScript Rules
*   `strict: true` must be enabled in `tsconfig.json`.
*   `any` is strictly forbidden. Use `unknown` if the type is truly dynamic, followed by type narrowing.
*   `@ts-ignore` is forbidden. Use `@ts-expect-error` with a descriptive comment if absolutely necessary.

### Naming Conventions
*(Assumption: Delegated naming conventions resolved to standard TypeScript/React idioms.)*
*   **Files/Directories:** `kebab-case` (e.g., `shopping-cart.tsx`).
*   **Components/Interfaces/Types:** `PascalCase` (e.g., `ShoppingCart`, `UserSession`).
*   **Variables/Functions/Methods:** `camelCase` (e.g., `calculateTotal`, `cartItems`).
*   **Constants/Environment Variables:** `UPPER_SNAKE_CASE` (e.g., `MAX_RETRIES`, `DATABASE_URL`).

### Component/File Size Limits
*   **Target:** 300 lines of code per file.
*   **Mandatory Refactor:** 500 lines of code per file.

## Frontend Standards

*   **React Server Components (RSC):** Default to Server Components. Only use `"use client"` when interactivity, hooks (`useState`, `useEffect`), or browser APIs are strictly required.
*   **State Management:** Prefer URL state (query parameters) and server state over complex client-side state management.
*   **Styling:** Use Tailwind CSS exclusively. Avoid inline styles. Group utility classes logically.
*   **Data Fetching:** Fetch data on the server whenever possible.

## Backend/API & Validation Standards

*   **API Design:** Use Next.js Route Handlers (`app/api/...`) for external integrations and Server Actions for internal mutations.
*   **Validation:** All incoming data (API payloads, form submissions, query parameters) MUST be validated at the boundary using Zod.
*   **Database Access:** All database queries must go through Prisma. Raw SQL is forbidden unless explicitly approved for performance reasons.
*   **Business Logic:** Keep route handlers and server actions thin. Delegate complex logic to dedicated service functions in `src/server/`.

## Error Handling

Errors must be categorized, logged, and handled gracefully without exposing internal stack traces to the client.

| Category | Description | HTTP Status |
| :--- | :--- | :--- |
| `VALIDATION_ERROR` | Invalid input data (e.g., Zod parse failures). | 400 |
| `AUTHENTICATION_ERROR` | Missing or invalid credentials. | 401 |
| `AUTHORIZATION_ERROR` | Authenticated user lacks required permissions. | 403 |
| `BUSINESS_ERROR` | Domain rule violation (e.g., insufficient stock). | 409 / 422 |
| `EXTERNAL_SERVICE_ERROR` | Failure in a third-party API (e.g., payment gateway). | 502 |
| `INFRASTRUCTURE_ERROR` | Database or cache connectivity issues. | 503 |
| `UNKNOWN_ERROR` | Unhandled exceptions. | 500 |

## Logging

All logs must be structured JSON. Standard `console.log` is forbidden in production; use the designated logging utility (e.g., Pino).

**Required Structured Log Fields:**
*   `event`: String identifier for the log event.
*   `timestamp`: ISO 8601 formatted date string.
*   `requestId`: Unique identifier for tracing the request lifecycle.
*   `userId`: (Optional) ID of the authenticated user.
*   `metadata`: (Optional) Additional contextual data.

```json
{
  "level": "error",
  "event": "payment_processing_failed",
  "timestamp": "2023-10-27T10:00:00Z",
  "requestId": "req_12345abcde",
  "userId": "usr_98765",
  "metadata": {
    "orderId": "ord_555",
    "gatewayError": "insufficient_funds"
  }
}
```

## Security

*   **Authentication & Authorization:** Must occur server-side. Client-side checks are for UX only, never for security.
*   **Secrets Handling:**
    *   Must be injected via environment variables.
    *   Production secrets must be sourced from a secure Secret Manager.
    *   **Never commit secrets** to version control.
*   **Dependency Policy:**
    *   Must pass automated security scans (e.g., `npm audit`).
    *   Must pass license review (no copyleft licenses like GPL in proprietary code).
    *   Must be actively maintained.
    *   Prefer building over adding a dependency when the required functionality is small.

## Accessibility

*   **Standard:** All user interfaces must comply with WCAG 2.1 AA standards.
*   **Semantic HTML:** Use native HTML elements (`<button>`, `<nav>`, `<dialog>`) appropriately.
*   **ARIA:** Use ARIA attributes only when native HTML is insufficient.
*   **Keyboard Navigation:** All interactive elements must be fully operable via keyboard.

## Performance

*   **Core Web Vitals:** The application must meet the following thresholds in production:
    *   Largest Contentful Paint (LCP): < 2.5 seconds.
    *   First Input Delay (FID): < 100 milliseconds.
    *   Cumulative Layout Shift (CLS): < 0.1.
*   **Asset Optimization:** Images must be optimized using Next.js `<Image>` component.

## Testing

*(Assumption: Delegated test types resolved to standard web application testing layers.)*

*   **Minimum Coverage:** 80% minimum overall, 95% for critical business logic (e.g., checkout, payments).
*   **Required Test Types:**
    *   **Unit Tests:** For utility functions, Zod schemas, and isolated components (Vitest).
    *   **Integration Tests:** For API routes, Server Actions, and database queries.
    *   **End-to-End (E2E) Tests:** For critical user journeys like login and checkout (Playwright).

## CI/CD

All code must pass through an automated CI pipeline before merging.

**CI Gates per PR:**
1.  Linting (ESLint, Prettier).
2.  Typechecking (`tsc --noEmit`).
3.  Unit Tests.
4.  Integration Tests.
5.  Security Scan (Dependency audit, secret detection).

## Documentation

*   **README:** Must contain setup instructions, environment variable requirements, and local development commands.
*   **Architecture Decision Records (ADRs):** Required for any significant architectural change or new technology adoption, stored in `docs/adr/`.
*   **Code Comments:** Use TSDoc for complex functions, interfaces, and public APIs. Explain *why*, not *what*.

## Observability

*(Assumption: Delegated observability requirements resolved to standard APM and telemetry practices.)*

*   **Tracing:** Distributed tracing (OpenTelemetry) must be implemented across all API routes and database calls.
*   **Metrics:** Track key business metrics (e.g., order completion rate) and system metrics (e.g., API latency, DB query time).
*   **Alerting:** Critical errors (`INFRASTRUCTURE_ERROR`, `UNKNOWN_ERROR`) must trigger immediate alerts to the engineering team.

## AI Development Rules

*   **AI-Generated Code Policy:** AI code is UNTRUSTED. It must be reviewed, tested, and validated by a human engineer before merging.
*   **Agent Restrictions (without human approval):**
    *   May NOT deploy to production.
    *   May NOT rotate credentials.
    *   May NOT modify infrastructure.
    *   May NOT approve pull requests.

## Prompt / MCP / RAG Standards

*   **Prompts:** Must be version-controlled, documented, and tested alongside application code. Changes require code review.
*   **MCP Integrations:** Must operate on the principle of least-privilege, be fully auditable, and be instantly revocable.
*   **RAG Sources:** Must be trusted, versioned, and explicitly source-attributed in outputs.

## Code Review Standards

Every Pull Request must explicitly answer the following questions in its description:
1.  **What changed?**
2.  **Why?**
3.  **Risks?** (What could break?)
4.  **Rollback plan?**
5.  **Testing evidence?** (Screenshots, test output, or specific test cases added).

## Git Standards

*   **Branch Conventions:** `feature/*`, `bugfix/*`, `hotfix/*`, `chore/*`.
*   **Commit Conventions:** Follow Conventional Commits (`feat:`, `fix:`, `refactor:`, `test:`, `docs:`, `perf:`, `chore:`).
*   **History:** Squash commits upon merging to maintain a clean main branch history.

## Dependency Rules

*   All dependencies must be pinned to exact versions in `package.json`.
*   Adding a new production dependency requires justification in the PR and approval from a senior engineer.

## Definition of Done

A feature or bugfix is only "Done" when:
- [ ] Requirements are fully implemented.
- [ ] Tests are written and passing.
- [ ] Typecheck is passing.
- [ ] Linting is passing.
- [ ] Security review is completed.
- [ ] Documentation (and ADRs, if applicable) is updated.
- [ ] Accessibility is validated.
- [ ] Performance is validated.
- [ ] Code is reviewed and approved.

## Non-Negotiable Rules (NEVER / ALWAYS)

*   **NEVER** commit secrets, API keys, or `.env.local` files to version control.
*   **NEVER** bypass CI checks or force-push to the `main` branch.
*   **NEVER** trust client-side data; **ALWAYS** validate at the server boundary.
*   **ALWAYS** handle errors gracefully and log them with structured context.
*   **ALWAYS** ensure the application compiles without TypeScript errors or warnings.

## Amendment Process

Changes to this constitution require a formal process:
1.  **Written Proposal:** Submit a PR modifying this document with a detailed rationale.
2.  **Architecture Review:** The proposal must be reviewed by the core architecture team.
3.  **Team Approval:** Requires consensus or a majority vote from the engineering team.
4.  **Version Increment:** Upon approval, the version number at the top of this document must be incremented (SemVer).