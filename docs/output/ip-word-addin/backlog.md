# Backlog — IP Word Add-In (POC)

**Versão:** 1.0  
**Data:** 2026-06-03  
**Autor:** @backlog (Ben) — DPABB Framework  
**PRD:** [docs/output/ip-word-addin/prd.md](docs/output/ip-word-addin/prd.md)  
**Arquitetura:** [docs/output/ip-word-addin/architecture.md](docs/output/ip-word-addin/architecture.md)  
**Status:** Draft

---

## Sumário

| Epic | Stories | Pontos | Must | Should | Could |
|------|---------|--------:|------:|-------:|------:|
| EPIC-01: Core Add-In | 5 | 27 | 4 | 1 | 0 |
| EPIC-02: Reporting & Audit | 2 | 11 | 2 | 0 | 0 |
| EPIC-03: Quality & Tests | 2 | 8 | 1 | 1 | 0 |
| **Total** | 9 | **46** | 7 | 2 | 0 |

**Capacidade Sprint 1 (MVP):** 27 pts  
**Sprints estimados:** 2–3 (POC)

---

## Team Estimates & Effort

Abaixo está a alocação de time prevista para o POC, em horas/semana, duração e janela de trabalho. Use `Hours/week × Duration` para calcular person-hours.

| Role | Hours/week | Activity | Duration (weeks) | Start week | End week | Person-hours |
|------|-----------:|----------|-----------------:|-----------:|---------:|-------------:|
| Product Owner (PO) | 8 | Requirement clarifications, acceptance | 10 | 1 | 10 | 80 |
| Tech Lead | 40 | Build / architecture oversight | 6 | 2 | 7 | 240 |
| Backend Dev | 40 | Implement APIs / Agent orchestrator | 6 | 2 | 7 | 240 |
| Frontend Dev | 40 | Add-In UI, Office.js integration | 6 | 2 | 7 | 240 |
| QA | 20 | Test harness, cross-client validation (WP-4) | 4 | 5 | 8 | 80 |
| Security Reviewer | 8 | Security review, compliance checks | 4 | 4 | 7 | 32 |

**Total person-hours (POC window above):** 912 hours

Guidance: converta person-hours para custo utilizando as premissas do Business Case ou para story-points conforme regra do time (ex.: 1 SP ≈ 8–13 hours, ajustar localmente).


## EPIC-01: Core Add-In

**Objetivo:** Implementar o Add-In Word que detecta issues, insere comentários padronizados e permite ações de reviewer.  
**FRs cobertos:** FR-1, FR-2, FR-3, FR-4  
**Estimativa total:** 27 pts  
**Prioridade:** Must Have

### STORY-01.01: Run QC detection (FR-1)

**Como** Reviewer  
**Quero** executar um conjunto pré-definido de prompts sobre o documento  
**Para** obter candidatos a issues com categoria, severidade e rationale

**Critérios de Aceitação:**
- [ ] Given a sample `.docx` When I run QC Then I receive a structured list of issues (category, severity, rationale)
- [ ] Issues include document location (range/paragraph id)

| Campo | Valor |
|-------|-------|
| Estimativa | M (8pts) |
| Prioridade | Must Have |
| Dependências | TECH-001, TECH-002 |
| Rastreabilidade | FR-1 |

### STORY-01.02: Insert structured comment (FR-2)

**Como** Reviewer  
**Quero** que cada issue vire um comentário com severity, rationale e suggested rewrite  
**Para** entregar feedback padronizado a outside counsel

**Critérios de Aceitação:**
- [ ] Given detected issue When I choose insert Then a Word comment is added preserving existing comments
- [ ] Comment contains severity, rationale, suggested rewrite and audit metadata id

| Campo | Valor |
|-------|-------|
| Estimativa | S (3pts) |
| Prioridade | Must Have |
| Dependências | STORY-01.01, Office.js capability |
| Rastreabilidade | FR-2 |

### STORY-01.03: Reviewer actions (apply/ignore/resolve) (FR-3)

**Como** Reviewer  
**Quero** aplicar uma sugestão (track-changes), ignorar ou marcar resolvido  
**Para** controlar alterações e manter trilha auditável

**Critérios de Aceitação:**
- [ ] Given a comment When I Accept Then the suggested rewrite is applied as an edit with track-changes
- [ ] All actions generate an audit entry with timestamp and user

| Campo | Valor |
|-------|-------|
| Estimativa | M (8pts) |
| Prioridade | Must Have |
| Dependências | STORY-01.02, TECH-002 |
| Rastreabilidade | FR-3, FR-5 |

### STORY-01.04: In-add-in chat for alternate rewrites (FR-4)

**Como** Reviewer  
**Quero** solicitar rewrites alternativos no taskpane chat  
**Para** iterar rapidamente sobre opções de redação

**Critérios de Aceitação:**
- [ ] Chat accepts a prompt and returns 2–3 alternate rewrites
- [ ] Reviewer can preview and apply a selected rewrite

| Campo | Valor |
|-------|-------|
| Estimativa | M (8pts) |
| Prioridade | Should Have |
| Dependências | STORY-01.01, TECH-002 |
| Rastreabilidade | FR-4 |

