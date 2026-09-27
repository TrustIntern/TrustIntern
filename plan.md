# TrustIntern — Build and Learning Plan

## 1. Product goal

Build and ship TrustIntern as an India-first, public, account-free service that helps students assess internship and job offers. A user can submit a URL, company and recruiter details, offer text, or an offer document. TrustIntern returns an explainable report with evidence, source links, freshness, confidence, and practical next steps. It supports safer decisions; it does not declare a person or company definitively fraudulent.

This is a real-data product, developed in small weekly iterations by two people. Every iteration should leave behind a useful standalone component, a short learning note, tests for its own behavior, and an integration point for the next feature. The visual website is an independent workstream and is broken into small deliverables too.

## 2. Decisions agreed so far

- **Audience and geography:** Students first; India is the initial jurisdiction. Expand only after India-specific source quality and safety have been evaluated.
- **Access:** No student accounts. Each completed report receives an unguessable private link and a downloadable, redacted PDF. Users can bookmark or share the link themselves.
- **Data separation:** Reusable, public company/domain facts are stored as canonical intelligence so later investigations can reuse fresh facts. User-specific submissions and findings stay with their private report and never become company-wide facts.
- **Retention:** Do not keep original uploads long term. Delete raw uploaded files no later than two hours after successful analysis. Persist only the minimum extracted/redacted evidence needed for the report. Define and implement retention/expiry controls before public launch.
- **Refresh:** Recheck reusable public facts at least monthly. Store provenance, check time, freshness/expiry, and changed-fact history. A later user should get fresh report-specific analysis plus reusable, still-fresh company facts.
- **Investigation behavior:** Run independent checks concurrently with explicit timeouts. Return a useful partial report when providers fail, clearly showing what was checked, what failed, and what remains unknown. Do not leave jobs hanging indefinitely.
- **Risk output:** Use explainable, versioned deterministic scoring for risk contributions. AI extracts and summarizes; it does not make the final score or assert guilt. Include an “insufficient evidence” outcome. Calibrate thresholds with a labeled corpus rather than treating the BRD's example bands as proven.
- **Sources:** Prefer lawful official/public sources and documented APIs. Consider additional passive OSINT only after source-specific terms, privacy, reliability, consent, and rate limits have been reviewed. Do not use login-only collection, password-reset enumeration, or bypasses in the public flow.
- **Budget:** Assume no recurring budget for the first usable public beta. Design around caching, bounded concurrency, source quotas, free/open tooling, graceful degradation, and a documented upgrade path. Free hosting is a beta constraint, not an uptime promise.
- **Team learning:** Work in weekly slices. The two contributors alternate end-to-end feature ownership; the other reviews, exercises the feature, and checks its documentation. Switch roles on the next slice. Integrate only through agreed API/data contracts.
- **Visual direction:** The initial concept generated during planning is a dark navy, student-friendly security product with restrained cyan/indigo/amber accents, a dimensional evidence-network visual, an investigation form, and an evidence-led report. Treat that image as a direction proposal, not a frozen specification. Approve responsive layouts, motion, and scroll/3D behavior before implementing them. Motion must respect reduced-motion settings and remain usable on mobile/low-powered devices.

## 3. Product architecture

```text
Next.js public website
  ├─ submission and report experiences
  └─ typed API client
       ↓
FastAPI application
  ├─ validation and safe URL/file handling
  ├─ investigation job/status/report API
  ├─ source adapters and bounded orchestration
  ├─ evidence normalization and deterministic scoring
  └─ scheduled refresh task
       ↓
PostgreSQL
  ├─ canonical public entity/source/evidence cache
  ├─ private redacted reports and access-token hashes
  └─ refresh and investigation job state
```

Use a modular monolith first: one frontend, one API, one database, and a worker/scheduler that can initially run as a separate process. Avoid microservices and a vector database until measured needs justify them. Use LangGraph when the investigation graph benefits from explicit durable state, parallel branches, and conditional execution; keep each agent/tool callable as an ordinary isolated function so it can be tested without LangGraph.

