# Requirements Intel — AIOS Framework

> Pipeline AIOS de 5 fases que transforma uma especificação bruta em **Discovery Report + PRD + Arquitetura TOGAF + Backlog + Business Case** — totalmente gerados por agentes de IA especializados.

---

## O que é

**Requirements Intel** é um meta-framework de orquestração de agentes para análise inteligente de requerimentos técnicos. Cada fase é executada por um agente especializado, que lê o contexto acumulado, aplica suas regras, gera um artefato e passa o handoff para o próximo — sem re-elicitação.

```
Documento / Ideia / Reunião
         ↓
  @discovery *start          →  discovery-report.md
         ↓
  @prd *generate             →  prd.md
         ↓
  @architect *design         →  architecture.md  (TOGAF ADM + Mermaid + Azure Icons)
         ↓
  @backlog *breakdown        →  backlog.md
         ↓
  @business-case *analyze    →  business-case.md  +  lessons-learned.md
```

---

## Agentes

| Ativação | Nome | Especialidade |
|----------|------|--------------|
| `@discovery` | Scout | Elicitação, stakeholders, mapeamento de problemas, regras de negócio |
| `@prd` | Priya | FRs, NFRs, personas, critérios de aceite BDD, rastreabilidade |
| `@architect` | Aria | TOGAF 10 ADM Phases A–E, Mermaid + Azure icons, ADRs, Team Plan |
| `@backlog` | Ben | Épicos, Stories, Sprint 1, estimativas, rastreabilidade FR→Story |
| `@business-case` | Biz | ROI, payback, TCO, análise de sensibilidade, GO/NO-GO |

---

## Início Rápido

### Pipeline completo a partir de um documento

```
# Envie o documento (.docx, .pdf, .txt) e execute:
@discovery *start
```

O pipeline avança automaticamente fase a fase, lendo o `memory.md` para não re-perguntar nada já respondido.

### Fase individual

```bash
@discovery *start          # Elicitação + discovery-report.md
@prd *generate             # PRD com FRs, NFRs, personas
@architect *design         # TOGAF ADM A-E + diagramas Mermaid
@backlog *breakdown        # Épicos, stories, Sprint 1
@business-case *analyze    # ROI, payback, GO/NO-GO
```

### Comandos utilitários

| Comando | Efeito |
|---------|--------|
| `*status` | Estado de todas as fases via `pipeline-state.json` |
| `*resume {fase}` | Retoma uma fase específica lendo o `memory.md` |
| `*memory` | Exibe o `memory.md` resumido do projeto atual |
| `*lessons` | Lições aprendidas aplicáveis à fase atual |
| `*todo` | TODO do projeto com progresso |
| `*icons` | Lista ícones disponíveis (M365 + Power Platform + Azure) |

---

## Estrutura do Repositório

