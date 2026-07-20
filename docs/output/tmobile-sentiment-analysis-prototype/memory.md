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
- **Problem:** No governed, timely visibility into aggregate workforce sentiment and emerging discussion topics from Teams communications. Current latency: 4-8 weeks; no systematic topic detection; fragmented pulse surveys (quarterly, 30-40% response rate).
- **Proposed solution:** Microsoft-native Fabric + Azure AI + Power BI + Copilot analytics pipeline with governance controls.
- **Key stakeholders:** Executive sponsor, HR analytics lead, data platform lead, compliance/privacy, business unit leaders.
- **Timeline and budget:** 12-week delivery, USD 550K-650K build range; USD 15K/month OpEx; estimated 24-month ROI 30%, payback 17 months.

### AS-IS State (Expanded)
- **Sentiment measurement:** Quarterly pulse surveys (low response), manual channel reviews (~10 hrs/week), anecdotal feedback from 1:1s.
- **Topic detection:** Manual tagging by HR; no correlation with business events; no detection of unexpected drivers.
- **Data access:** Teams data locked in platform; no centralized analytics; no Purview governance.
- **Latency impact:** Insights surface 4-8 weeks post-shift; leaders miss intervention windows.

### TO-BE Vision (Expanded)
- **Real-time sentiment:** Daily updated scores by org/time/topic; 24h latency vs. 4-8w today.
- **Automated topics:** Weekly detection + ranking; velocity indicators; drill to anonymized sources.
- **Governance:** Full lineage in Purview; audit trail; privacy-by-design anonymization; retention policies automated.
- **Access:** 2-min executive dashboard view OR conversational agent queries ("Show rising topics" / "Sentiment pre/post [event]").

### Fixed Constraints (Non-Negotiable)
- **Technology:** Microsoft-only platform components (Fabric, Azure AI, Power BI, Copilot, Purview, Entra).
- **Regulatory:** Privacy-by-design controls, multi-step anonymization, anti re-identification safeguards, audit trail mandatory.
- **Organizational:** Initial pilot for 6-10 executive users; NO individual-level reporting; NO punitive analytics; NO autonomous escalation (Phase 1).
- **Data:** Teams only; 90-day message retention assumed (legal validation Week 1); aggregates retained longer.

### Decisions Taken (Do Not Reopen)

| Decision | Phase | Reason | Rejected Alternatives |
|----------|------|--------|-----------------------|
| Use Fabric medallion architecture | Architect | Governed, scalable analytics pattern with data contracts | Flat ETL model; direct Graph→Power BI |
| Use aggregate-level outputs only in PoC | Discovery/Architect | Privacy and trust controls for pilot; no individual drill-through | Individual-level reporting; employee-ranking |
| Use Copilot portal instead of custom frontend | Discovery/Architect | Faster delivery, lower complexity, M365 integration | Custom web app; Slack integration |
| Use hybrid sentiment/topic AI services | Architect | Balanced deterministic (Azure Language) + generative (OpenAI) | Single Azure Language only; third-party APIs |
| 90-day message retention window | Discovery | Balance insight freshness vs. privacy risk | Longer retention; real-time streaming |
| Daily batch ingestion (not streaming) | Architect | Simpler operations, cost control, sufficient for PoC use cases | Real-time ingestion; hourly batching |
| Confidence bands on all sentiment outputs | PRD | Avoid false-positive decision-making in pilot; human-in-loop | Crisp binary sentiment; no confidence |
| Multi-turn Copilot agent (Should-Have) | PRD | Rich exploration experience; enables discovery of cross-domain insights | Single-turn Q&A only |
| Privacy audit gate before go/no-go | Business Case | Non-negotiable for communication data; external auditor recommended | Self-assessment only |
| Payback at 17 months (with conditions) | Business Case | ROI 30% achievable with adoption targets and cost controls; conditions: model quality >= 0.78 F1, compliance audit 0 high findings | Faster payback implies reduced scope or accepting privacy risk |

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
| 1 | What is the target timeline? | 12-week delivery model (4 sprints) | Discovery |
| 2 | What is the initial user group size? | 6-10 executive users; executives, HR analytics, 1 data governance lead | Discovery |
| 3 | Is custom frontend in scope? | No, use Power BI dashboards + Copilot portal conversational AI | Discovery |
| 4 | Is individual employee-level output in scope? | No, strictly aggregate level; no ranking, no drill-to-individual | Discovery |
| 5 | What build budget range is assumed? | USD 550K-650K (midpoint USD 600K); plus USD 15K/month OpEx | Discovery |
| 6 | Can this data be used for employee performance/discipline? | No, explicitly out of scope; cannot use outputs for punitive actions | PRD |
| 7 | What is acceptable sentiment model quality? | F1 >= 0.78 on curated T-Mobile validation set; confidence labeling mandatory | PRD |
| 8 | How should low-confidence predictions be handled? | Flag with disclaimer; propose alternative query; fallback to human review | PRD |
| 9 | What retention policy for message data? | 90 days for raw message text; aggregates retained longer; legal validation Week 1 | PRD/Discovery |
| 10 | Is real-time alerting in scope for Phase 1? | No, batch daily ingestion only; Phase 2+ candidate for anomaly detection | PRD |
| 11 | How many topics should we detect per week? | Target 8-15 actionable topics; hypothesis to validate in model tuning | Discovery |
| 12 | What if Graph API scopes don't permit use case? | Fallback: reduced scope or alternative data source; legal gate Week 1 | Discovery/Risk |
| 13 | Should topic labels be auto-generated or human-curated? | Hybrid: auto-generated clusters, human validation in UAT, confidence tagged | PRD |
| 14 | Can executives query individual message content? | No; only anonymized topic themes and aggregate sentiment trends | PRD/Constraints |