### Core contracts to define early

- **Submission:** optional URL, company name, recruiter name/email, job text, and supported document. Validate sizes, types, URLs, and field lengths. Never fetch arbitrary internal/private network addresses; defend against SSRF and unsafe redirects.
- **Evidence item:** stable ID, claim, observed value, source name and URL, collected-at time, freshness/expiry, provenance class (official/public/user-submitted/model inference), confidence, status (`supported`, `contradicted`, `unknown`, `unavailable`), and scoring contribution with reason.
- **Investigation:** ID, status, timestamps, progress stage, completed/failed checks, report link token, and retry policy. Frontend can poll status without an account.
- **Report:** score and calibrated band or insufficient-evidence status; opportunity/company summary; grouped findings; source evidence and freshness; model-derived observations labeled as such; limitations; recommendations; score-policy version; PDF representation.
- **Canonical entity cache:** normalized company/domain, source snapshots, source-specific freshness, public links, and verification history only. No user uploads, recruiter-sensitive details, or user report token.
- **Source adapter:** typed input/output, source-specific timeout, rate budget, attribution, cache policy, failure mapping, and an offline fixture for deterministic tests.

## 4. Weekly iteration roadmap

Each numbered iteration is intended to fit roughly one week, including learning, implementation, review, and integration. If a slice does not meet its exit check, finish it before starting another. Use real external data only in controlled live smoke tests; normal tests use clearly identified captured fixtures and never pretend fixture results are live.

### Track A — Product and website foundation

**A0. Product/visual brief** — Read the BRD and this plan together; record user journeys, page map, approved design direction, brand tokens, accessibility and motion rules. Deliver a short design decision record. Exit: both teammates agree on the first release's pages and the visual concept is approved or revised.

**A1. Repository and local development shell** — Add a clear frontend/backend project structure, README, environment-variable example with no secrets, formatting/lint/test commands, and local run instructions. Deliver an app that starts locally. Exit: a new contributor can follow the guide and see both services running.

**A2. Website frame and navigation** — Implement responsive header, footer, page container, typography/color/spacing tokens, and accessible focus states using the approved concept. Exit: usable desktop/mobile shell; no investigation backend required.

**A3. Scroll and 3D visual experiment** — Build the evidence-network hero visual as an isolated component with a static fallback, responsive behavior, reduced-motion support, and a performance budget. Exit: it can be enabled/disabled independently and does not block form usage.

**A4. Landing page content sections** — Implement problem explanation, “how it works,” safety/privacy explanation, limitations, and call to action. Avoid fabricated metrics or claims. Exit: complete informative page with working anchor navigation.

**A5. Investigation form, frontend only** — Create accessible URL/company/recruiter/job-description/file fields, client validation, file size/type feedback, consent/privacy copy, and a local-only submit state. Exit: can validate and produce a typed submission object without making API calls.

**A6. Progress and status experience** — Implement queued/running/partial/complete/failed states, progress labels, timeout messaging, and retry actions against a mock API contract. Exit: every status is understandable; no indefinite spinner.

**A7. Report page and PDF visual contract** — Build report layout using a clearly labeled fixture and sections for score, evidence, source links/freshness, uncertainty, recommendations, and PDF action. Exit: desktop/mobile report is readable and “unknown” is distinct from “safe.”

**A8. Motion/accessibility/performance pass** — Audit keyboard use, screen-reader labels, contrast, reduced motion, touch, low-end mobile behavior, image/3D loading, and Core Web Vitals baseline. Exit: no critical accessibility or responsive defects; animation has a functional fallback.

### Track B — API and reusable data foundation

**B1. API skeleton and health checks** — Create FastAPI app, versioned routes, settings, structured logs, health/readiness endpoints, and local run guide. Exit: clean start, typed error response, and health route documented.