```
requirements-intel/
│
├── CLAUDE.md                        ← Configuração AIOS + regras de memória
│
├── agents/                          ← Personas, autoridades e comandos por agente
│   ├── architect.md                 ← Aria — TOGAF + Mermaid + ADRs + Team Plan
│   ├── backlog.md                   ← Ben — Épicos, stories, estimativas
│   ├── business-case.md             ← Biz — ROI, payback, GO/NO-GO
│   ├── discovery.md                 ← Scout — Elicitação, stakeholders
│   └── prd.md                       ← Priya — FRs, NFRs, rastreabilidade
│
├── skills/                          ← Protocolos LOAD/SAVE por fase
│   ├── architect/SKILL.md
│   ├── backlog/SKILL.md
│   ├── business-case/SKILL.md
│   ├── discovery/SKILL.md
│   └── prd/SKILL.md
│
├── templates/                       ← Templates de output por fase
│   ├── architect/architecture.md    ← TOGAF completo com 11 diagramas Mermaid
│   ├── backlog/backlog.md
│   ├── business-case/business-case.md
│   ├── discovery/discovery-report.md
│   └── prd/prd.md
│
├── framework/                       ← Arquivos globais do framework
│   ├── memory.md                    ← Template de memória por projeto
│   ├── lessons-learned.md           ← Lições acumuladas entre projetos
│   ├── pipeline-state.json          ← Template de estado do pipeline
│   └── todo.md                      ← Template de TODO por projeto
│
├── aios-core/
│   ├── tasks/artifact-manager.md   ← Protocolo LOAD/SAVE com Read/Write tools
│   └── checklists/pipeline-quality.md
│
├── assets/icons/                    ← Ícones Microsoft oficiais
│   ├── ICONS.md                     ← Catálogo M365 + Power Platform
│   ├── m365/                        ← 62 SVGs Microsoft 365 (pessoas, infra, processo)
│   ├── power-platform/              ← 8 SVGs Power Platform
│   └── azure/                       ← 705 SVGs Azure Public Service Icons V23
│       └── AZURE-ICONS.md           ← Catálogo completo com Mermaid alt
│
├── workflows/
│   ├── requirements-pipeline.md    ← Pipeline completo 5 fases
│   └── quick-assessment.md         ← Pipeline resumido (sem business case)
│
└── docs/
    ├── example-run.md               ← Exemplo de execução
    └── output/                      ← Artefatos gerados por projeto
        └── estapar-ocr/             ← Exemplo: Estapar OCR Fechamento de Caixa
            ├── discovery-report.md
            ├── prd.md
            ├── architecture.md
            ├── backlog.md
            ├── business-case.md
            ├── memory.md
            └── pipeline-state.json
```

---

## Sistema de Memória

O coração do framework é o **`memory.md` por projeto** — arquivo acumulativo que cada agente lê antes de agir e atualiza ao sair. Isso elimina re-elicitação e comprime contexto entre fases.

```
memory.md
├── Meta (fase atual, token savings)
├── Contexto Acumulado (problema, decisões fixas, restrições)
├── Artefatos Produzidos (status por artefato)
├── Handoffs Entre Fases (Scout→Priya→Aria→Ben→Biz)
├── Perguntas Respondidas (nunca perguntar de novo)
└── Changelog
```

**Economia de tokens estimada:** ~75% por fase (agente lê o handoff, não o artefato inteiro anterior)

---

## Arquitetura — TOGAF + Mermaid

O `@architect` usa **TOGAF 10 ADM (Phases A–E)** e gera obrigatoriamente 11 diagramas Mermaid:

| Diagrama | Tipo Mermaid | Fase TOGAF |
|----------|-------------|-----------|
| Contexto (C4 L1) | `architecture-beta` | Phase A |
| Legenda Azure (componentes) | HTML `<img>` + SVGs locais | Phase A |
| Capacidades de negócio | `mindmap` | Phase B |
| Processo To-Be | `flowchart TD` | Phase B |
| ER Conceitual | `erDiagram` | Phase C1 |
| Fluxo de dados | `flowchart LR` | Phase C1 |
| Container (C4 L2) | `architecture-beta` | Phase C2 |
| Sequência principal | `sequenceDiagram` | Phase C2 |
| Tecnologia / Infra | `architecture-beta` | Phase D |
| Rede e segurança | `flowchart TB` | Phase D |
| CI/CD | `flowchart LR` | Phase D |
| Roadmap | `gantt` | Phase E |
| Ramp-up de equipe | `gantt` | Phase E |
| Onboarding | `flowchart TD` | Phase E |

### Ícones em diagramas Mermaid (`azure:` / `mdi:`)

```
azure:api-management    azure:app-service        azure:functions
azure:kubernetes-service azure:sql-database      azure:cosmos-db
azure:cache-for-redis   azure:storage-accounts   azure:key-vault
azure:monitor           azure:application-insights azure:service-bus
azure:event-hubs        azure:front-door          azure:devops
azure:azure-active-directory  azure:openai        azure:cognitive-services
```

