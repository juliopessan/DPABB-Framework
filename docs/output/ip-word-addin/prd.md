# PRD - IP Document Draft Evaluation (Word Add-In)

**Version:** 1.0  
**Date:** 2026-06-03  
**Author:** @prd (Priya) — DPABB Framework  
**Discovery Input:** [discovery-report.md](discovery-report.md)  
**Status:** Draft

---

## 1. Document Overview

### 1.1 Purpose
This PRD defines Phase 1 (POC) scope for an AI-assisted Word Add-In to automate QC of patent drafts, providing structured feedback, preserving attorney comments/track-changes, and capturing immutable audit metadata to support legal governance and pilot ROM estimation.

### 1.2 Document Principles
- Discovery briefing is the source of truth.  
- Human-in-the-loop validation is mandatory for all high-severity suggestions.  
- Microsoft-first stack (Office Add-In, Entra ID).  
- Every FR must map to validated business pain.  
- Output language: EN-US.

### 1.3 Phase Framing
Focus: validate in-document QC, UX-preserving edits, and auditability to enable a production-grade roadmap.

---

## 2. Program Vision

Deliver an assistive, governed workflow that reduces reviewer effort, standardizes feedback, and provides auditable evidence of review decisions—enabling faster, higher-quality IP filings.

---

## 3. Validated Business Problem

### 3.1 Core Business Problem
Patent draft reviews are manual and inconsistent, causing high reviewer effort and repeated iterations with outside counsel; organizations lack standardized feedback taxonomy and traceable audit logs.

### 3.2 Validated Pain Evidence from Discovery
- Manual redline/comment workflows consume reviewer hours daily.  
- No centralized taxonomy or traceability for reviewer decisions.  
- Rework cycles are common and costly.

### 3.3 Why This Matters Now
Scaling legal review throughput is a priority to reduce time-to-file and costs; pilot provides rapid ROI and establishes governance for future automation.

---

## 4. Product Strategy and Phase 1 Intent

### 4.1 Program Objective
Provide a governed, assistive solution that standardizes QC, reduces manual effort, and captures auditable evidence for compliance.

### 4.2 Strategic Product Positioning
This is a conservative, governance-first POC to validate feasibility and controls; not an autonomous drafting tool.

### 4.3 Product Split
#### Product A: Word Add-In (Core)
In-document QC, comment insertion, chat pane, apply/ignore flows, audit capture.

#### Product B: Reporting & Integration
QC summary exports, tenant-scoped audit store, simple push to SharePoint/OneDrive for pilot.

---

## 5. Objectives and North Star Metric

### 5.1 Program North Star Metric
Name: Reviewer Throughput Efficiency  
Definition: Percentage reduction in average reviewer time per document for in-scope patent drafts (measured across pilot reviewers).

### 5.2 Program Success Metrics
| Metric | Baseline | Phase 1 Target | Why It Matters |
|---|---:|---:|---|
| Avg review time per doc | TBD (pilot baseline) | -25% | Direct productivity gain |
| Issue classification agreement vs SME | TBD | ≥80% | Quality of automated detection |
| Audit trail completeness | 0% | 100% | Compliance & traceability |

### 5.3 Product A Objectives
1. Validate reliable in-document issue detection and comment insertion.  
2. Ensure preservation of existing comments/track-changes.

### 5.4 Product B Objectives
1. Deliver audit metadata export and tenant-scoped storage for pilot.  
2. Provide basic QC summary reporting for Legal Ops.

---

## 6. Personas and Stakeholders

### 6.1 Primary Personas
#### Reviewer - IP Attorney
- Needs: accurate, concise feedback; control over edits.  
- Pain: manual redline effort and inconsistent feedback.

#### Document Author - Outside Counsel
- Needs: actionable feedback and clear rewrites.  
- Pain: ambiguous comments causing rework.

#### Legal Ops / Program Manager
- Needs: metrics, audit logs, pilot acceptance.  
- Pain: lack of traceability.

#### Platform Engineer
- Needs: clear integration points, tenant isolation options.  
- Pain: client API inconsistencies.

### 6.2 Stakeholder Map
| Stakeholder | Relevance to Program |
|---|---|
| Reviewers | Primary users and validators |
| Outside counsel | Receiver of feedback |
| Legal Ops | Sponsor & acceptance owner |
| IT / Platform | Integration & hosting |
| Compliance / Privacy | Approval for telemetry/model hosting |

---

## 7. User Journeys

### 7.1 Product A Journeys
#### Journey A1 - Run QC
- User: Reviewer  
- Current pain: Manual scanning and ad-hoc comments.  
- Desired flow: Launch Add-In → Select context docs → Run QC → Review structured comments → Accept/Reject edits → Save.  
- Expected value: Faster, consistent feedback and traceable decisions.

#### Journey A2 - Ask for alternate rewrite
- User: Reviewer  
- Flow: In-taskpane chat → request alternate rewrite → AI returns options → reviewer applies preferred rewrite.  

### 7.2 Product B Journeys
#### Journey B1 - Export QC Summary
- User: Legal Ops  
- Flow: After review, export QC summary → push to SharePoint/OneDrive → consume in metrics dashboard.

---

## 8. Scope Summary

### 8.1 In Scope for Phase 1
#### Product A In Scope
1. Inline issue detection for targeted QC checks.  
2. Comment insertion with severity, rationale, suggested rewrite.  
3. In-add-in chat pane (basic).  
4. Reviewer actions (Accept/Ignore/Resolve) with audit capture.

#### Product B In Scope
1. QC summary export (CSV/JSON).  
2. Tenant-scoped audit metadata store (pilot config).  
3. Simple push to SharePoint/OneDrive.

### 8.2 Out of Scope for Phase 1
#### Product A Out of Scope
- Full claim drafting or substantive legal drafting.

