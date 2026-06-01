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

- Sentiment understanding depends on fragmented surveys and manual interpretation.
- Discussion patterns are not systematically detected from collaboration channels.
- Leadership has no single governed interface for trend exploration.

### 4.3 Impact If Unresolved

- **Financial:** slower intervention on culture/productivity issues, higher hidden productivity loss.
- **Operational:** delayed detection of workforce concerns and topic escalation.
- **Reputational:** reduced confidence in leadership responsiveness.
- **Regulatory:** elevated risk around handling sensitive employee communication data.

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

## 11. Out of Scope (Initial)

- Individual employee-level sentiment surveillance.
- Full enterprise rollout to all business units in this phase.
- Non-Microsoft data sources beyond approved Teams scope.
- Bespoke custom web frontend outside M365 Copilot portal.

---

## 12. Next Steps

- [x] @prd - Generate PRD from this discovery baseline.
- [x] Confirm data governance control set with security/legal.
- [x] Validate PoC KPI thresholds with executive sponsor.

---

## Version History

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 | 2026-06-01 | @discovery | Initial approved version |
