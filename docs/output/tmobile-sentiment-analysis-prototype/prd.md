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

### Journey 1: Executive trend exploration (Persona: Executive Decision Maker)

**Scenario:** Weekly sentiment check for executive steering.  
**Duration:** 2–5 minutes.  
**Frequency:** 2–3x/week.

**Happy Path:**
1. Open Power BI dashboard via Copilot portal.
2. Review sentiment trend (past 4 weeks) by org segment.
3. Spot negative trend in a specific business unit.
4. Drill into topic drivers for that unit (e.g., "remote work concerns").
5. Ask Copilot: "What topics are driving negative sentiment in Operations?"
6. Receive grounded response with representative topics, confidence score, and sample conversation themes.
7. Export summary to share with CHRO.

**Expected value:** Detect emerging workforce concerns 4–6 weeks earlier than survey cycle. Identify whether issue is localized or systemic.

**Edge Cases:**
- Missing data for a period (e.g., holiday break).
- Low-confidence sentiment slice (few messages, mixed signals).
- Topic appears in multiple business units; need cross-unit view.

**Error Handling:**
- System displays confidence bands; low-confidence periods show "Insufficient data" badge.
- Fallback: "Try a different date range or business unit."
- Copilot agent returns confidence disclaimer and suggests alternative query.

---

### Journey 2: HR deep-dive analysis (Persona: HR Analytics Lead)

**Scenario:** Investigate sentiment impact of new benefits policy roll-out.  
**Duration:** 15–30 minutes.  
**Frequency:** Ad-hoc, event-driven.

**Happy Path:**
1. Open Power BI dashboard; select "Pre/Post Analysis" view.
2. Define time window: 1 week before policy announcement, 3 weeks after.
3. Select business unit affected by policy.
4. View sentiment trajectory: identify shift point (typically day 2–3 post-announcement).
5. Drill into topic clusters post-announcement: see "benefits clarification," "payroll questions," "migration concerns."
6. Ask Copilot: "Correlate sentiment trends with HR communications about the benefits change. What topics did we miss?"
7. Receive insight: "Sentiment improved slightly, but 12% of conversations focus on 'transition timeline'—not explicitly addressed in comms."
8. Share finding with Internal Comms to plan follow-up message.

**Expected value:** Rapid feedback on communication effectiveness; ability to course-correct messaging in days, not weeks.

**Edge Cases:**
- Multiple overlapping events (e.g., benefits policy + external news).
- Low volume of topic mentions (hard to distinguish signal from noise).
- Confidence tradeoff between precision and recall.

**Error Handling:**
- Copilot includes confidence caveats: "Topic count is low; conclusion may not be reliable."
- Recommend manual validation or follow-up survey for high-stakes decisions.

---

### Journey 3: Governance audit (Persona: Data Governance Lead)

**Scenario:** Monthly compliance check on sentiment data pipeline.  
**Duration:** 10–15 minutes.  
**Frequency:** Monthly.

**Happy Path:**
1. Open Purview data catalog; navigate to Sentiment Data governance dashboard.
2. Review lineage: source → Bronze/Silver/Gold layers → Power BI → Copilot semantic model.
3. Verify anonymization controls: check that no personal identifiers appear in Gold layer.
4. Check access audit log: who queried the data, when, what scope (by org/date/topic).
5. Validate retention policy: message details purged at 90 days; aggregates retained.
6. Generate compliance report: 0 access violations, 100% lineage traced, anonymization controls verified.

**Expected value:** Auditable evidence of privacy controls; confidence that Teams data is handled compliantly.

**Edge Cases:**
- Access audit shows unexpected queries (e.g., external user).
- Anonymization check finds identifiable patterns (e.g., unique topic + date combination).
- Retention policy violated (message older than 90 days still present).

**Error Handling:**
- Alert governance team immediately.
- Halt new sentiment processing until resolved.
- Provide audit trail for remediation.

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

