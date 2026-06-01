# PRD - T-Mobile Sentiment Analysis Prototype

**Version:** 1.0  
**Date:** 2026-06-01  
**Author:** @prd (Priya)  
**Discovery:** discovery-report.md  
**Status:** Approved

---

## 1. Overview

This product delivers a governed Microsoft-native analytics capability that ingests Teams communication data, computes aggregate sentiment and emerging topics, and enables executive exploration through dashboards and conversational queries.

**Tagline:** "Teams Sentiment Intelligence enables executive leaders to detect workforce signals early through governed dashboards and natural language exploration."

---

## 2. Problem and Opportunity

T-Mobile leadership lacks timely, governed visibility into enterprise sentiment and discussion trends. The opportunity is to validate, within 12 weeks, a scalable architecture that combines Fabric analytics and AI-assisted exploration while preserving privacy and compliance.

---

## 3. Personas

### Persona 1: Executive Decision Maker
| Field | Detail |
|-------|--------|
| Profile | Senior leadership user consuming aggregate people insights |
| Main objective | Identify sentiment shifts and emerging business topics quickly |
| Current pain | Limited confidence in current manual pulse mechanisms |
| Technical level | Basic |
| Usage frequency | 2-3 times per week |

### Persona 2: HR Analytics Lead
| Field | Detail |
|-------|--------|
| Profile | Analytics lead supporting workforce and engagement initiatives |
| Main objective | Correlate sentiment trends with initiatives and events |
| Current pain | Fragmented data and no conversational analytics layer |
| Technical level | Intermediate |
| Usage frequency | Daily |

### Persona 3: Data Governance Lead
| Field | Detail |
|-------|--------|
| Profile | Compliance owner for privacy and data handling |
| Main objective | Ensure anonymization, retention, and auditable controls |
| Current pain | High risk if data controls are not explicit and testable |
| Technical level | Advanced |
| Usage frequency | Weekly/governance checkpoints |

---

## 4. User Journeys

### Journey 1: Executive trend exploration (Persona: Executive)

[Open dashboard] -> [Review sentiment trend by period] -> [Drill into topic cluster summary] -> [Ask natural language follow-up] -> [Capture action insight]

**Happy Path:** User gets trend + topic context in under 2 minutes and exports insights.  
**Edge Cases:** Missing period data, low-confidence sentiment slice.  
**Errors and Fallbacks:** System shows confidence warning and proposes alternative slice.

### Journey 2: HR deep-dive analysis (Persona: HR Analytics Lead)

[Select business segment] -> [Compare pre/post event sentiment] -> [Inspect topic drivers] -> [Validate with conversational query] -> [Share summary]

---

## 5. Functional Requirements

| ID | Description | Acceptance Criteria | Priority | Origin |
|----|-------------|---------------------|----------|--------|
| FR-001 | Ingest approved Teams data via Graph API pipeline | Ingestion job runs daily and stores records with source/time metadata | Must | RQ-001 |
| FR-002 | Apply anonymization and policy tagging in ingestion/transformation | All records consumed by BI/agents contain anonymized identifiers and policy tags | Must | RQ-002 |
| FR-003 | Compute sentiment scores for normalized conversation units | Sentiment output generated for >= 95% processable records | Must | RQ-003 |
| FR-004 | Detect and score emerging discussion topics | Topic model produces ranked clusters per analysis window | Must | RQ-003 |
| FR-005 | Publish executive dashboard in Power BI | Dashboard shows sentiment trend, topic trend, and confidence indicators | Must | RQ-004 |
| FR-006 | Provide conversational analytics agent in Copilot | User can ask NL questions and receive grounded aggregate responses | Should | RQ-005 |
| FR-007 | Implement semantic model in Fabric as single source of truth | Dashboard and agents consume the same governed semantic model | Must | RQ-006 |
| FR-008 | Capture lineage/catalog metadata in Purview | Core tables and views are visible with lineage links | Should | RQ-007 |
| FR-009 | Provide deployment automation for Dev/QA/Prod | Pipeline supports promotion and config separation by environment | Must | RQ-009 |
| FR-010 | Produce pilot scorecard for go/no-go decision | KPI dashboard includes technical, model, governance, and adoption metrics | Must | RQ-010 |