### Ícones locais SVG (HTML `<img>` — Team Plan, RACI, Legenda)

| Coleção | Localização | Quantidade |
|---------|------------|-----------|
| Microsoft 365 | `assets/icons/m365/` | 62 SVGs |
| Power Platform | `assets/icons/power-platform/` | 8 SVGs |
| Azure Services | `assets/icons/azure/` | 705 SVGs (30 categorias) |

---

## Team Plan (Phase E)

O `@architect` gera automaticamente na Phase E um **plano completo de equipe** com:

- **E.4** Composição: tabela com ícones M365 por papel (PO, Arquiteto, Tech Lead, Devs, DevOps, QA, Data Engineer, UX, Scrum Master)
- **E.5** Ramp-up: Gantt com quando cada papel entra no projeto
- **E.6** RACI: matriz de responsabilidades com ícones M365 nos cabeçalhos
- **E.7** Custo: tabela de custo/mês por papel com multiplicador CLT ×1,7
- **E.8** Onboarding: flowchart bifurcado por papel com tempo por papel
- **E.9** Comunicação: ferramentas e canais por categoria

---

## Auto-Evolutivo — Lessons Learned

Ao final de cada pipeline completo, o `@business-case` atualiza o `framework/lessons-learned.md` com novas lições. Todo agente lê as lições da sua fase (`grep [FASE]`) antes de executar.

```
lessons-learned.md
├── [DISCOVERY] — Padrões de elicitação eficaz
├── [PRD] — NFRs com métrica mensurável
├── [ARCHITECT] — Monolito para equipes pequenas
├── [BACKLOG] — Stories técnicas no Sprint 1
└── [PIPELINE] — Handoff escrito economiza >40% tokens
```

---

## Exemplo de Output: Estapar OCR

Pipeline executado sobre a **Especificação Funcional v1.1 — Estapar OCR/Orquestrador (Fechamento de Caixa)**:

| Artefato | Highlights |
|---------|-----------|
| `discovery-report.md` | 11 sistemas PDV, 9+ bancos, 18 RNs extraídas, 9 itens pendentes mapeados |
| `prd.md` | 32 FRs com critérios BDD + 13 NFRs com métricas + rastreabilidade FR→RN |
| `architecture.md` | TOGAF ADM A–E, 11 diagramas Mermaid, 3 ADRs, Team Plan 9 pessoas |
| `backlog.md` | 8 épicos, 54 stories, 273 story points, 8 sprints (~4 meses) |
| `business-case.md` | ROI 18,7% em 24 meses, payback ~16 meses, recomendação **GO** |

---

## Pipeline State Machine

O `pipeline-state.json` por projeto controla o estado de cada fase:

```json
{
  "current_phase": "architect",
  "pipeline_status": "in_progress",
  "phases": {
    "discovery": { "status": "completed", "artifact_exists": true },
    "prd":       { "status": "completed", "artifact_exists": true },
    "architect": { "status": "in_progress" },
    "backlog":   { "status": "pending" },
    "business_case": { "status": "pending" }
  }
}
```

Nenhuma fase inicia sem que as dependências estejam `"completed"`.

---

## Contribuindo

1. Adicione lições em `framework/lessons-learned.md` com a tag `[FASE]`
2. Melhore templates em `templates/{fase}/`
3. Estenda agents em `agents/{agente}.md`
4. Adicione novos workflows em `workflows/`

---

## Tecnologias

- **Runtime:** Claude Code (Anthropic) com AIOS Framework
- **Diagramas:** Mermaid.js (`architecture-beta`, `erDiagram`, `sequenceDiagram`, `gantt`, `mindmap`, `flowchart`)
- **Ícones:** Microsoft Azure Icons V23 (705 SVGs) + Microsoft 365 Icons + Power Platform Icons
- **Metodologia:** TOGAF 10 ADM | C4 Model | MoSCoW | BDD (Given/When/Then)
- **Estado:** JSON state machine por projeto