**B2. Shared API schemas** — Define request/response types for submission, evidence, investigation, report, and errors; publish OpenAPI and a small frontend-generated or shared client. Exit: frontend and backend compile/validate against the same contract.

**B3. PostgreSQL local foundation** — Add local database configuration, migrations, connection lifecycle, and repository layer. Exit: migrations can create/drop test schema and app can connect without embedding secrets.

**B4. Canonical entities and normalization** — Model company/domain identity and normalization/deduplication rules; include India-specific company-name/domain variations carefully. Exit: equivalent inputs map consistently and ambiguous matches remain unresolved rather than merged.

**B5. Source snapshot/evidence persistence** — Store source checks and normalized evidence with provenance, freshness, and status. Exit: evidence can be inserted, retrieved, expired, and audited without report logic.

**B6. Private report storage/access** — Store redacted report payload and only a hash of the cryptographically random access token; implement read-only report lookup and revocation/expiry controls. Exit: guessing a token is infeasible, raw token is not persisted, and wrong/expired tokens do not reveal report existence.

**B7. Investigation job lifecycle** — Add create/status/result states, idempotency key, bounded retries, cancellation/deadline, and progress updates. Exit: frontend can poll a job through every state, including partial completion.

**B8. Basic queue/worker boundary** — Make investigations and scheduled refreshes callable as jobs; start with the simplest reliable queue/background process supported by the deployment. Exit: web request does not need to hold a long OSINT task open.

### Track C — Safe intake and rule-based MVP

**C1. URL safety and normalization** — Parse/normalize links, resolve redirects under restrictions, block loopback/private/reserved destinations and DNS rebinding cases, and cap response bytes/time. Exit: SSRF-focused tests cover malicious and valid URLs.

**C2. Text intake and deterministic rules** — Identify explicit fees, deposits, training charges, urgency, guaranteed selection, and sensitive-data requests using transparent rules with exact text spans. Exit: findings cite their matched text and include false-positive tests.

**C3. Initial evidence report builder** — Convert rule outputs and unavailable checks into the report schema. Exit: useful report renders without an AI provider.

**C4. Deterministic score policy** — Implement versioned transparent score contributions, caps, contradiction handling, insufficient evidence, and positive corroboration. Exit: table-driven tests cover score boundaries and explain every point/change.

**C5. Connect website form to submission API** — Replace the local-only submit with validated API call and job polling. Exit: a user can submit text/URL and see a real report produced by local deterministic checks.

**C6. Private report link and PDF export** — Generate a safe private URL and downloadable accessible PDF. Exit: PDF and web report agree and contain no hidden raw input or internal-only notes.

### Track D — Live public evidence adapters

**D1. RDAP adapter** — Use standardized domain registration data where available; interpret redaction as unknown, not suspicion. Exit: adapter handles supported and unsupported domains, errors, and cache freshness.

**D2. DNS/TLS/certificate transparency adapter** — Collect domain resolution, MX/NS and certificate timing/coverage as evidence. Exit: checks are bounded, explain meaning/limits, and do not infer company legitimacy from infrastructure alone.

**D3. Website fetch/metadata adapter** — Safely fetch public pages and extract title, contact links, redirects, and basic metadata. Exit: robots/terms-aware policy, strict SSRF protections, byte/time limits, and source attribution.

**D4. Official Indian company-source research** — Select and document specific lawful official registries/public sources and permitted access methods. Add one adapter at a time. Exit: source's coverage, terms, quotas, and missing-data behavior are documented and tested.

**D5. Public complaint/source adapter** — Add carefully selected sources for reported scams or complaints only after permitted access and attribution are established. Exit: source quality and possible false reports are shown; allegations are not treated as proof.

**D6. urlscan.io optional adapter** — Add opt-in scanning only after account/visibility and quota behavior are designed; parse current quota headers, avoid leaking submitted private URLs without clear user consent, and handle 429. Exit: source is disabled by default until policy/settings are explicit.