### FR-001 Detail: Teams Ingestion
**Description:** Acquire Teams communication data through approved Graph endpoints at scale and frequency.  
**User Story:** As a data engineer, I need to ingest Teams messages daily so that sentiment analysis has current data.

**Acceptance Criteria:**
- Given approved credentials and Graph scopes, when the scheduled ingestion job runs daily at 2 AM UTC, then all new messages from the past 24h are ingested into Bronze layer.
- Given ingestion job completes successfully, then record includes: message_id, sender_anonymized_id, channel_id, timestamp, message_text, tenant_segment, source_metadata.
- Given transient Graph/API failure, when retry policy triggers (exponential backoff, max 5 retries), then failure is logged and alert is emitted to DevOps team if max retries exceeded.
- Given daily ingestion, when volume monitoring runs, then alerts are triggered if daily ingestion < 80% of prior 7-day average (indicates potential API/permission issue).
- Given ingestion success, then metadata recorded: job start/end time, record count, error count, latency p95.

**Priority:** Must Have  
**Origin:** RQ-001  
**Dependencies:** TECH-001 (CI/CD pipeline), TECH-002 (identity and secrets management)

---

### FR-005 Detail: Power BI Dashboard
**Description:** Publish executive dashboard with sentiment trend, topic clusters, and drill-down capabilities.  
**User Story:** As an executive, I want a Power BI dashboard so I can see sentiment trends and emerging topics in 2 minutes.

**Acceptance Criteria:**
- Dashboard displays sentiment trend (past 4 weeks) by organization, time period, and topic.
- Each sentiment data point includes confidence band (95% CI) and count of contributing messages.
- Topic view shows top 10 topics by frequency, with velocity indicator (trending up/down/stable).
- Drill-down capability: click sentiment point → see contributing topics; click topic → see representative conversation themes (anonymized).
- Page load time: <= 3 seconds (p95); visual interaction response: <= 2 seconds (p95).
- Dashboard accessible only to approved executive users via Copilot portal; access logged in audit trail.
- Export to PowerPoint or CSV functionality available.

**Priority:** Must Have  
**Origin:** RQ-004  
**Dependencies:** STORY-01.03 (Gold semantic model), STORY-02.02 (topic detection)

---

### FR-006 Detail: Copilot Conversational Agent
**Description:** Enable conversational exploration for executive and HR personas via Copilot portal.  
**User Story:** As an HR analyst, I want to ask natural language questions about sentiment so I can explore ad-hoc patterns without building Power BI filters.

**Acceptance Criteria:**
- Given an authorized user query (e.g., "What topics are rising fastest this month?"), when the Copilot agent executes, then it queries the semantic model and returns grounded aggregate response within 8 seconds p95.
- Response includes: answer, contributing data points, confidence score (0.0–1.0), and timestamp of data freshness.
- Given a low-confidence query (< 0.6 confidence), when agent responds, then it includes disclaimer and alternative query suggestion.
- Given an out-of-scope query (e.g., requesting individual employee names), when agent processes, then it refuses and suggests legitimate alternative (e.g., "I can show you topics, not individuals").
- Multi-turn conversation supported: user can refine query based on prior responses.
- All queries logged for audit and model improvement.

**Priority:** Should Have (MVP block: must deliver basic queries; advanced refinement in Phase 2)  
**Origin:** RQ-005  
**Dependencies:** STORY-03.01 (semantic model), LLM hallucination guardrails

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

## 9. Product Roadmap and Phasing

### Phase 1 (PoC, Weeks 1–12)
**Objective:** Deliver MVP with ingestion, sentiment/topic detection, executive dashboard, and baseline Copilot agent.

| Sprint | Focus | Key Deliverables |
|--------|-------|------------------|
| Sprint 1 | Foundations | CI/CD, identity/secrets, Graph connectivity, Bronze schema design |
| Sprint 2 | Data pipeline | Ingestion, anonymization, Silver/Gold layers, data quality checks |
| Sprint 3 | AI signals | Sentiment scoring, topic detection, confidence labeling, model validation |
| Sprint 4 | Consumption + governance | Power BI dashboard, Copilot agent, Purview catalog, deployment automation, UAT |

