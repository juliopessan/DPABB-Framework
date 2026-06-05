# Discovery Report - T-Mobile Sentiment Analysis Prototype

**Version:** 1.0  
**Date:** 2026-06-01  
**Conducted by:** @discovery (Scout)  
**Status:** Approved

---

## 1. Executive Summary

T-Mobile needs a Microsoft-native proof of concept that converts Teams communication signals into aggregate sentiment and emerging-topic intelligence, with strict privacy controls and measurable readiness for enterprise rollout.

---

## 2. Context

| Field | Value |
|-------|-------|
| Company / Area | T-Mobile / HR, Internal Communications, Executive Leadership |
| Product / System | Teams Sentiment and Topic Intelligence PoC |
| Project Trigger | Limited visibility into workforce sentiment trends and delayed issue detection |
| Indicative Timeline | 12 weeks |
| Indicative Budget | USD 550K-650K build + about USD 15K/month run (PoC scale) |

---

## 3. Stakeholders

| Stakeholder | Role | Main Objective | Influence Level |
|-------------|------|----------------|-----------------|
| Executive Sponsor | Sponsor | Improve enterprise sentiment visibility and response time | High |
| HR Analytics Lead | Business Owner | Correlate sentiment shifts with people programs | High |
| Internal Comms Lead | Domain User | Track reaction to announcements and campaigns | Medium |
| Data Governance Lead | Compliance Owner | Enforce privacy, anonymization, and policy adherence | High |
| IT Platform Lead | Technology Owner | Ensure secure integration with M365 and Fabric | High |
| Executive Consumer Group (6-10 users) | Initial End Users | Use dashboards and natural language analytics | Medium |

---

## 4. Core Problem

### 4.1 Problem Statement

"As executive and people leaders, we lack a governed and timely way to detect sentiment and emerging topics across Teams communications, causing delayed action and reduced confidence in workforce signals."

### 4.2 Current Situation (As-Is)

**Communication Fragmentation:**
- Sentiment understanding depends on fragmented surveys, manual interpretation, and anecdotal feedback.
- Discussion patterns in Teams are not systematically detected, analyzed, or cataloged.
- Leadership reaction to campaigns, announcements, and initiatives is unknown until weeks after delivery.
- HR pulse surveys run quarterly, creating 90-day insight gaps.
- Internal comms team relies on manual social listening from channel summaries.

**Visibility Constraints:**
- Leadership has no single governed interface for trend exploration across org/time/topic dimensions.
- Existing dashboards do not correlate Teams sentiment with business events or program effectiveness.
- No capability to do ad-hoc natural language exploration (e.g., "What are people saying about the new remote work policy?").

**Governance and Risk:**
- No formal privacy controls on handling Teams communication data for analytics.
- Compliance and legal teams lack visibility into data usage and lineage.
- Retention and access controls are not enforced programmatically.

### 4.3 Impact If Unresolved

- **Financial:** Slower intervention on culture/productivity issues, estimated USD 50–100K/quarter in hidden productivity loss and disengagement costs.
- **Operational:** Delayed detection of workforce concerns, policy friction, or attrition signals (typical lag: 6–8 weeks).
- **Reputational:** Reduced confidence in leadership responsiveness; perception of disengagement from employee experience.
- **Regulatory:** Elevated risk around handling sensitive employee communication data without explicit privacy controls or audit trails.
- **Strategic:** Inability to measure campaign effectiveness or correlate business events with sentiment shifts.

---

## 5. Opportunities

| # | Opportunity | Estimated Value | Urgency |
|---|-------------|-----------------|---------|
| 1 | Aggregate sentiment trend monitoring by org/time/topic | High | High |
| 2 | Early signal detection of emerging concerns | High | High |
| 3 | Self-service analytics for executive users | Medium | High |
| 4 | Conversational analysis via natural language interface | Medium | Medium |
| 5 | Reusable enterprise data/AI foundation on Microsoft stack | High | Medium |

---

## 6. High-Level Requirements

| ID | Requirement | Type | Priority |
|----|-------------|------|----------|
| RQ-001 | Ingest Teams communication data through approved Microsoft Graph paths | Functional | Must |
| RQ-002 | Apply anonymization before analytics consumption | Functional | Must |
| RQ-003 | Generate sentiment scores and emerging-topic clusters | Functional | Must |
| RQ-004 | Expose aggregate insights in executive Power BI dashboards | Functional | Must |
| RQ-005 | Enable natural language analytics through Copilot agent experience | Functional | Should |
| RQ-006 | Maintain medallion data layers in Microsoft Fabric | Functional | Must |
| RQ-007 | Provide lineage and catalog metadata in Purview | Non-Functional | Should |
| RQ-008 | Enforce role-based access and auditability | Non-Functional | Must |
| RQ-009 | Support Dev/QA/Prod deployment model | Non-Functional | Must |
| RQ-010 | Produce pilot success criteria for enterprise go/no-go decision | Functional | Must |

---

## 7. Constraints

### 7.1 Technical