**D7. Source cache and freshness policy** — Add per-source TTL, stale-while-revalidate where appropriate, deduped concurrent lookups, and cache invalidation. Exit: repeat submissions reuse fresh public facts while report-specific evidence is independently recomputed.

### Track E — Documents and multimodal AI

**E1. Upload safety and ephemeral processing** — Enforce allowlisted formats, file/page/size limits, content sniffing, malware scanning where feasible, isolated temp storage, and automatic cleanup. Exit: success and failure paths both clean up uploads on schedule.

**E2. PDF text and metadata extraction** — Extract text and basic PDF metadata locally; OCR scanned pages using an open tool if needed. Exit: output includes page references and extraction quality; unsupported files receive clear feedback.

**E3. Document fact extraction contract** — Define typed facts such as company name, role, stipend, dates, contact, fees, and clauses; validate model/extractor output and attach page/quote evidence. Exit: invalid model output cannot alter the report silently.

**E4. Gemini multimodal adapter** — Add optional Gemini document/image analysis behind provider abstraction, spend/quota settings, redaction controls, timeout, and privacy disclosure. Do not assume a consumer Google AI Plus plan grants production API quota. Exit: structured extraction works when configured and a useful non-AI path works when unavailable.

**E5. AI provider health, budget, and fallback** — Add configurable provider selection, concurrency limits, retry/backoff, circuit breaker, usage tracking, and hard-disable switches. Groq may be a text-only fast fallback when its current account/model limits support it. Exit: exhaustion, 429, outage, and malformed response yield a complete partial report.

**E6. Communication analysis enrichment** — Use AI only for nuanced text patterns not handled by deterministic rules; require quoted spans and confidence with schema validation. Exit: model narrative cannot invent uncited facts or change score directly.

**E7. Image/audio/video scope decision** — Evaluate real student-use value, privacy, cost, accuracy, and available hardware for each modality. Build a modality only when a supported dependable detector and validation set exist. Synthetic-media findings remain tentative indicators. Exit: explicit go/no-go note per media type; no fake capability claims.

### Track F — Orchestration and intelligence quality

**F1. Standalone agent modules** — Package company, domain, communication, document, and source checks as independently testable functions using the common evidence contract. Exit: each agent runs with fixtures without web app or LLM credentials.

**F2. Parallel investigation orchestration** — Run independent checks concurrently with deadlines, cancellation, result aggregation, and per-source failure isolation. Exit: latency benchmark and partial results are tested.

**F3. LangGraph integration (only if justified)** — Represent the existing functions as graph nodes with explicit state and conditional paths. Preserve direct unit-test invocation. Exit: same output contract, graph state can resume if persistence is used, and measurable benefit over simpler orchestration is recorded.

**F4. Evidence deduplication and contradiction handling** — Merge repeated facts from distinct sources without losing provenance; retain disagreements and source timestamps. Exit: no hidden overwrite of contradictory evidence.

**F5. India internship evaluation corpus** — Build a consented/legally usable, manually labeled corpus of genuine, suspicious, and inconclusive examples. Remove personal data and record annotation rules. Exit: labels and evidence provenance reviewed by both teammates.

**F6. Score calibration and error review** — Measure false positives/negatives, source coverage, calibration, and subgroup/source bias; update versioned policy only from reviewed evidence. Exit: documented acceptance thresholds are selected before public claims.

**F7. Explainability and user comprehension** — Test whether students understand evidence vs inference, unknown vs verified, and recommended actions. Exit: revise report language where users mistake an indicator for proof.

### Track G — Rechecks, operations, and public beta

**G1. Monthly refresh scheduler** — Select canonical entities due for recheck, prioritize popular/recent entities, obey source budgets, and record failures. Exit: scheduled refresh is idempotent and can be safely paused/resumed.

