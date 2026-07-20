# Discovery Briefing - IP Document Draft Evaluation — Word Add-In

**Version:** 1.0  
**Date:** 2026-06-03  
**Prepared by:** @discovery (Scout) — DPABB Framework  
**Status:** Draft

---

## 1. Document Purpose

This briefing consolidates the validated discovery findings for a ROM-oriented POC: an AI-powered Word Add-In to automate QC on patent draft reviews. It provides business context, scope boundaries, assumptions, risks, and estimation guidance to support stakeholder alignment and vendor estimation activities.

---

## 2. Executive Summary

- Initiative: Deliver an assistive AI Word Add-In that performs in-document QC (inline detection, structured feedback, suggested rewrites) while preserving existing comments/track-changes and capturing immutable audit metadata.  
- POC posture: fast, tenant-aware pilot using Foundry-hosted model calls (recommended for speed), with tenant-scoped audit storage where possible.  
- Key value: reduce reviewer time, standardize feedback, improve traceability/compliance.  
- ROM posture: focused POC (10-week effort, small cross-functional team) to validate core capabilities and technical risks prior to full enterprise rollout.

---

## 3. Context and Background

### 3.1 Program Context
- Opportunity: Improve IP review throughput and quality for legal teams that receive patent drafts from outside counsel.  
- Ownership: Legal Ops sponsor; Platform Engineering supports integration with tenant DMS.

### 3.2 Strategic Estimation Context
- This briefing supports a vendor/POC estimate and internal alignment. It is ROM-oriented: not a detailed implementation plan.

### 3.3 Delivery Context
- Timeline pressure: pilot expected within ~10 weeks to demonstrate value.  
- Dependencies: sample documents, stakeholder availability for pilot validation, security/compliance approvals for telemetry/model hosting.

---

## 4. Validated Problem

### 4.1 Core Problem
Patent draft reviews are manual, inconsistent, and lack centralized traceability. Reviewers spend significant time creating comments/redlines; feedback is not standardized by severity or rationale, causing rework and inefficiency.

### 4.2 Evidence of Pain
- Legal teams report hours/day lost to manual review and high iteration counts with outside counsel.  
- No existing standardized taxonomy for issues (severity, rationale, suggested rewrite).  
- Lack of audit trail for model/prompt use and reviewer decisions.

### 4.3 Why Solve Now
- Increased pressure to scale review throughput, reduce cost, and improve governance for IP filings. Pilot provides rapid ROI and will inform enterprise governance for AI-assisted legal workflows.

---

## 5. Estimation Objective

- Objective: produce a ROM for a POC that validates core capabilities (inline detection, structured comments, audit capture) and de-risks preservation, privacy, and model behavior.  
- Expected vendor outputs: solution approach, milestones, effort ranges, assumptions, exclusions.

---

## 6. Scope Definition

### 6.1 In Scope - Workstream A (POC)
- Add-In UI (Office Web Add-In) for Word task pane.  
- Inline issue detection for a targeted set of QC checks (terminology consistency, antecedent references, formatting, basic clarity).  
- Structured comment generation (severity, rationale, suggested rewrite).  
- Audit metadata capture (prompt version, model id, timestamp, user action).  
- Exportable QC summary report (CSV/JSON).

### 6.2 In Scope - Workstream B (Pilot Integration)
- Basic push of QC summary to SharePoint/OneDrive (metadata only).  
- Tenant-scoped storage for audit logs where available.

### 6.3 Related but Peripheral Context
- Deeper prior-art cross-checking, claim drafting, and full DMS integration (versioning/dashboarding) are out of POC scope.

### 6.4 Out of Scope for This ROM
- Full claims drafting or automated prosecution submissions.  
- Scanned PDFs or OCR flows.  
- Multi-tenant SaaS production rollout and operationalization beyond pilot.

---

## 7. Stakeholder Map

| Stakeholder | Role | Influence | Notes |
|-------------|------|----------|-------|
| Reviewers (in-house IP counsel) | Primary users | High | Must approve edits; pilot validators |
| Outside counsel (authors) | Recipients | Medium | Receive standardized feedback; usability impacts adoption |
| Legal Ops / Program Manager | Sponsor | High | Pilot owner, acceptance gate |
| IT / Platform Engineering | Integrator | Medium | DMS integration, hosting policies |
| Compliance / Privacy (LGPD) | Reviewer | High | Approval required for telemetry/model hosting |
| Reporting / Analytics consumers | Consumer | Low-Med | Dashboard/report requirements |