- Microsoft-only stack (Fabric, Azure AI, Power BI, Copilot, Purview).
- Data source is Teams via Graph API permissions and policy boundaries.
- No custom standalone frontend in PoC scope.

### 7.2 Regulatory / Compliance

- Personal data in chat requires strict minimization and anonymization controls.
- Potential re-identification risk must be mitigated and tested.
- Retention and access controls must align with enterprise policies.

### 7.3 Organizational

- Initial user group is limited to executive stakeholders (6-10 users).
- Cross-functional approval needed from Legal, Security, and HR governance.

### 7.4 Mandatory Integrations

| System | Integration Type | Current SLA |
|--------|------------------|-------------|
| Microsoft Graph API | Data extraction | M365 governed service SLA |
| Microsoft Fabric | ETL, lakehouse, semantic model | Enterprise managed |
| Power BI | Dashboard and semantic consumption | Enterprise managed |
| Copilot Portal | Conversational interface | Enterprise managed |
| Microsoft Purview | Catalog and lineage | Enterprise managed |

---

## 8. Assumptions

1. Required Graph permissions and compliance approvals are granted for PoC datasets.
2. Sentiment model quality can be improved through domain-specific tuning during pilot.
3. Initial demand is manageable with controlled query concurrency and usage guardrails.
4. Executive users accept aggregate outputs without user-level drill-through.

---

## 9. Identified Risks

| Risk | Probability | Impact | Initial Mitigation |
|------|-------------|--------|--------------------|
| Re-identification from text context | Medium | High | Multi-step anonymization, thresholding, and restricted views |
| Policy limitation on chat processing scope | Medium | High | Early legal/security review and explicit policy mapping |
| LLM run-cost growth at scale | High | High | Token controls, batching, caching, and tiered inference |
| Low trust in AI sentiment outputs | Medium | Medium | Human validation set and confidence thresholds |
| Delays in access approvals | Medium | Medium | Week 1 governance workstream and escalation path |

---

## 10. Success Metrics

| Metric | Current Baseline | Target | Timeline |
|--------|------------------|--------|----------|
| Teams data ingestion success rate | Not measured | >= 99% daily ingestion success | PoC end |
| Sentiment model F1 on curated validation set | Not measured | >= 0.78 | PoC end |
| Topic relevance score (expert review) | Not measured | >= 80% accepted topics | PoC end |
| Dashboard task completion for executive users | Not measured | >= 85% completion in usability sessions | PoC end |
| Avg natural language response latency | Not measured | <= 8 seconds p95 | PoC end |
| Privacy control audit findings | N/A | 0 high-severity findings | PoC end |

---

## 11. Current Process and Pain Points (AS-IS Detailed)

### 11.1 Sentiment Measurement Today
- **Pulse surveys:** Quarterly, 10–15 question templates, 30–40% response rates.
- **Manual channel reviews:** HR team spends ~10 hrs/week skimming key channels for sentiment signals.
- **Anecdotal feedback:** Leadership comments from 1:1s, all-hands Q&A, town halls (unstructured).
- **External tools:** Limited tooling; some teams use Slack analytics, but Teams integration is absent.
- **Latency:** Insights typically surface 4–8 weeks after sentiment shift occurs.

### 11.2 Topic Detection
- No systematic process for identifying emerging discussion topics in Teams.
- HR and internal comms teams manually tag themes (e.g., "remote work concerns," "benefits question").
- No correlation with business events or program announcements.
- No capability to detect unexpected sentiment drivers.

### 11.3 Current System Constraints
- Teams data is locked in the platform; no export to centralized analytics.
- Power BI dashboards exist for other HR metrics but do not include Teams data.
- Purview is not used for Teams communication data governance.
- No federated consent model for privacy-preserving analytics.

---

## 12. Future State Vision (Phase 1 TO-BE)

### 12.1 Sentiment Visibility
- **Real-time monitoring:** Daily updated sentiment scores aggregated by org/time/topic.
- **Confidence indicators:** AI outputs include confidence bands; low-confidence results are flagged.
- **Rapid insight access:** Executives access trends via 2-minute dashboard view or conversational query.
- **Event correlation:** Sentiment trends are overlaid with business events (product launches, policy changes, external news) to identify drivers.
- **Latency:** Insights available within 24 hours of communication (vs. 4–8 weeks today).

### 12.2 Topic Intelligence
- **Automated clustering:** Topics detected and ranked weekly by frequency and velocity (emerging topics prioritized).
- **Governance transparency:** Each topic tagged with source channels, anonymized contributor count, and confidence score.
- **Trend analysis:** Topic trends over time; ability to drill into representative messages (anonymized).
- **Conversational access:** "What topics are rising fastest this month?" or "Show me sentiment on [specific topic]." 

### 12.3 Governance and Privacy
- **End-to-end lineage:** Purview catalog shows data source → ingestion → transformation → semantic model → dashboard/agent.
- **Privacy by design:** Anonymization applied at ingestion; no personal data flows to analytics.
- **Audit trail:** All queries, exports, and access logged with user/time/scope.
- **Retention management:** Automated purge of message details after N days; aggregates retained longer.