### STORY-01.05: Preservation fallback mode (non-invasive export) (FR-2)

**Como** Reviewer  
**Quero** um modo export-only quando client não garante preservação  
**Para** evitar perda de comentários/track-changes

**Critérios de Aceitação:**
- [ ] Given unsupported client When I select export mode Then system generates a QC report (JSON/CSV) without modifying doc

| Campo | Valor |
|-------|-------|
| Estimativa | S (3pts) |
| Prioridade | Must Have |
| Dependências | TECH-001 |
| Rastreabilidade | FR-2 |

---

## EPIC-02: Reporting & Audit

**Objetivo:** Implementar captura de auditoria e export de relatórios para Legal Ops.  
**FRs cobertos:** FR-5, FR-6  
**Estimativa total:** 11 pts  
**Prioridade:** Must Have

### STORY-02.01: Audit capture per action (FR-5)

**Como** Legal Ops  
**Quero** que cada AI action e reviewer-decision gere um registro de auditoria  
**Para** ter trilha imutável de decisões

**Critérios de Aceitação:**
- [ ] Audit entries include timestamp, user, action, prompt id/version, model id
- [ ] Entries stored tenant-scoped when available

| Campo | Valor |
|-------|-------|
| Estimativa | M (8pts) |
| Prioridade | Must Have |
| Dependências | TECH-001, TECH-002 |
| Rastreabilidade | FR-5 |

### STORY-02.02: QC summary export (CSV/JSON) (FR-6)

**Como** Legal Ops  
**Quero** exportar um resumo de issues por documento  
**Para** alimentar dashboards e relatórios

**Critérios de Aceitação:**
- [ ] Export contains issue list, severity distribution, document id and audit references

| Campo | Valor |
|-------|-------|
| Estimativa | S (3pts) |
| Prioridade | Must Have |
| Dependências | STORY-02.01 |
| Rastreabilidade | FR-6 |

---

## EPIC-03: Quality & Tests

**Objetivo:** Garantir preservação cross-client e cobertura de QA para o POC.  
**Estimativa total:** 8 pts  
**Prioridade:** Must/Should

### STORY-03.01: Cross-client preservation tests (WP-4)

**Como** QA  
**Quero** validar comportamento em Word Desktop (Win) e Word Web  
**Para** confirmar preservation strategy

**Critérios de Aceitação:**
- [ ] Test matrix executed across Win/Web with sample docs and results documented

| Campo | Valor |
|-------|-------|
| Estimativa | M (8pts) |
| Prioridade | Must Have |
| Dependências | STORY-01.02 |
| Rastreabilidade | WP-4 |

### STORY-03.02: Prompt regression tests

**Como** AI Engineer  
**Quero** um conjunto de regression prompts and golden outputs  
**Para** monitorar drift and quality

**Critérios de Aceitação:**
- [ ] Baseline prompts and expected outputs stored; automated smoke run possible

| Campo | Valor |
|-------|-------|
| Estimativa | S (3pts) |
| Prioridade | Should Have |
| Dependências | TECH-003 |
| Rastreabilidade | FR-1 |

---

## Backlog Técnico

### TECH-001: Setup de Infraestrutura (CI/CD, repositório, ambientes)
**Descrição:** Criar repositório, pipeline CI/CD com build/test e ambientes (dev/staging).  
**Estimativa:** M (8pts) | **Prioridade:** Must (blocker) | **Dependências:** —

### TECH-002: Autenticação e Autorização (Entra ID + RBAC)
**Descrição:** Implementar Entra ID SSO, managed identities e modelo básico de RBAC.  
**Estimativa:** M (8pts) | **Prioridade:** Must | **Dependências:** TECH-001

### TECH-003: Observabilidade e Telemetria
**Descrição:** Configurar Application Insights / Log Analytics, alertas e dashboards básicos.  
**Estimativa:** S (3pts) | **Prioridade:** Should | **Dependências:** TECH-001

---

## Sprint 1 — MVP

**Objetivo do Sprint:** Delivery das capabilities core: infra, auth, detection e comment insertion (MVP).  
**Capacidade:** 27 pts  
**Duração:** 2 semanas

| Story | Estimativa | Responsável |
|-------|----------:|------------:|
| TECH-001 | 8pts | Platform |
| TECH-002 | 8pts | Platform |
| STORY-01.01 | 8pts | Backend |
| STORY-01.02 | 3pts | Frontend |
| **Total** | **27pts** | |

---

## Rastreabilidade Completa

| Story | FR/NFR coberto | Rastreado? |
|-------|---------------|-----------|
| STORY-01.01 | FR-1 | ✅ |
| STORY-01.02 | FR-2 | ✅ |
| STORY-01.03 | FR-3, FR-5 | ✅ |
| STORY-01.04 | FR-4 | ✅ |
| STORY-01.05 | FR-2 | ✅ |
| STORY-02.01 | FR-5 | ✅ |
| STORY-02.02 | FR-6 | ✅ |

---

## Enrichment: Delivery Notes

- Definition of Ready: sample docs available and security intake completed.  
- Definition of Done: automated tests + evidence (screenshot or export) + audit entries for applied edits.

---

Prepared by: @backlog (Ben) — DPABB Framework