### Stakeholder Tension Points
- Speed vs. data residency (Foundry-hosted quicker; tenant-hosted preferred by privacy).  
- In-place edits vs. risk of corrupting attorney comments/track-changes.

---

## 8. Personas

### Persona 1 - Reviewer (IP Attorney)
- Goals: reduce time per review, maintain control over edits.  
- Needs: accurate issue detection, clear rationale, easy accept/apply edits.  
- Pain points: inconsistent outside-counsel quality, manual redline work.

### Persona 2 - Document Author (Outside Counsel)
- Goals: receive actionable feedback, minimize rework.  
- Needs: clear, standardized comments and suggested rewrites.  
- Pain points: ambiguous reviewer feedback.

### Persona 3 - Legal Ops
- Goals: metrics, auditability, governance.  
- Needs: exportable reports and traceable audit logs.  
- Pain points: lack of centralized tracking.

### Persona 4 - Platform Engineer
- Goals: reliable integration, secure hosting.  
- Needs: clear APIs, tenant isolation options.  
- Pain points: complex Word client behaviors.

---

## 9. Current Process (AS-IS)

### 9.1 AS-IS Summary
- Documents arrive as `.docx` from outside counsel. Reviewers open in Word, manually read, add comments and redlines with track-changes, then send back. Feedback taxonomy varies by reviewer; no centralized metric collection or audit for decisions.

### 9.2 Pain Points
- High manual effort and variability in quality.  
- Iteration cycles with outside counsel are frequent and time-consuming.  
- No consistent way to measure reviewer agreement or aggregate issue types.

---

## 10. Future State Vision (POC TO-BE)

### 10.1 POC TO-BE
- Reviewer launches Add-In in Word, selects contextual documents (IDF/prior art) as needed, runs QC, and receives structured comments with severity, rationale, and suggested rewrites. Reviewer accepts/rejects edits using standard track-changes; all actions are captured in an audit log and a QC summary report.

### 10.2 Expected Value
- Faster reviews, more consistent feedback, traceable decisions, and measurable pilot KPIs.

---

## 11. Solution Framing for ROM

### Current Working Assumptions
- Microsoft-first stack (Office Add-In, Entra ID, SharePoint/OneDrive).  
- POC uses Foundry-hosted LLM calls unless tenant-hosted required by compliance.  
- Preserve existing comments/track-changes as a non-negotiable requirement for UX.

### Architecture Posture
- Client: Office Web Add-In (Office.js) for compatibility.  
- Backend: Azure Functions / Foundry agent for prompt orchestration.  
- Storage: tenant-scoped SQL/Blob where available; Application Insights for telemetry (opt-in/consent).

### Implication for Vendors
- Provide integration approach for Office.js, approach for comment preservation, and tenant-scoped storage options. Clearly call out assumptions and exclusions in estimates.

---

## 12. Gaps and Opportunities

| Gap | Current Impact | Opportunity |
|-----|----------------|------------|
| No standard issue taxonomy | High rework / inconsistent feedback | Define taxonomy as part of pilot to improve consistency |
| Preservation risk across clients | Potential data loss / UX regression | Invest in Office.js-first approach and fallback exports |
| Lack of auditability | Compliance exposure | Build immutable audit entries and per-action logs |

---

## 13. Hypotheses

| Hypothesis | Status | Notes |
|------------|--------|-------|
| Assistive AI can reduce review time by ≥25% | Hypothesis | Pilot to validate with 10–15 sample docs |
| Office.js provides sufficient APIs to preserve comments/track-changes | Unconfirmed | Requires WP-4 cross-client tests |

---

## 14. Risks of Discovery and Estimation

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Model hallucination on legal text | Medium | High | Human-in-loop, conservative prompts, label provenance/confidence |
| Comment/track-change corruption across clients | High | High | Office.js-first implementation, fallback export mode, early cross-client QA |
| Data residency / LGPD constraints | Medium | High | Tenant-scoped storage, consent flows, offer tenant-hosted option for production |

---

## 15. Constraints and Dependencies

### Constraints
- Must operate on locally opened `.docx` without forced upload.  
- Preserve attorney comments and track-changes.  
- Microsoft-only technology stack.

### Dependencies
- Sample document set (10–15) and pilot reviewers.  
- Compliance approval for telemetry/model hosting.  
- Platform availability for tenant-scoped storage.

---

## 16. Compliance and Regulation

### Industry Context
- IP documents often contain sensitive invention details; LGPD applies to personal data within documents.

### Applicable Compliance Themes
- Confidentiality, tenant isolation, auditability, retention, explainability of AI suggestions.