---

## 13. Hypotheses and Working Assumptions

| # | Hypothesis | Status | Evidence | Risk if Wrong |
|---|-----------|--------|----------|---------------|
| H-001 | Sentiment model can achieve >= 0.78 F1 on T-Mobile communication corpus | Likely | Strong baseline from general English sentiment (0.82–0.88); domain tuning needed | Medium — may require extended training cycle |
| H-002 | Topic detection will surface 8–15 actionable topics per week | Likely | Typical enterprise Teams volume and diversity | Low |
| H-003 | Executive users will adopt dashboards with >= 60% monthly DAU | Likely | Sponsor committed; usage tied to people programs | Medium — adoption depends on integration timing |
| H-004 | Privacy controls can be automated without manual governance gates | Inconclusive | Depends on Graph API permission scopes and legal interpretation | High — if false, add 2–3 week governance phase |
| H-005 | Copilot agent will handle 70%+ of self-serve queries without fallback | Likely | Semantic model quality is strong predictor | Medium — LLM hallucination risk on edge cases |

---

## 14. Compliance and Regulation

### 14.1 Industry and Data Sensitivity Context
- **Industry:** Telecommunications (regulated, sensitive employee data).
- **Data classification:** Employee communications treated as personal data per GDPR/CCPA/State Privacy Laws.
- **Jurisdiction:** US-based primarily, but workforce spans multiple regions.
- **Sensitivity:** Message content includes opinions on policies, leadership, and workplace concerns (high reputational/legal sensitivity).

### 14.2 Applicable Compliance Themes

| Theme | Control Requirement | Status |
|-------|-------------------|--------|
| **Confidentiality** | Message content must not be exposed to unauthorized users; aggregation only. | ✅ In scope |
| **Access Control** | Executive dashboard access restricted to approved HR/leadership group; audit trail maintained. | ✅ In scope |
| **Auditability** | All data flows, queries, and exports logged with user/timestamp/scope. | ✅ In scope |
| **Retention** | Message text retained <= 90 days; aggregates retained longer per legal guidance. | 🟡 Assume 90 days; legal validation required |
| **Explainability** | Sentiment and topic scoring must be defensible; confidence indicators provided. | ✅ In scope |
| **Anti-Reidentification** | Multi-step anonymization to prevent user identity inference from message context. | 🟡 Requires independent privacy audit |

### 14.3 Compliance Status

**Confirmed Constraints:**
- Teams data falls under employee privacy and must have explicit governance.
- Scope limited to small executive group (6–10 users) to minimize exposure in PoC.
- Legal/Privacy sign-off required before production deployment.

**Assumptions Requiring Validation:**
- Graph API scopes permit sentiment analysis use case (confirmation needed week 1).
- 90-day retention window is acceptable per T-Mobile data governance policy.
- Anonymization technique (hashing + suppression) meets enterprise privacy standards.

---

## 15. Out of Scope (Initial)

- Individual employee-level sentiment surveillance or employee ranking/scoring.
- Full enterprise rollout to all business units; PoC limited to executives.
- Non-Microsoft data sources beyond approved Teams scope (Slack, email, etc.).
- Bespoke custom web frontend outside M365 Copilot portal.
- Real-time alerting or autonomous workflow triggering (Phase 2+ candidate).
- Integration with external sentiment vendors or APIs.

---

## 16. Next Steps

- [x] @prd - Generate PRD from this discovery baseline.
- [x] Confirm data governance control set with security/legal (Week 1 critical path).
- [x] Validate PoC KPI thresholds with executive sponsor.
- [ ] Legal/Privacy review of Graph API scopes and anonymization approach.
- [ ] Secure executive sign-off on governance and privacy controls.

---

## 17. Estimation Guidance for ROM

### 17.1 Complexity and Risks

| Dimension | Rating | Justification |
|-----------|--------|---------------|
| **Scope** | Medium | 4 discrete workstreams (ingestion, anonymization, AI, consumption); well-defined boundaries |
| **Integration** | High | Graph API + Fabric + Power BI + Copilot + Purview = 5 critical integrations |
| **Data Quality** | Medium | Teams chat is semi-structured; standardization effort required |
| **AI/ML** | High | Custom sentiment tuning + topic modeling + confidence scoring |
| **Governance** | High | Privacy-by-design + anonymization validation + compliance audit |
| **Timeline Pressure** | High | 12-week hard deadline; critical path: governance approvals |

### 17.2 Estimation Posture
- Conservative capacity (not heroic delivery).
- Assumes 1–2 week governance approval delays.
- Includes 1-week buffer for sentiment model quality tuning.
- Team ramp-up and knowledge transfer included.
- Assumes existing Fabric/Power BI tenant is ready.

---

## Version History

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 | 2026-06-01 | @discovery | Initial approved version with expanded AS-IS, TO-BE, hypotheses, and compliance detail |
