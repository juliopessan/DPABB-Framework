# Backlog - T-Mobile Sentiment Analysis Prototype

**Version:** 1.0  
**Date:** 2026-06-01  
**Author:** @backlog (Ben)  
**PRD:** prd.md  
**Architecture:** architecture.md  
**Status:** Approved

---

## Summary

| Epic | Stories | Points | Must | Should | Could |
|------|---------|--------|------|--------|-------|
| EPIC-01 Data Foundation | 4 | 24 | 4 | 0 | 0 |
| EPIC-02 AI Signal Layer | 4 | 27 | 3 | 1 | 0 |
| EPIC-03 Insight Consumption | 4 | 24 | 2 | 2 | 0 |
| EPIC-04 Governance and Ops | 4 | 22 | 3 | 1 | 0 |
| **Total** | **16** | **97** | **12** | **4** | **0** |

**Sprint 1 Capacity (MVP):** 34 pts  
**Estimated Sprints:** 4

---

## EPIC-01: Data Foundation

**Objective:** Deliver governed ingestion and core medallion data path.  
**FR Coverage:** FR-001, FR-002, FR-007  
**Total Estimate:** 24 pts  
**Priority:** Must Have

### STORY-01.01: Teams ingestion connector pipeline
**As** a data engineer  
**I want** a scheduled Graph ingestion pipeline  
**So that** Teams data is loaded with source/time metadata.

**Acceptance Criteria:**
- [ ] Given approved access, when schedule runs, then Bronze is populated with traceable ingestion metadata.
- [ ] Given transient API failure, when retry policy runs, then error is logged and alert is emitted if max retries fail.

| Field | Value |
|-------|-------|
| Estimate | M (8 pts) |
| Priority | Must Have |
| Dependencies | TECH-001 |
| Traceability | FR-001 |

### STORY-01.02: Anonymization and policy tagging
**As** a governance lead  
**I want** anonymization and policy tags applied before analytics consumption  
**So that** privacy requirements are met.

**Acceptance Criteria:**
- [ ] Given incoming message entities, when transformation runs, then sensitive identifiers are tokenized.
- [ ] Given Gold dataset publish, when validation executes, then policy tags are present.

| Field | Value |
|-------|-------|
| Estimate | M (8 pts) |
| Priority | Must Have |
| Dependencies | STORY-01.01 |
| Traceability | FR-002 |

### STORY-01.03: Gold semantic-ready dataset
| Field | Value |
|-------|-------|
| Estimate | M (8 pts) |
| Priority | Must Have |
| Dependencies | STORY-01.02 |
| Traceability | FR-007 |

### STORY-01.04: Data quality and reconciliation checks
| Field | Value |
|-------|-------|
| Estimate | S (3 pts) |
| Priority | Must Have |
| Dependencies | STORY-01.03 |
| Traceability | FR-001, FR-007 |

---

## EPIC-02: AI Signal Layer

**Objective:** Deliver sentiment and topic intelligence with quality guardrails.  
**FR Coverage:** FR-003, FR-004, FR-010  
**Total Estimate:** 27 pts  
**Priority:** Must Have

### STORY-02.01: Sentiment scoring pipeline
| Field | Value |
|-------|-------|
| Estimate | M (8 pts) |
| Priority | Must Have |
| Dependencies | STORY-01.03 |
| Traceability | FR-003 |

### STORY-02.02: Topic detection and ranking
| Field | Value |
|-------|-------|
| Estimate | M (8 pts) |
| Priority | Must Have |
| Dependencies | STORY-02.01 |
| Traceability | FR-004 |

### STORY-02.03: Model quality evaluation harness
| Field | Value |
|-------|-------|
| Estimate | M (8 pts) |
| Priority | Must Have |
| Dependencies | STORY-02.01, STORY-02.02 |
| Traceability | FR-010 |

### STORY-02.04: Confidence labeling for low-certainty outputs
| Field | Value |
|-------|-------|
| Estimate | S (3 pts) |
| Priority | Should Have |
| Dependencies | STORY-02.03 |
| Traceability | FR-003, FR-004 |

---

## EPIC-03: Insight Consumption

**Objective:** Deliver executive dashboarding and conversational insight.  
**FR Coverage:** FR-005, FR-006, FR-010  
**Total Estimate:** 24 pts  
**Priority:** Must/Should mix

### STORY-03.01: Executive dashboard baseline
| Field | Value |
|-------|-------|
| Estimate | M (8 pts) |
| Priority | Must Have |
| Dependencies | STORY-01.03, STORY-02.02 |
| Traceability | FR-005 |