---

## Key Design Principles (PRD)

- **Privacy by design:** Anonymization mandatory, not optional; all data handling assumes high sensitivity.
- **Confidence-first:** All outputs include uncertainty bands; low-confidence flagged; users never misled.
- **Human-in-the-loop:** AI scores advisory; human validation required for governance/high-stakes decisions.
- **Bias vigilance:** Sentiment model tested for demographic/topic bias; fallback to human review if detected.
- **Microsoft-native:** 100% Microsoft stack; no third-party NLP, no custom ML frameworks beyond Azure AI.

## Project Patterns Identified

- **Privacy-first scope definition:** Early governance involvement is mandatory for communication-data solutions. Defining anonymization strategy and aggregation level at Discovery phase prevents rework.
- **Hybrid AI scoring:** Combining deterministic (Azure Language) + generative (OpenAI) improves coverage and interpretability vs. single-model approaches.
- **Cost controls in PoC:** Token controls, caching, and batching must be designed in pilot stage to enable safe scale-up. LLM cost escalation is high-probability/high-impact risk.
- **Confidence and guardrails over accuracy alone:** For PoCs with high-stakes decisions (people/HR), confidence labeling and hallucination detection matter more than marginal F1 improvements.
- **Compliance as critical path:** For communication-data products, governance approvals are blocking issue; assume 1-2 week delay and escalate to sponsor immediately.

## Open Items for Week 1 (Critical Path)

- [ ] Legal/Privacy sign-off on Graph API scopes and use case interpretation.
- [ ] Validation of 90-day message retention policy vs. enterprise data governance.
- [ ] Confirmation that anonymization technique (hashing + suppression) meets privacy audit standards.
- [ ] Executive sponsor approval of USD 600K budget and 17-month payback model.
- [ ] Confirm 6-10 executive user list and access provisioning plan.

---

## Memory Changelog

| Date | Phase | Change |
|------|-------|--------|
| 2026-06-01 | Discovery | Memory created with baseline context |
| 2026-06-01 | PRD | FR/NFR and traceability summary added |
| 2026-06-01 | Architect | Architecture decisions and NFR mapping added |
| 2026-06-01 | Backlog | Point model and sprint summary added |
| 2026-06-01 | Business Case | Financial decision and conditions added; phase set to done |
| 2026-06-01 v2 | All phases | Expanded sections: AS-IS/TO-BE, hypotheses, compliance, roadmap, risks, design principles, open items |