**G2. Changed-fact timeline** — Record when public evidence changes, show last checked/current status, and invalidate expired facts. Exit: report never presents stale facts as current.

**G3. Retention and deletion jobs** — Enforce raw-file cleanup, report-link expiration/revocation policy, minimal log retention, and cleanup of orphaned jobs. Exit: operational tests prove deletion for completed and failed processing.

**G4. Abuse controls** — Add IP/request throttles, upload quotas, anti-automation protections, resource caps, and safe error responses without requiring student accounts. Exit: load/abuse tests cannot exhaust AI or source quotas through simple repeated requests.

**G5. Monitoring and operator runbook** — Track latency, queue age, cache hit rate, provider/source errors and quotas, cleanup failures, and monthly refresh status. Exit: documented actions exist for common failures and no sensitive payloads enter logs.

**G6. Deployable environments and backups** — Document local, preview, and public-beta configurations; secrets management; migrations; database backup/restore; domain/TLS; and spend alerts. Exit: restore drill and redeploy from clean checkout succeed.

**G7. Privacy, terms, and correction process** — Publish clear data flow/retention text, source attribution, limitations, contact/correction/takedown path, and handling for disputed reports. Obtain appropriate review before broad public release. Exit: users can understand what is collected, what is stored, and how to request correction/deletion.

**G8. Public beta release gate** — Run end-to-end, accessibility, mobile performance, security/SSRF, retention, quota, and corpus-quality reviews. Limit capacity and disclose beta constraints. Exit: no critical security/privacy issue, graceful degraded reports, functioning deletion, and evidence-backed product claims.

## 5. How to work as a pair

For each iteration, one teammate owns the implementation and writes a short “what I learned / how to run / how to test” note. The other independently checks the acceptance conditions, reviews the user-facing behavior, and tests the component at its boundary. Swap primary ownership on the next iteration. Both approve the API/schema contract before any cross-track integration.

Keep each part standalone through narrow interfaces: frontend pages use an API client and can run against a local fixture server; backend agents accept typed input and return typed evidence; source adapters can be tested with captured fixtures; report rendering consumes only the public report contract. A feature is integrated after its own checks pass, then an end-to-end check verifies the connection.

Maintain a decision log for changes to scope, data retention, external sources, score weights, AI providers, hosting, and visual direction. Update this plan when evidence or provider terms change. Never commit API keys, raw user documents, private report links/tokens, or scraped personal datasets.

## 6. Resource review and recommendations

The supplied tool list is a research menu, not a required shopping list. Avoid wiring every tool into every report; each integration adds latency, failures, privacy exposure, and maintenance.

| Resource from suggestions | Plan decision |
|---|---|
| LangGraph | Optional orchestration layer after the agent functions and contracts exist; parallel/durable workflow is a fit, but validate complexity against simpler async orchestration first. |
| Apify Domain Enricher | Optional paid enrichment. Do not make it necessary for core operation; use RDAP/DNS/TLS and public official sources first. |
| crt.sh / certificate transparency | Candidate low-cost domain evidence source, with conservative interpretation and bounded access. |
| WhatsMyName, Sherlock, Maigret | Not in default public recruiter checks. Username existence does not verify employment or identity, and broad probing is fragile/noisy. |
| Holehe | Exclude from public flow: it probes account recovery/registration behavior and can expose sensitive account associations; frequent site throttling also undermines reliability. |
| GHunt | Exclude from default public flow; account pivots and sensitive metadata are unnecessary for a student-facing first release. |
| CrossLinked | Exclude from automated public flow; search-result scraping does not verify current employment and creates terms/reliability concerns. |
| SpiderFoot | Useful for controlled, human-led analyst research later; too broad and operationally heavy as a per-submission default. |
| Maltego CE | Optional analyst/demo visualization after reliable normalized evidence exists; not part of the automated request path. |
| Stipple and OPSWAT | Do not depend on these without confirmed accessible pricing, data terms, and a tested student-project allowance. Local extraction first. |
| NVIDIA Synthetic Video Detector | Later experiment only: deployment has supported-GPU requirements or requires approved hosted access; never advertise video detection until accessible and validated. |
| urlscan.io | Optional, consented URL scan only. Read current quotas and scan visibility; respect returned action-specific rate-limit headers and never submit private URLs silently. |
| VirusTotal public API | Do not use in a public production workflow. Its documented public limit is 4 requests/minute (and 500/day) and it disallows commercial products/services/business workflows. It can be a manual developer research aid within its terms only. |
| Gemini API / Google AI Plus | Gemini API supports multimodal PDF understanding, but consumer subscription and API quotas are separate. Check actual API project quota/terms and data handling; core flow cannot require it. |
| Groq | Candidate text-only fast adapter. Limits depend on organization and model and can be reached by RPM, token, or daily caps; use adaptive throttles and a non-AI fallback. |
| Ollama Cloud | Optional provider experiment. Current cloud API exists, but quota/plan may change; never treat it as guaranteed free production capacity. Local Ollama is optional for development if the team's computers can run the model. |