### FR-001 Detail
**Description:** Acquire Teams communication data through approved Graph endpoints.  
**Acceptance Criteria:**
- Given approved credentials and scopes, when the scheduled run starts, then data is ingested into Bronze with ingestion timestamp and source metadata.
- Given transient Graph/API failure, when retry policy triggers, then failure is logged and retried up to policy limit with alerting.

**Priority:** Must Have  
**Origin:** RQ-001

### FR-006 Detail
**Description:** Enable conversational exploration for executive and HR personas.  
**Acceptance Criteria:**
- Given an authorized user query, when the Copilot agent executes, then it returns aggregate sentiment/topic insights grounded in semantic model data.
- Given a low-confidence or out-of-scope query, when the agent responds, then it includes a confidence disclaimer and safe fallback suggestion.

**Priority:** Should Have  
**Origin:** RQ-005

---

## 6. Non-Functional Requirements

| ID | Category | Requirement | Metric | Origin |
|----|----------|-------------|--------|--------|
| NFR-001 | Performance | Dashboard interactivity for executive usage | <= 5s p95 for key visual interactions | RQ-004 |
| NFR-002 | Performance | Conversational response latency | <= 8s p95 for standard aggregate queries | RQ-005 |
| NFR-003 | Security | Access control and least privilege | Entra RBAC enforced; 0 critical IAM audit findings | RQ-008 |
| NFR-004 | Privacy | De-identification and anti re-identification controls | 0 high-severity privacy findings in PoC audit | RQ-002 |
| NFR-005 | Availability | Data pipeline reliability | >= 99% successful scheduled runs | RQ-001 |
| NFR-006 | Scalability | Pilot-to-scale readiness | Capacity model produced for low/med/high usage scenarios | RQ-010 |
| NFR-007 | Governance | End-to-end lineage for curated datasets | 100% Gold entities cataloged with lineage in Purview | RQ-007 |
| NFR-008 | Operability | Monitoring and alerting coverage | Alerts for ingestion failures, model exceptions, and query SLA breaches | RQ-009 |

---

## 7. Constraints

### 7.1 Technical
- Microsoft-native implementation only.
- Fabric medallion + semantic model architecture is mandatory.
- Copilot portal is the consumption channel for conversational layer.

### 7.2 Regulatory
- Processing boundaries must align with internal policy and legal interpretation of Teams data usage.
- Privacy safeguards must be approved before broader rollout.

### 7.3 Timeline and Budget
- Target completion in 12 weeks.
- Build budget bounded by USD 550K-650K for PoC delivery scope.

---

## 8. Assumptions

1. Sponsor can secure governance approvals during early project phases.
2. Existing Microsoft tenant capabilities are sufficient for PoC throughput.
3. Domain adaptation data is available to tune sentiment/topic quality.

---

## 9. Explicit Out of Scope

- Employee-level monitoring, ranking, or punitive analytics.
- Real-time stream processing at full enterprise scale in PoC window.
- Full autonomous actioning workflow beyond insight delivery.

---

## 10. Success Criteria

| Metric | Target | Measurement Window |
|--------|--------|--------------------|
| Ingestion reliability | >= 99% | Last 4 PoC weeks |
| Sentiment F1 | >= 0.78 | Final validation cycle |
| Topic reviewer acceptance | >= 80% | Final validation cycle |
| Executive task completion | >= 85% | UAT week |
| Privacy audit high findings | 0 | Before go/no-go |

---

## 11. Traceability

| FR/NFR | Discovery Origin | Traced? |
|--------|------------------|---------|
| FR-001 | RQ-001 | Yes |
| FR-002 | RQ-002 | Yes |
| FR-003 | RQ-003 | Yes |
| FR-004 | RQ-003 | Yes |
| FR-005 | RQ-004 | Yes |
| FR-006 | RQ-005 | Yes |
| FR-007 | RQ-006 | Yes |
| FR-008 | RQ-007 | Yes |
| FR-009 | RQ-009 | Yes |
| FR-010 | RQ-010 | Yes |
| NFR-001 | RQ-004 | Yes |
| NFR-002 | RQ-005 | Yes |
| NFR-003 | RQ-008 | Yes |
| NFR-004 | RQ-002 | Yes |
| NFR-005 | RQ-001 | Yes |
| NFR-006 | RQ-010 | Yes |
| NFR-007 | RQ-007 | Yes |
| NFR-008 | RQ-009 | Yes |

---

## Version History

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 | 2026-06-01 | @prd | Initial approved version |
