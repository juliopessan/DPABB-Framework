# Memory - tmobile-sentiment-analysis-prototype

> Auto-updated project context for full DPABB pipeline execution.

---

## Meta

| Field | Value |
|-------|-------|
| Project | tmobile-sentiment-analysis-prototype |
| Pipeline start | 2026-06-01 |
| Last update | 2026-06-01 |
| Current phase | done |
| Estimated token savings | ~16,000 tokens vs cold pipeline |

---

## Accumulated Context

### About the Project
- **Problem:** No governed, timely visibility into aggregate workforce sentiment and emerging discussion topics from Teams communications.
- **Proposed solution:** Microsoft-native Fabric + Azure AI + Power BI + Copilot analytics pipeline with governance controls.
- **Key stakeholders:** Executive sponsor, HR analytics lead, data platform lead, compliance/privacy, business unit leaders.
- **Timeline and budget:** 12-week delivery, USD 550K-650K build range.

### Fixed Constraints (Non-Negotiable)
- **Technology:** Microsoft-only platform components.
- **Regulatory:** Privacy-by-design controls and anti re-identification safeguards required.
- **Organizational:** Initial pilot for 6-10 executive users.

### Decisions Taken (Do Not Reopen)

| Decision | Phase | Reason | Rejected Alternatives |
|----------|------|--------|-----------------------|
| Use Fabric medallion architecture | Architect | Governed, scalable analytics pattern | Flat ETL model |
| Use aggregate-level outputs only in PoC | Discovery/Architect | Privacy and trust controls for pilot | Individual-level reporting |
| Use Copilot portal instead of custom frontend | Discovery/Architect | Faster delivery and lower complexity | Custom standalone UI |
| Use hybrid sentiment/topic AI services | Architect | Balanced deterministic and generative capabilities | Single-model path |

---

## Produced Artifacts

| Artifact | Path | Status | Version |
|----------|------|--------|---------|
| discovery-report.md | docs/output/tmobile-sentiment-analysis-prototype/discovery-report.md | Approved | 1.0 |
| prd.md | docs/output/tmobile-sentiment-analysis-prototype/prd.md | Approved | 1.0 |
| architecture.md | docs/output/tmobile-sentiment-analysis-prototype/architecture.md | Approved | 1.0 |
| backlog.md | docs/output/tmobile-sentiment-analysis-prototype/backlog.md | Approved | 1.0 |
| business-case.md | docs/output/tmobile-sentiment-analysis-prototype/business-case.md | Approved | 1.0 |

---

## Handoffs Between Phases

### Discovery -> PRD

```text
Business goal confirmed: detect aggregate sentiment and emerging topics from Teams data for executive decision support.
Must constraints: Microsoft stack only, strict privacy controls, no custom frontend in PoC.
Success gates: ingestion reliability, model quality, adoption, compliance.
```

### PRD -> Architect

```text
Critical NFRs to enforce:
- NFR-003 security via Entra RBAC and least privilege
- NFR-004 privacy via de-identification and suppression
- NFR-005 auditability via run logs and lineage
- NFR-008 responsible AI via confidence controls
```

### Architect -> Backlog

```text
Primary components: Graph ingestion, Fabric medallion, AI Language/OpenAI scoring, semantic model, Power BI, Copilot agents, Purview.
Complexity class: COMPLEX.
Mandatory technical stories in Sprint 1: TECH-001 CI/CD, TECH-002 identity and secrets.
```

### Backlog -> Business Case

```text
Total points: 107.
Sprint 1 target: 34 points (needs scope split from 40-point draft set).
Estimated implementation: 4 sprints over 12 weeks.
```

---

## Answered Questions (Do Not Ask Again)

| # | Question | Answer | Phase |
|---|----------|--------|-------|
| 1 | What is the target timeline? | 12-week delivery model | Discovery |
| 2 | What is the initial user group size? | 6-10 executive users | Discovery |
| 3 | Is custom frontend in scope? | No, use Power BI and Copilot portal | Discovery |
| 4 | Is individual employee-level output in scope? | No, aggregate level only | Discovery |
| 5 | What build budget range is assumed? | USD 550K-650K | Discovery |

---

## Project Patterns Identified

- Early governance involvement is mandatory for communication-data solutions.
- Hybrid AI scoring improves coverage and interpretability.
- Cost controls must be designed in pilot stage, not after scale.

---

## Memory Changelog

| Date | Phase | Change |
|------|-------|--------|
| 2026-06-01 | Discovery | Memory created with baseline context |
| 2026-06-01 | PRD | FR/NFR and traceability summary added |
| 2026-06-01 | Architect | Architecture decisions and NFR mapping added |
| 2026-06-01 | Backlog | Point model and sprint summary added |
| 2026-06-01 | Business Case | Financial decision and conditions added; phase set to done |