#### Product B Out of Scope
- Deep analytics dashboards and multi-tenant SaaS operations.

#### Program Out of Scope
- Scanned/PDF OCR ingestion.

---

## 9. MVP Scope and Prioritization
| ID | Feature | Product | Priority |
|---|---|---|---|
| F1 | Inline issue detection (core QC) | A | Must-Have |
| F2 | Structured comment insertion (severity/rationale/rewrite) | A | Must-Have |
| F3 | Audit capture & export | B | Must-Have |
| F4 | Reviewer actions (apply/ignore/resolve) | A | Must-Have |
| F5 | In-add-in chat (clarification/rewrites) | A | Should-Have |

**Nice-to-Have:** reviewer-configurable rule packs (Phase 2)

---

## 10. Functional Requirements by Feature

### 10.1 Product A Functional Requirements
#### F1. Inline issue detection
**Business pain addressed:** high manual effort and inconsistent feedback
**Description:** Run predefined QC prompts against selected ranges in the open `.docx` and return structured issues.
**Primary user story:** As a Reviewer, I want the Add-In to detect candidate issues so that I can focus my review on high-impact edits.
**Acceptance criteria:** 1) Detects issues for sample docs; 2) Returns category, severity, rationale, and suggested rewrite.
**Dependencies:** sample docs; backend prompt orchestrator.
**Priority:** Must-Have

#### F2. Structured comment insertion
**Business pain addressed:** inconsistent comments and no standard taxonomy
**Description:** Insert Word comments with metadata and suggested rewrite without deleting existing comments.
**Primary user story:** As a Reviewer, I want standardized comments so that outside counsel receive consistent feedback.
**Acceptance criteria:** 1) Comment inserted with severity/rationale/rewrite; 2) Existing comments preserved.
**Dependencies:** Office.js APIs.
**Priority:** Must-Have

### 10.2 Product B Functional Requirements
#### F3. Audit capture & export
**Business pain addressed:** lack of traceability/audit
**Description:** Record per-action audit entries and export QC summary (CSV/JSON) to tenant storage.
**Primary user story:** As Legal Ops, I want exportable audit logs so that I can review decisions and metrics.
**Acceptance criteria:** 1) Audit entries include timestamp/user/prompt/model; 2) QC export matches in-document issues.
**Dependencies:** Tenant storage or consent-based central store.
**Priority:** Must-Have

---

## 11. Shared Functional Requirements

### S1. Human-in-the-Loop Validation Workflow
**Acceptance criteria:** 1) High-severity suggestions require reviewer sign-off before finalizing; 2) Reviewer actions logged; 3) Unvalidated outputs marked draft.

### S2. Role-Based Access Control
**Acceptance criteria:** role-restricted access and logged unauthorized attempts.

### S3. Auditability and Traceability
**Acceptance criteria:** record/version/validation changes are logged; provenance retained.

---

## 12. Non-Functional Requirements

### 12.1 Security
1. Entra ID authentication and tenant isolation.  
2. Data protection and LGPD compliance for telemetry.  
3. Protected audit logs.

### 12.2 Traceability and Explainability
1. Preserve source references and model/prompt metadata.  
2. Clearly distinguish AI-generated vs SME-approved content.

### 12.3 Performance
Latency target: detection + feedback ≤30s for 10-page draft (POC target).

### 12.4 Reliability
Repository persistence, version history integrity.

### 12.5 Scalability
Architecture supports future phases and higher volumes.

### 12.6 Usability
Minimal reviewer friction; accept/reject flows via track-changes.

### 12.7 Maintainability
Modular backend and clear prompt/version management.

### 12.8 Accessibility
Align with enterprise accessibility expectations.

---

## 13. Compliance and Regulatory Considerations

### 13.1 Compliance Context
IP documents can contain sensitive invention details and PII; LGPD applies.

### 13.2 Phase 1 Compliance Requirements
1. No trusted publication without SME validation.  
2. Role-based access and auditable history.

### 13.3 Mandatory Compliance-Driven Features
- Human-in-the-loop validation, RBAC, audit logging, provenance.

---

## 14. Assumptions and Constraints

### 14.1 Assumptions
1. PRD covers Phase 1 only.  
2. Sample docs are `.docx` with selectable text.  
3. Pilot reviewers are available.

### 14.2 Constraints
1. Office client API variability across platforms.  
2. Compliance approvals may add time.

---

## 15. Dependencies
1. Access to sample documents and pilot reviewers.  
2. Platform identity and tenant storage availability.  
3. Security/compliance sign-off for telemetry/model hosting.

---

## 16. Risks and Mitigations
| Risk | Probability | Impact | Mitigation | Owner |
|---|---:|---:|---|---|
| Model hallucination | Medium | High | Human-in-loop; conservative prompts; provenance labels | Product Lead |
| Comment preservation failure | High | High | Office.js-first; fallback export mode; early QA | Tech Lead |
| Compliance blocking tenant-hosted | Medium | High | Offer tenant-scoped storage; escalate to Legal Ops | Security Owner |

---

## 17. Roadmap

### Milestone 1 - Mobilization (Weeks 1)
- Confirm pilot reviewers; collect 10–15 sample docs; security intake.

### Milestone 2 - Foundation Build (Weeks 2–5)
- Implement Add-In UI, Office.js comment flows, basic backend orchestrator.

### Milestone 3 - Pilot Test & Validation (Weeks 6–7)
- Cross-client preservation tests; measure metrics; fix issues.

### Milestone 4 - Review & Decision (Week 8)
- Present results for GO/NO-GO and next-phase recommendations.

---

## 18. Appendix
- FR mapping to backlog candidates and technical notes available in project backlog.

---

Prepared by: DPABB Framework — PRD generator