### STORY-03.02: Trend drill-down and topic view
| Field | Value |
|-------|-------|
| Estimate | M (8 pts) |
| Priority | Must Have |
| Dependencies | STORY-03.01 |
| Traceability | FR-005 |

### STORY-03.03: Copilot BI agent integration
| Field | Value |
|-------|-------|
| Estimate | M (8 pts) |
| Priority | Should Have |
| Dependencies | STORY-03.01, STORY-02.03 |
| Traceability | FR-006 |

### STORY-03.04: Multi-agent orchestration prompts and safeguards
| Field | Value |
|-------|-------|
| Estimate | S (3 pts) |
| Priority | Should Have |
| Dependencies | STORY-03.03 |
| Traceability | FR-006, FR-010 |

---

## EPIC-04: Governance and Operations

**Objective:** Ensure production-ready governance, lineage, and deployment automation.  
**FR Coverage:** FR-008, FR-009, FR-010  
**Total Estimate:** 22 pts  
**Priority:** Must/Should mix

### STORY-04.01: Purview catalog and lineage publication
| Field | Value |
|-------|-------|
| Estimate | M (8 pts) |
| Priority | Must Have |
| Dependencies | STORY-01.03 |
| Traceability | FR-008 |

### STORY-04.02: Dev/QA/Prod deployment pipelines
| Field | Value |
|-------|-------|
| Estimate | M (8 pts) |
| Priority | Must Have |
| Dependencies | TECH-001 |
| Traceability | FR-009 |

### STORY-04.03: Monitoring and SLA alerts
| Field | Value |
|-------|-------|
| Estimate | S (3 pts) |
| Priority | Must Have |
| Dependencies | TECH-003 |
| Traceability | FR-009, FR-010 |

### STORY-04.04: Pilot scorecard automation
| Field | Value |
|-------|-------|
| Estimate | S (3 pts) |
| Priority | Should Have |
| Dependencies | STORY-03.01, STORY-04.01 |
| Traceability | FR-010 |

---

## Technical Backlog (Mandatory)

### TECH-001: CI/CD and environment bootstrap
**Description:** Configure repositories, branching policy, and deployment pipelines for Dev/QA/Prod.  
**Estimate:** M (8 pts) | **Priority:** Must | **Dependencies:** None

### TECH-002: Identity and access baseline
**Description:** Implement Entra role model and least-privilege access for data and BI assets.  
**Estimate:** M (8 pts) | **Priority:** Must | **Dependencies:** TECH-001

### TECH-003: Monitoring and observability baseline
**Description:** Enable telemetry and alerting for ingestion, AI scoring, and dashboard/query health.  
**Estimate:** S (3 pts) | **Priority:** Should | **Dependencies:** TECH-001

---

## Sprint 1 - MVP

**Sprint Goal:** Stand up secure data foundation and first usable executive sentiment dashboard path.  
**Capacity:** 34 pts  
**Duration:** 2 weeks

| Story | Estimate | Owner |
|-------|----------|-------|
| TECH-001 | 8 pts | DevOps |
| TECH-002 | 8 pts | Platform Engineer |
| STORY-01.01 | 8 pts | Data Engineer |
| STORY-01.02 | 8 pts | Data Engineer |
| STORY-03.01 (slice) | 8 pts | BI Developer |
| **Total** | **40 pts** | |

**Adjustment Note:** Move STORY-03.01 to 2-week slice at 8 pts and keep stretch items optional, targeting an effective committed load of 34 pts.

---

## Full Traceability

| Story | FR/NFR Covered | Traced? |
|-------|----------------|---------|
| STORY-01.01 | FR-001, NFR-005 | Yes |
| STORY-01.02 | FR-002, NFR-004 | Yes |
| STORY-01.03 | FR-007, NFR-001 | Yes |
| STORY-02.01 | FR-003 | Yes |
| STORY-02.02 | FR-004 | Yes |
| STORY-03.01 | FR-005 | Yes |
| STORY-03.03 | FR-006, NFR-002 | Yes |
| STORY-04.01 | FR-008, NFR-007 | Yes |
| STORY-04.02 | FR-009 | Yes |
| STORY-04.03 | FR-009, NFR-008 | Yes |
| STORY-04.04 | FR-010, NFR-006 | Yes |

---

## Version History

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 | 2026-06-01 | @backlog | Initial approved version |
