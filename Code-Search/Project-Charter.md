# Semantic Code Search MVP – Lightweight Product Documentation
<a id="title"></a>

## Table of contents
<a id="toc"></a>

- [1) Project charter / one‑pager](#project-charter)
  - [Problem statement](#problem-statement)
  - [Who it’s for](#audience)
  - [Goals (MVP)](#goals-mvp)
  - [Non-goals (MVP)](#non-goals-mvp)
  - [Scope (MVP)](#scope-mvp)
  - [Success metrics (first 90 days)](#success-metrics)
  - [Risks and mitigations](#risks-and-mitigations)
  - [Timeline and milestones](#timeline-and-milestones)
  - [Stakeholders](#stakeholders)
  - [Dependencies and assumptions](#dependencies-and-assumptions)
- [2) Personas, use cases, and user journeys](#personas-use-cases-journeys)
  - [Personas](#personas)
  - [Quick user journeys](#quick-user-journeys)
  - [Canonical tasks with example queries (MVP set of 15)](#canonical-tasks)
    - [Use case 1) Business workflow code](#use-case-1)
    - [Use case 2) Trace an error handling path](#use-case-2)
    - [Use case 3) Locate event producers/consumers](#use-case-3)
    - [Use case 4) Feature flag usage](#use-case-4)
    - [Use case 5) Sensitive data handling](#use-case-5)
    - [Use case 6) API client patterns](#use-case-6)
    - [Use case 7) Security risky patterns](#use-case-7)
    - [Use case 8) Data writes and schema](#use-case-8)
    - [Use case 9) Observability](#use-case-9)
    - [Use case 10) Caching](#use-case-10)
    - [Use case 11) Tests and mocking](#use-case-11)
    - [Use case 12) IaC posture](#use-case-12)
    - [Use case 13) Privacy workflows](#use-case-13)
    - [Use case 14) Config and secrets](#use-case-14)
    - [Use case 15) Legacy tech/migration](#use-case-15)
- [3) Product requirements (PRD) and acceptance criteria](#prd-acceptance)
  - [Functional requirements](#functional-requirements)
    - [Search and query types](#search-and-query-types)
    - [Filters (MVP)](#filters-mvp)
    - [Results and UX](#results-and-ux)
    - [Permissions](#permissions)
    - [Integrations](#integrations)
    - [Indexing](#indexing)
    - [Observability and feedback](#observability-and-feedback)
    - [Admin controls](#admin-controls)
  - [Non-functional requirements](#non-functional-requirements)
    - [Performance and availability](#performance-and-availability)
    - [Scalability and capacity (MVP targets)](#scalability-and-capacity)
    - [Freshness and reliability](#freshness-and-reliability)
    - [Security and privacy](#security-and-privacy)
    - [Cost targets](#cost-targets)
    - [Compliance](#compliance)
    - [Accessibility and usability](#accessibility-and-usability)
  - [Acceptance criteria per use case](#acceptance-criteria)
    - [Baseline acceptance (applies to all use cases)](#baseline-acceptance)
    - [Per-use-case acceptance](#per-use-case-acceptance)
      - [Acceptance 1) Business workflow code](#acceptance-1)
      - [Acceptance 2) Error handling path](#acceptance-2)
      - [Acceptance 3) Event producers/consumers](#acceptance-3)
      - [Acceptance 4) Feature flag usage](#acceptance-4)
      - [Acceptance 5) Sensitive data handling](#acceptance-5)
      - [Acceptance 6) API client patterns (retries)](#acceptance-6)
      - [Acceptance 7) Security risky patterns](#acceptance-7)
      - [Acceptance 8) Data writes and schema](#acceptance-8)
      - [Acceptance 9) Observability](#acceptance-9)
      - [Acceptance 10) Caching](#acceptance-10)
      - [Acceptance 11) Tests and mocking](#acceptance-11)
      - [Acceptance 12) IaC posture](#acceptance-12)
      - [Acceptance 13) Privacy workflows](#acceptance-13)
      - [Acceptance 14) Config and secrets](#acceptance-14)
      - [Acceptance 15) Legacy tech/migration](#acceptance-15)
  - [Test data and evaluation notes](#test-data-and-evaluation-notes)
  - [Out of scope for MVP](#out-of-scope)
- [Summary](#summary)

---

## 1) Project charter / one‑pager
<a id="project-charter"></a>

### Problem statement
<a id="problem-statement"></a>

- Engineers and partners spend too much time finding the right code when they only know the business idea (for example “refund flow” or “GDPR delete”), not exact file or function names. Grep and IDE search miss cross-language, pattern-based, or poorly named code.
- This slows onboarding, incident response, refactoring, and compliance tasks.

### Who it’s for
<a id="audience"></a>

- Primary: Backend developers, SREs, data engineers, platform engineers.
- Secondary: Security engineers, QA, frontend developers, DevOps/IaC owners.
- Tertiary (read‑only): Product managers, support engineers, technical writers.

### Goals (MVP)
<a id="goals-mvp"></a>

- Let users ask natural-language or snippet-based questions and get relevant code results fast.
- Support core filters: language, repo, branch.
- Respect repository permissions automatically.
- Provide a simple web UI and minimal IDE/CLI integration.
- Cover the top 10–20 everyday developer and ops tasks.

### Non-goals (MVP)
<a id="non-goals-mvp"></a>

- No code generation or auto‑fixes.
- No full static analysis or formal data lineage.
- No guaranteed full recall; focus on “useful top results fast.”
- No complex cross-org sharing or multi-tenant features beyond our current SCM permissions.

### Scope (MVP)
<a id="scope-mvp"></a>

- Index up to 50 repositories across 4 languages (Python, TypeScript/JavaScript, Java, Go).
- Index default branches (main/master) and optionally one additional branch per repo.
- Query types: natural language, code snippet, “find similar code to this selection.”
- Results: ranked list with snippets, file path, language tag, line anchors, open-in-repo links.
- Integrations: web app, VS Code extension (basic), CLI command.
- Logging and metrics for usage and quality.

### Success metrics (target for first 90 days)
<a id="success-metrics"></a>

- Time-to-first-answer: p95 ≤ 2 seconds for a query returning results.
- Precision@5: ≥ 70% (at least 1 truly relevant result in top 5) on a curated query set.
- Adoption: ≥ 50 weekly active engineers (or 30% of target users, whichever is greater).
- Task impact: self-reported “time saved” ≥ 30% on onboarding/triage/refactor tasks.
- Index freshness: 95% of changes searchable within 15 minutes of push to main.

### Risks and mitigations
<a id="risks-and-mitigations"></a>

- False positives or noisy results: include snippet context and quick feedback controls; iterate ranking.
- Privacy/leaks: enforce SCM permissions end-to-end; redact logs; encrypt at rest and in transit.
- Stale indexes: near-real-time incremental indexing; fallback reindex nightly.
- Cost creep: cap index size and embedding calls; cache; set per-repo inclusion rules.
- Adoption friction: default keyboard shortcuts and simple UI; lightweight onboarding.

### Timeline and milestones
<a id="timeline-and-milestones"></a>

- Week 0–2: Design and spike. Pick embedding and re-ranking approach. Define schema and filters.
- Week 3–6: Build indexer, core search service, web UI v1, permissions integration, basic metrics.
- Week 7–8: VS Code and CLI integration (basic). Curate evaluation set. Tune ranking.
- Week 9–10: Pilot with 2–3 teams. Collect feedback. Improve relevance and filters.
- Week 11–12: Hardening, docs, training, and broader rollout.

### Stakeholders
<a id="stakeholders"></a>

- Sponsor: Head of Engineering / VP Platform.
- Product owner: Developer Experience PM.
- Engineering: Platform/DevEx team (backend, frontend), SRE (infra, observability).
- Security: AppSec for permissions, logging, and data handling.
- Champions: 5–10 engineers across backend, SRE, data for pilot feedback.

### Dependencies and assumptions
<a id="dependencies-and-assumptions"></a>

- Git provider (e.g., GitHub/GitLab) with SSO and repo permissions.
- Compute and storage for index service; secrets manager.
- Embedding and re-ranking models/services (self-hosted or vendor).
- Access to CI events or webhooks to trigger indexing.

---

## 2) Personas, use cases, and user journeys
<a id="personas-use-cases-journeys"></a>

### Personas
<a id="personas"></a>

- Backend developer: Finds feature logic, handlers, data access, patterns to reuse.
- SRE: Locates error handling, logging, metrics, and deployment/IaC settings.
- Data engineer: Finds ETL jobs, schemas, lineage points, and data writes.
- Security engineer: Finds risky patterns, secrets handling, authz logic.
- QA/test engineer: Finds test coverage and mocking examples.
- Frontend developer: Finds UI flows, i18n strings, API calls to backend.
- DevOps/IaC owner: Finds Terraform/Helm settings and deployment configs.
- Support/PM (read-only): Finds user-visible messages and feature flags.

### Quick user journeys
<a id="quick-user-journeys"></a>

- Feature discovery: User types “Where is the checkout tax calculation?” Filters by repo=web-backend, language=python. Opens top result, jumps to function, follows links to related files.
- Incident triage: User types “payment declined error logs orders.” Filters by time/branch if available. Finds handler, opens linked metric, shares permalink in incident channel.
- Migration/refactor: User types “places using legacy HttpClient” and filters language=java. Reviews results, opens in IDE, and bulk updates.

### Canonical tasks with example queries (MVP set of 15)
<a id="canonical-tasks"></a>

#### Use case 1) Business workflow code
<a id="use-case-1"></a>

- Query: “Where is checkout tax and shipping calculated?”
- Variant: “calculate order total with tax and shipping”

#### Use case 2) Trace an error handling path
<a id="use-case-2"></a>

- Query: “Where do we handle ‘payment declined’ errors?”
- Variant: “payment declined exception mapping to HTTP 402”

#### Use case 3) Locate event producers/consumers
<a id="use-case-3"></a>

- Query: “Who publishes orders.created event?”
- Variant: “Kafka topic orders.created produce code”

#### Use case 4) Feature flag usage
<a id="use-case-4"></a>

- Query: “Every usage of feature flag new_checkout”
- Variant: “fallback path when new_checkout flag is off”

#### Use case 5) Sensitive data handling
<a id="use-case-5"></a>

- Query: “Where do we log email addresses?”
- Variant: “PII email in logs or metrics”

#### Use case 6) API client patterns
<a id="use-case-6"></a>

- Query: “Examples of retry with exponential backoff for HTTP calls”
- Variant: “HTTP client with retry policy and circuit breaker”

#### Use case 7) Security risky patterns
<a id="use-case-7"></a>

- Query: “Code building shell commands from user input”
- Variant: “possible command injection from request parameters”

#### Use case 8) Data writes and schema
<a id="use-case-8"></a>

- Query: “Where do we write to user_profiles table?”
- Variant: “SQL updates for user email”

#### Use case 9) Observability
<a id="use-case-9"></a>

- Query: “Where is orders_processing_latency metric emitted?”
- Variant: “request latency metrics for /orders endpoint”

#### Use case 10) Caching
<a id="use-case-10"></a>

- Query: “Where are user profiles cached and TTL set?”
- Variant: “cache set user profile expire minutes”

#### Use case 11) Tests and mocking
<a id="use-case-11"></a>

- Query: “Examples of mocking Redis in unit tests”
- Variant: “integration tests for payments API”

#### Use case 12) IaC posture
<a id="use-case-12"></a>

- Query: “Terraform modules creating public S3 buckets”
- Variant: “Helm charts missing CPU limits”

#### Use case 13) Privacy workflows
<a id="use-case-13"></a>

- Query: “Where is GDPR delete implemented?”
- Variant: “erase user data across services”

#### Use case 14) Config and secrets
<a id="use-case-14"></a>

- Query: “Where is JWT secret read from env or config?”
- Variant: “token signing key configuration”

#### Use case 15) Legacy tech/migration
<a id="use-case-15"></a>

- Query: “Places still using legacy HttpClient”
- Variant: “migrate from Joda-Time to java.time usage examples”

---

## 3) Product requirements (PRD) and acceptance criteria
<a id="prd-acceptance"></a>

### Functional requirements
<a id="functional-requirements"></a>

#### Search and query types
<a id="search-and-query-types"></a>

- Natural-language queries with synonyms and domain terms.
- Code snippet search: paste code and find similar code.
- “Find similar” from a file/selection in IDE.
- Boolean/keyword hints supported (AND, quotes) as a best-effort, not strict parser.

#### Filters (MVP)
<a id="filters-mvp"></a>

- Language: Python, TypeScript/JavaScript, Java, Go.
- Repo: one or more repositories by name.
- Branch: default branch plus one optional per repo.
- Nice-to-have (if time permits): path include/exclude prefix, file type.

#### Results and UX
<a id="results-and-ux"></a>

- Ranked list with:
  - Code snippet preview with highlighted matches.
  - File path, repo, language, and line numbers.
  - Open in repo (web), open in IDE, copy permalink.
- Result details view with more lines of context and quick filters.
- Basic query suggestions if zero results (try different repo/language/path).

#### Permissions
<a id="permissions"></a>

- Enforce SCM permissions per user. If a user cannot view a file in the repo, it does not appear in results.
- Do not store raw code in analytics or logs; store only hashes/metadata.

#### Integrations
<a id="integrations"></a>

- Web app: search bar, filters, result list, details view.
- VS Code extension: command palette action and side panel; “search selection” action.
- CLI: codesearch “query string” --repo=… --lang=… --branch=… outputting paths and line ranges.

#### Indexing
<a id="indexing"></a>

- Incremental indexing on push to default branch via webhooks or scheduled pulls every 15 minutes.
- Full reindex nightly or on demand per repo.
- Basic deduplication (generated files, vendor directories) via default ignore rules.

#### Observability and feedback
<a id="observability-and-feedback"></a>

- Metrics: query count, latency, zero-results rate, click-through rate, Precision@K on a curated set.
- Feedback: “Was this helpful?” control per result; report false positives.

#### Admin controls
<a id="admin-controls"></a>

- Include/exclude repos and paths.
- Manage synonyms and stopwords.
- Manual reindex trigger and status dashboard.

### Non-functional requirements
<a id="non-functional-requirements"></a>

#### Performance and availability
<a id="performance-and-availability"></a>

- Latency: p95 ≤ 2.0 s for initial query; p99 ≤ 4.0 s. Autocomplete/suggestions ≤ 300 ms when present.
- Availability: 99.5% monthly for query service. Graceful degradation if re-ranker fails (serve embedding-only ranking).

#### Scalability and capacity (MVP targets)
<a id="scalability-and-capacity"></a>

- Up to 50 repos, 10 million lines of code indexed.
- Concurrency: 100 concurrent queries with stable p95 latency.
- Storage: sized for embeddings plus metadata with 30% headroom.

#### Freshness and reliability
<a id="freshness-and-reliability"></a>

- 95% of default-branch updates searchable within 15 minutes.
- Index recoverable from snapshot backups; RPO ≤ 24 hours.

#### Security and privacy
<a id="security-and-privacy"></a>

- SSO via existing identity provider; enforce repo-level RBAC.
- Encrypt in transit (TLS) and at rest (storage encryption).
- PII and secrets: do not persist raw code in analytics; redact known secret patterns in logs.
- Audit logs for admin actions and index changes.

#### Cost targets (adjust to org baseline)
<a id="cost-targets"></a>

- Cloud cost budget ≤ $X/month for compute and storage in MVP.
- Observed cost per million lines indexed tracked and reported monthly.

#### Compliance
<a id="compliance"></a>

- Log retention ≤ 30 days for user-level telemetry; configurable for org needs.

#### Accessibility and usability
<a id="accessibility-and-usability"></a>

- Keyboard navigation for results.
- Clear empty-state guidance and examples.

### Acceptance criteria per use case
<a id="acceptance-criteria"></a>

#### Baseline acceptance (applies to all use cases)
<a id="baseline-acceptance"></a>

- Relevance: At least one truly relevant result in the top 5 for the curated query text.
- Latency: p95 ≤ 2.0 s for the query used in testing.
- Permissions: Results never include files the tester cannot access.
- Freshness: If test code was updated on default branch >15 minutes before, results reflect the change.

#### Per-use-case acceptance
<a id="per-use-case-acceptance"></a>

##### Acceptance 1) Business workflow code
<a id="acceptance-1"></a>

- Given the query “Where is checkout tax and shipping calculated?” top 5 includes the function or module performing tax/shipping math.
- Clicking a result shows the lines with the calculation and related helper references.

##### Acceptance 2) Error handling path
<a id="acceptance-2"></a>

- Query “Where do we handle ‘payment declined’ errors?” returns handler code or error-mapping within top 5.
- Result snippet contains the error string, exception type, or status mapping.

##### Acceptance 3) Event producers/consumers
<a id="acceptance-3"></a>

- Query “Who publishes orders.created event?” returns producer code in top 5 and at least one consumer within top 10 (if present).
- Results include file paths showing topic/stream name.

##### Acceptance 4) Feature flag usage
<a id="acceptance-4"></a>

- Query “Every usage of feature flag new_checkout” returns all references in the selected repo(s) with flag on/off branches visible in snippets.
- Applying language=python filter narrows results accordingly.

##### Acceptance 5) Sensitive data handling
<a id="acceptance-5"></a>

- Query “Where do we log email addresses?” shows at least one logging call with email/PII in top 5, if any exist.
- If none exist, zero-results messaging appears with guidance.

##### Acceptance 6) API client patterns (retries)
<a id="acceptance-6"></a>

- Query “Examples of retry with exponential backoff for HTTP calls” returns at least one reusable example in top 5.
- Snippet shows retry configuration or loop with backoff.

##### Acceptance 7) Security risky patterns
<a id="acceptance-7"></a>

- Query “Code building shell commands from user input” returns at least one potential risk instance in top 10.
- Snippets highlight string concatenation or unsafe exec with request parameters.

##### Acceptance 8) Data writes and schema
<a id="acceptance-8"></a>

- Query “Where do we write to user_profiles table?” returns SQL write locations in top 5.
- Snippet includes table or model name and the write operation.

##### Acceptance 9) Observability
<a id="acceptance-9"></a>

- Query “Where is orders_processing_latency metric emitted?” returns metric emission code in top 5.
- Clicking opens code showing labels/tags for the metric.

##### Acceptance 10) Caching
<a id="acceptance-10"></a>

- Query “Where are user profiles cached and TTL set?” returns cache set/get calls in top 5.
- Snippets include TTL configuration.

##### Acceptance 11) Tests and mocking
<a id="acceptance-11"></a>

- Query “Examples of mocking Redis in unit tests” returns at least one test file in top 5.
- Results open to test cases with clear mocking patterns.

##### Acceptance 12) IaC posture
<a id="acceptance-12"></a>

- Query “Terraform modules creating public S3 buckets” returns any modules with public ACL or policy in top 10.
- If language filter=terraform is applied, only IaC files are shown.

##### Acceptance 13) Privacy workflows
<a id="acceptance-13"></a>

- Query “Where is GDPR delete implemented?” returns deletion jobs/handlers in top 5 if present.
- Snippets show code that enumerates user-related tables/resources.

##### Acceptance 14) Config and secrets
<a id="acceptance-14"></a>

- Query “Where is JWT secret read from env or config?” returns config loader code in top 5.
- Results include the env var or config key access.

##### Acceptance 15) Legacy tech/migration
<a id="acceptance-15"></a>

- Query “Places still using legacy HttpClient” returns usage sites in top 10.
- Applying language=java filter reduces to Java-only results and still surfaces matches.

### Test data and evaluation notes
<a id="test-data-and-evaluation-notes"></a>

- Maintain a small, versioned query set that maps each use case to known relevant files for periodic regression checks.
- Track Precision@5 and Zero-results rate per use case over time.

### Out of scope for MVP
<a id="out-of-scope"></a>

- Cross-organization sharing or external customer access.
- Write APIs to push private code outside our control.
- Full semantic diffing or refactoring tools.
- Natural language chatbots that edit code.

---

## Summary
<a id="summary"></a>

- We will deliver a semantic code search MVP for engineers to find code by intent, not just keywords. It supports natural-language and snippet queries, filters (language, repo, branch), and shows ranked results with context. It respects existing repo permissions.
- Target impact: faster onboarding, faster incident triage, and smoother refactors. Success looks like p95 latency ≤ 2 s, Precision@5 ≥ 70%, 30% time saved on key tasks, and strong weekly adoption.
- Scope covers up to 50 repos and 4 languages, with a web UI, basic VS Code extension, and CLI. Indexes refresh within 15 minutes for most changes.
- Risks (relevance, privacy, cost, freshness) are addressed with ranking feedback, strict RBAC, budget caps, and incremental indexing.
- Timeline: 12 weeks from design to rollout, including a 2–3 week pilot. The MVP ships with 15 canonical tasks and clear acceptance criteria to measure success and guide iteration.