Provider quotas/prices and external-source terms change. Re-verify official documentation and account-specific limits during the iteration that implements each adapter; do not rely on copied quota numbers as permanent configuration.

## 7. Testing and acceptance principles

- Unit tests cover each rule, evidence normalizer, cache policy, scoring contribution, token access behavior, and cleanup path.
- Adapter tests use captured fixtures and explicit unavailable/rate-limited/malformed responses. Live smoke tests are small, manually run, and quota-aware.
- Integration tests verify API contracts, migrations, job polling, cache reuse, report access, and PDF/report agreement.
- End-to-end tests cover URL/text submission, partial results, provider outage, report download/revisit, second-user cache reuse, monthly refresh, and raw upload deletion.
- Security checks cover SSRF, malicious redirects, DNS rebinding, unsafe file types, oversized input, secret leakage, report-token guessing, request abuse, and prompt injection in submitted content.
- Quality evaluation records false positive/negative rates, evidence/source coverage, factual citation support, median/p95 latency, provider availability, cache hits, and user comprehension. Set numeric release thresholds after the first labeled evaluation rather than inventing them now.

## 8. Initial implementation order

Start with A0 and A1: approve the visual/product brief and make the two services easy to run. Then build A2 and B1/B2 as separate slices, followed by A5 and C1/C2. The first end-to-end milestone is a user-submitted URL/text producing a deterministic, evidence-labeled report with unavailable checks clearly shown. Add live source adapters one at a time, then document AI, then scheduled refreshes and operations. Keep multimedia deepfake detection outside the release promise until there is a validated, affordable, maintainable path.

## 9. Source material

- Product requirements: `TrustIntern_BRD.md`.
- Tool research supplied for planning: `Suggestion_Tools_AI_Agents.md` (provided from Downloads; not copied into the repository by this plan).
- Visual direction: generated during the planning conversation; approval is an A0 activity before implementation.
- Official references checked during planning: [LangGraph documentation](https://langchain-ai.github.io/langgraph/reference/), [ICANN RDAP](https://www.icann.org/rdap/), [urlscan.io API](https://urlscan.io/docs/api/), [VirusTotal public API limits](https://docs.virustotal.com/v2.0/reference/public-vs-private-api), [Gemini document processing](https://ai.google.dev/gemini-api/docs/document-processing), [Gemini rate limits](https://ai.google.dev/gemini-api/docs/rate-limits), [Groq rate limits](https://console.groq.com/docs/rate-limits), [Ollama Cloud](https://docs.ollama.com/cloud), [NVIDIA detector support matrix](https://docs.nvidia.com/nim/maxine/synthetic-video-detector/latest/support-matrix.html), and [Render free-tier limits](https://render.com/docs/free).

These links support planning choices and should be rechecked when their associated integration is implemented.