### Phase 2 (Scale, estimated Months 4–6 post-PoC)
**Objective:** Expand to broader user group (department heads, all HR), add advanced Copilot capabilities, real-time alerting.

- Multi-turn agent refinement (contextual follow-ups)
- Anomaly detection (unexpected sentiment shifts)
- Predictive attrition correlation
- Self-service report builder
- Mobile-friendly dashboard

### Phase 3+ (Enterprise, estimated Months 7+)
- Full enterprise roll-out (all business units, all M365 users with consent)
- Integration with HR systems (turnover, engagement scores, promotions)
- Autonomous escalation workflows (alert leaders on negative trend)
- Multi-language support (if expansion beyond US)

---

## 10. Risks and Mitigation

| # | Risk | Probability | Impact | Mitigation | Owner |
|----|------|-------------|--------|-----------|-------|
| R-001 | Graph API scopes do not permit sentiment use case per legal review | Medium | High | Week 1 legal/privacy validation; prepare fallback: reduced scope or alternative data source | Security/Privacy |
| R-002 | Sentiment model quality < 0.70 F1 on T-Mobile corpus | Medium | High | Domain-specific tuning budget (50 hrs); labeled validation dataset; fallback: human-in-the-loop review | AI team |
| R-003 | LLM hallucination in Copilot agent (confidently returns wrong answer) | Medium | Medium | Grounding strategy (only use semantic model data); fallback response when confidence < 0.6; human validation loop | AI team |
| R-004 | Privacy audit surfaces re-identification risk | Medium | High | Multi-step anonymization + independent audit (budget 40 hrs); suppress low-frequency topics; restrict outputs | Privacy |
| R-005 | LLM token costs exceed budget at scale | High | Medium | Implement caching, batching, prompt optimization; monitor daily spend; governance quotas | FinOps |
| R-006 | Governance approvals delayed (legal, security, privacy) | Medium | High | Week 1 workstream; escalation path to sponsor; prepare alternate scope if necessary | Program lead |
| R-007 | Executive adoption < 40% monthly DAU | Medium | Medium | Early user champion program; embedded training; integration with existing HR systems; measure and iterate | Product |

---

## 11. Explicit Out of Scope

- **Employee-level monitoring:** No ranking, scoring, or identification of individual sentiment contributors.
- **Punitive analytics:** Outputs must not be used for performance management, discipline, or adverse employment actions.
- **Real-time stream processing:** Batch daily ingestion only (Phase 1); real-time streaming is Phase 2+ candidate.
- **Autonomous actioning:** No automated workflow triggers (e.g., auto-escalate negative sentiment). Phase 1 is insight-only.
- **External data sources:** Teams only; no Slack, email, or third-party sources in Phase 1.
- **Custom web frontend:** Must use Copilot portal and Power BI; no bespoke UI development.
- **Non-Microsoft technology:** All components must be Microsoft-native (Fabric, Azure AI, Power BI, Copilot).

---

## 12. Success Criteria

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

## 15. Stakeholder Alignment and Sign-Off

| Stakeholder | Role | Approval Required? | Status |
|-------------|------|-------------------|--------|
| Executive Sponsor | Sponsor | Yes | ✅ Approved |
| HR Analytics Lead | Product Owner | Yes | ✅ Approved |
| Data Governance Lead | Privacy/Compliance | Yes | ✅ Approved (with Week 1 legal review) |
| IT Platform Lead | Technology | Yes | ✅ Approved |
| Internal Comms Lead | Domain user | Advisory | ✅ Consulted |
| Legal/Privacy | Regulatory | Yes | 🟡 Week 1 gate |

---

## 16. Version History

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 | 2026-06-01 | @prd | Initial approved version with expanded user journeys, risk analysis, roadmap, and design principles |
