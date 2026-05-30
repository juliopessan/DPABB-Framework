# Workflow: Requirements Pipeline

**Trigger:** `*pipeline {nome-do-projeto}`  
**Duração:** 60-120 min (interativo)  
**Output:** 5 artefatos + memory.md + pipeline-state.json atualizado

---

## Visão do Pipeline com Persistência

```
                    ┌─────────────────────────────────┐
                    │     framework/lessons-learned.md  │ ← lido por todos
                    └─────────────────────────────────┘
                                    ↑ atualizado
@discovery *start                   │
  ├── LOAD: lessons-learned [DISCOVERY]
  ├── INIT: memory.md + pipeline-state.json
  ├── ELICIT: 16 perguntas estruturadas
  ├── SAVE: discovery-report.md
  └── WRITE: memory.md (handoff D→PRD) + state.json
         │
         ▼
@prd *generate
  ├── LOAD: memory.md (handoff) + discovery-report.md + lessons [PRD]
  ├── GENERATE: FRs, NFRs, personas, rastreabilidade
  ├── SAVE: prd.md
  └── WRITE: memory.md (handoff PRD→Arch) + state.json
         │
         ▼
@architect *design  ← TOGAF ADM Phases A-E + Mermaid + Azure Icons
  ├── LOAD: memory.md (handoff) + prd.md + lessons [ARCHITECT]
  ├── PHASE A: Visão + Diagrama Contexto (architecture-beta Azure)
  ├── PHASE B: Negócio + Mindmap + Flowchart
  ├── PHASE C1: Data + ER Diagram + Fluxo de dados
  ├── PHASE C2: Container Diagram (architecture-beta Azure) + Sequência
  ├── PHASE D: Tech Diagram (architecture-beta Azure) + CI/CD + Rede
  ├── PHASE E: Work Packages + Gantt + Build vs Buy
  ├── ADRs: mín. 3 (estilo, banco, auth) com alternativas
  ├── SAVE: architecture.md
  └── WRITE: memory.md (handoff Arch→Backlog) + state.json
         │
         ▼
@backlog *breakdown
  ├── LOAD: memory.md (handoff) + architecture.md + lessons [BACKLOG]
  ├── MAP: WP → Epics, FRs → Stories
  ├── TECH: TECH-001/002/003 sempre no Sprint 1
  ├── ESTIMATE: XS=1, S=3, M=8, L=13 (XL decomposto)
  ├── MOSCOW: priorizar com base no PRD
  ├── SPRINT 1: máx 60pts, Must Have only, happy path
  ├── SAVE: backlog.md
  └── WRITE: memory.md (handoff Backlog→BizCase) + state.json
         │
         ▼
@business-case *analyze
  ├── LOAD: memory.md (TODOS os handoffs) + state.json (métricas)
  ├── CALC: CapEx, OpEx, benefícios, ROI, payback, NPV
  ├── SENSITIVITY: 3 cenários (pessimista/base/otimista)
  ├── ALTERNATIVES: Build vs Buy vs Não fazer nada
  ├── RECOMMEND: GO / GO com ressalvas / REVISÃO / NO-GO
  ├── SAVE: business-case.md
  ├── WRITE: memory.md (pipeline concluído) + state.json
  └── UPDATE: framework/lessons-learned.md ← aprendizados do projeto
```

---

## Gates de Qualidade

| Fase | Gate | Mínimo |
|------|------|--------|
| Discovery | Checklist 7/7 | Problema + 3 stakeholders + 5 RQs + restrições + KPIs |
| PRD | Checklist 6/6 | Rastreabilidade 100% + NFRs com métricas |
| Architect | Checklist 9/9 | TOGAF A-E + 3 ADRs + Mermaid Azure + NFR coverage |
| Backlog | Checklist 7/7 | Rastreabilidade 100% + Sprint 1 + sem XL |
| Business Case | Checklist 7/7 | ROI+Payback+NPV + 3 cenários + recomendação |

---

## State Machine

```
Pipeline Status:
  not_started → in_progress → completed

Phase Status (por fase):
  pending → in_progress → completed | failed

Regra: fase só inicia quando a anterior está "completed"
```

---

## Retomada de Pipeline Interrompido

```bash
# Verificar estado atual
*status → lê docs/output/{projeto}/pipeline-state.json

# Retomar de onde parou
*resume discovery   → @discovery (re-carrega memory, continua elicitação)
*resume prd         → @prd (carrega discovery do memory, gera PRD)
*resume architect   → @architect (carrega PRD do memory, gera arquitetura)
*resume backlog     → @backlog (carrega arquitetura do memory, gera backlog)
*resume business-case → @business-case (carrega tudo do memory)
```

---

## Economia de Tokens por Fase

| Fase | Tokens cold (sem memory) | Tokens com memory | Economia |
|------|------------------------|------------------|---------|
| PRD | ~3.500 | ~900 | 74% |
| Architect | ~5.000 | ~1.200 | 76% |
| Backlog | ~6.000 | ~1.500 | 75% |
| Business Case | ~8.000 | ~2.000 | 75% |
| **Pipeline total** | **~22.500** | **~5.600** | **~75%** |

---

## Estrutura de Output Final

```
docs/output/{projeto}/
├── memory.md              ← Contexto vivo do projeto
├── pipeline-state.json    ← Estado + métricas
├── discovery-report.md    ← Fase 1
├── prd.md                 ← Fase 2
├── architecture.md        ← Fase 3 (TOGAF + Mermaid Azure)
├── backlog.md             ← Fase 4
└── business-case.md       ← Fase 5

framework/
└── lessons-learned.md     ← Framework evoluído
```
