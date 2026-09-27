# TrustIntern

**Check the evidence before you trust an internship offer.**

TrustIntern is a planned, India-first public service to help students assess internship and entry-level job opportunities. It will bring together relevant public information and the details a student provides, then explain what could be verified, what looks inconsistent, and what still needs checking.

TrustIntern is being built as a student project by a two-person team. The repository is currently in its planning and foundation stage; the complete staged roadmap is in [plan.md](./plan.md), and the product requirements are in [TrustIntern_BRD.md](./TrustIntern_BRD.md).

## Why this project exists

Students can receive convincing offers through job sites, email, messaging apps, social networks, and college groups. A polished website or offer letter alone does not establish that a recruiter represents the claimed company. At the same time, a missing online record does not prove that an opportunity is fraudulent.

TrustIntern aims to make basic verification easier by presenting multiple signals together with their sources and limitations. Its goal is to help a student decide what to verify next—not to make an unsupported accusation or guarantee that an offer is safe.

## How it is intended to work

1. **Submit an opportunity.** Provide any available job or internship URL, company and recruiter details, offer text, and optionally an offer document.
2. **Check independent evidence.** TrustIntern will inspect suitable public sources and compare relevant details, such as the claimed company domain, public company information, contact details, and payment requests.
3. **Review an explainable report.** The report will show the assessment, evidence and source links, when each fact was checked, uncertainty, unavailable checks, and practical next steps.
4. **Return to the report.** No student account is planned. A completed report will have an unguessable private link and a downloadable redacted PDF.

Independent checks should run concurrently where practical. If a source or AI provider is slow, unavailable, or over quota, the system should return a clearly marked partial report instead of leaving the student waiting indefinitely.

## What a report should communicate

- A calibrated risk assessment and an **insufficient evidence** state when the available checks are not enough.
- Findings linked to the source, observation time, freshness, and confidence where it can be assessed.
- A clear distinction between information verified from a source, details supplied by the user, and AI-generated observations.
- Any contradictions, missing information, or checks that could not be completed.
- Actionable suggestions, such as contacting the company through a channel found independently of the offer.

Risk scoring is planned to use transparent, versioned rules. AI may help extract or summarize information, but it should not decide by itself that a person or opportunity is fraudulent. Scores and thresholds must be evaluated against labeled examples before performance claims are made.

## Privacy and responsible use

TrustIntern is designed to minimize collection of personal information and to avoid student accounts. Reusable public company and domain facts may be cached to make later checks faster; those facts must remain separate from a student's private report and submitted documents. Original uploads are intended to be processed temporarily and deleted after analysis, with retention and deletion controls implemented before public launch.

The public workflow will prioritize lawful public sources and documented APIs. It is not intended to access private accounts, bypass access controls, or use password-reset flows to enumerate someone's accounts. Reports are decision support, not legal findings or proof of wrongdoing. Source limitations, false positives, and stale or missing information must be visible to users.

## Planned technical shape

The initial design is a modular application rather than a collection of independently deployed services:

```text
Next.js website
  submission form · investigation progress · report and PDF
              │ typed API
              ▼
FastAPI application and worker
  safe intake · public-source adapters · evidence normalization
  bounded orchestration · explainable scoring · monthly refresh
              │
              ▼
PostgreSQL
  reusable public entity/source facts · private redacted reports · job state
```

Individual checks should remain small, typed, and testable on their own. LangGraph is a candidate for coordinating stateful or parallel investigations after those checks and their shared evidence format exist; it is not a prerequisite for the first working version. A vector database and paid enrichment services are also deferred until a measured need justifies them.

The website has its own workstream. The current visual direction is a student-friendly security experience with a dark navy foundation, restrained cyan/indigo/amber accents, an evidence-network visual, and a clear investigation/report journey. Scroll and 3D effects are exploratory design elements: they must be approved, accessible, responsive, respect reduced-motion preferences, and never obstruct the core task.

## Development approach

The project will be built in small, roughly weekly iterations by two teammates. Each slice should:

- Teach one part of the system and produce a usable standalone component.
- Have a narrow interface so it can be reviewed before integration.
- Include its own learning notes and proportionate tests.
- Integrate only after its behavior and contract are understood.

The roadmap breaks the work into website, API/data, safe intake, public evidence sources, documents and AI, orchestration and quality, then refreshes and public-beta operations. See [plan.md](./plan.md) for iteration goals, exit checks, resource decisions, testing principles, and the source review.

## Current project status

- [x] Product requirements documented in [TrustIntern_BRD.md](./TrustIntern_BRD.md).
- [x] Resource options reviewed and a staged implementation roadmap documented in [plan.md](./plan.md).
- [ ] Visual direction and page behavior approved by both teammates.
- [ ] Local development foundation and website shell implemented.
- [ ] First end-to-end investigation using real, bounded public checks implemented.
- [ ] Privacy, security, evaluation, and deployment gates completed for a public beta.

There is no working verification service or production accuracy claim yet. Sample companies, scores, and findings should not be presented as live results; early UI work may use fixtures that are plainly identified as examples.

## Repository guide

| File or directory | Purpose |
|---|---|
| `README.md` | Project overview and current status |
| `TrustIntern_BRD.md` | Business and product requirements |
| `plan.md` | Learning-oriented iteration roadmap and architecture decisions |
| `.codex/` | Codex project hooks configuration |
| `.entire/` | Entire CLI project configuration; generated logs and temporary data are ignored |

## First milestone

The first meaningful end-to-end milestone is a student submitting an opportunity URL or text and receiving a useful report from deterministic checks, with evidence provenance and unavailable checks shown honestly. Live source adapters, document understanding, optional AI analysis, and monthly refreshes can then be added one at a time without making the core report depend on a single provider.