### Compliance Status
- Assumed: telemetry anonymization and tenant-scoped storage will be implemented for pilot; security review required before pilot.

---

## 17. Brainstorming of Solution Ideas

1. In-Add-In detection + comment insertion (baseline).  
2. Export-only QC summary (non-invasive) as fallback.  
3. Prior-art quick-check microservice (Phase 2).  
4. Reviewer-configurable rule packs (Phase 2).

---

## 18. Suggested OKRs for Phase 1

### Objective 1 - Reduce review effort
- KR1: Average review time per document reduced by 25% in pilot.  
- KR2: Pilot reviewers accept ≥50% of AI-suggested rewrites after human review.

### Objective 2 - Improve governance
- KR1: Audit trail completeness = 100% for pilot documents.  
- KR2: Telemetry consent and tenant-scoped storage configured for pilot.

---

## 19. ROM Estimation Guidance for Vendors

### Functional Scope Estimate
- Core components: Office Add-In, Prompt Orchestrator (backend), Metadata/ Audit Store, Report Exporter.

### Technical Estimate
- Integration complexity: Office.js behaviors across clients (variable).  
- Data controls: tenant-scoped storage increases infra work.  
- Security: compliance gating may add 1–2 weeks.

### Delivery Estimate
- Team: 1 PO, 1 Tech Lead, 2 devs (front/back), 1 QA, 1 security reviewer.  
- Duration (POC): 8–10 weeks (ROM).  
- ROM effort range: 4–8 person-months (incl. testing across clients).

### Commercial Guidance
- Use ranges (low/likely/high) and call out key uncertainty drivers: comment preservation, model hosting decision, compliance approvals.

---

## 20. Assumptions

1. Documents are `.docx` with selectable text (not scanned PDFs).  
2. Reviewers will participate in pilot validation.  
3. Tenant storage or consent for telemetry is obtainable for pilot.

---

## 21. Points for Validation

1. Preservation of comments/track-changes across Word Desktop (Win) and Word Web.  
2. Latency for typical 10-page draft (target ≤ 30s per run).  
3. Audit metadata capture completeness and export accuracy.

---

## 22. Open Questions

1. Which Word clients must be supported for pilot (Desktop Win mandatory; confirm Web/Mac)?  
2. Does the customer allow Foundry-hosted inference for pilot or require tenant-hosted?  
3. Are sample documents available and cleared for pilot (10–15)?

---

## 23. Recommended Supplier-Facing Brief Structure

1. Opportunity Overview  
2. Business Problem and Current Pain Points  
3. Scope of Work - Phase 1  
4. Workstream A  
5. Workstream B  
6. Assumptions and Exclusions  
7. Architecture Expectations  
8. Data and Integration Considerations  
9. Non-functional Requirements  
10. Required Vendor Response Format  
11. Risks and Open Questions  
12. Timeline and Evaluation Process

---

## 24. Actions and Immediate Next Steps

1. Validate comment/track-change preservation across Word Desktop (Win) and Word Web (WP-4).  
2. Finalize minimal POC feature set and acceptance criteria.  
3. Identify pilot reviewers and sample documents (10–15).  
4. Conduct security/compliance review for telemetry and model hosting.  
5. Approve model-hosting ADR for pilot (Foundry vs tenant-hosted).

---

## 25. Final Recommendation

Run a focused POC (8–10 weeks) using an Office.js-first Add-In and Foundry-hosted prompt orchestration for speed; require tenant-scoped audit storage where available and a formal compliance review before pilot execution. If compliance rules mandate tenant-hosted inference, treat that as production gating and expect additional setup time.

---

## Enrichment Section A - Generic Intake Snapshot

| Field | Value |
|---|---|
| Project or Opportunity | IP Document Draft Evaluation — Word Add-In |
| Sponsor | Legal Ops |
| Primary Domain | IP / Legal |
| Expected Timeline | 8–10 weeks (POC) |
| Budget Signal | Pilot budget required; TBD |

---

## Enrichment Section B - Governance Add-on

### B.1 Validation Queue
1. Cross-client preservation test (WP-4).  
2. Privacy/telemetry consent flow.

### B.2 Governance Add-on
- Human validation required for all high-severity AI suggestions during pilot.  
- Explicit ownership for approval workflow (Legal Ops).

---

## Version History

| Version | Date | Author | Change |
|--------|------|-------|--------|
| 1.0 | 2026-06-03 | @discovery | Initial ROM-oriented discovery briefing (POC scope) |

