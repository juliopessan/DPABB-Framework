# Backlog — {PROJETO}

**Versão:** 1.0  
**Data:** {DATA}  
**Autor:** @backlog (Ben)  
**PRD:** [prd.md](../prd/prd.md)  
**Arquitetura:** [architecture.md](../architect/architecture.md)  
**Status:** Draft | In Review | Approved

---

## Sumário

| Epic | Stories | Pontos | Must | Should | Could |
|------|---------|--------|------|--------|-------|
| EPIC-01 | | | | | |
| **Total** | | | | | |

**Capacidade Sprint 1 (MVP):** _____ pts  
**Sprints estimados:** _____

---

## EPIC-01: {Nome do Epic}

**Objetivo:** ...  
**FRs cobertos:** FR-001, FR-002  
**Estimativa total:** ___ pts  
**Prioridade:** Must Have

---

### STORY-01.01: {Título}

**Como** {persona}  
**Quero** {ação}  
**Para** {benefício}

**Critérios de Aceitação:**
- [ ] Given {contexto} When {ação} Then {resultado esperado}
- [ ] Given {contexto} When {ação} Then {resultado esperado}

| Campo | Valor |
|-------|-------|
| Estimativa | S (3pts) |
| Prioridade | Must Have |
| Dependências | — |
| Rastreabilidade | FR-001 |

---

### STORY-01.02: {Título}

**Como** {persona}  
**Quero** {ação}  
**Para** {benefício}

**Critérios de Aceitação:**
- [ ] Given {contexto} When {ação} Then {resultado esperado}

| Campo | Valor |
|-------|-------|
| Estimativa | M (8pts) |
| Prioridade | Must Have |
| Dependências | STORY-01.01 |
| Rastreabilidade | FR-002 |

---

## EPIC-02: {Nome do Epic}

**Objetivo:** ...  
**FRs cobertos:** FR-003, FR-004  
**Estimativa total:** ___ pts  
**Prioridade:** Should Have

---

### STORY-02.01: {Título}

**Como** {persona}  
**Quero** {ação}  
**Para** {benefício}

**Critérios de Aceitação:**
- [ ] Given {contexto} When {ação} Then {resultado esperado}

| Campo | Valor |
|-------|-------|
| Estimativa | L (13pts) |
| Prioridade | Should Have |
| Dependências | EPIC-01 |
| Rastreabilidade | FR-003 |

---

## Backlog Técnico

> Stories de infraestrutura, segurança e setup sem FR direto.

### TECH-001: Setup de Infraestrutura
**Descrição:** Configurar repositório, CI/CD, ambientes (dev/staging/prod)  
**Estimativa:** M (8pts) | **Prioridade:** Must (blocker) | **Dependências:** —

### TECH-002: Autenticação e Autorização
**Descrição:** Implementar {solução de auth} com RBAC  
**Estimativa:** M (8pts) | **Prioridade:** Must | **Dependências:** TECH-001

### TECH-003: Monitoramento e Alertas
**Descrição:** Configurar logging, métricas e alertas  
**Estimativa:** S (3pts) | **Prioridade:** Should | **Dependências:** TECH-001

---

## Sprint 1 — MVP

**Objetivo do Sprint:** {Descreva o que estará funcionando ao final}  
**Capacidade:** ___ pts  
**Duração:** ___ semanas

| Story | Estimativa | Responsável |
|-------|-----------|------------|
| TECH-001 | 8pts | |
| TECH-002 | 8pts | |
| STORY-01.01 | 3pts | |
| STORY-01.02 | 8pts | |
| **Total** | **27pts** | |

---

## Rastreabilidade Completa

| Story | FR/NFR coberto | Rastreado? |
|-------|---------------|-----------|
| STORY-01.01 | FR-001 | ✅ |
| STORY-01.02 | FR-002 | ✅ |

---

## Histórico de Versões

| Versão | Data | Autor | Mudança |
|--------|------|-------|---------|
| 1.0 | {DATA} | @backlog | Versão inicial |
