# DPABB Framework

> Pipeline AIOS de 5 fases que transforma uma especificação bruta em **Discovery Report + PRD + Arquitetura TOGAF + Backlog + Business Case** — totalmente gerados por agentes de IA especializados, e consolidados ao final em um único relatório HTML.

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
         ↓
  @business-case *consolidate (executado dentro de *exit) → relatorio-consolidado.html
```

**Etapa final obrigatória:** ao concluir `@business-case *analyze` (ou ao chamar `*exit`), os 5 artefatos são
consolidados **na íntegra** em um único `docs/output/{projeto}/relatorio-consolidado.html`, usando o design
system em `templates/consolidated-report/base-template.html` (editorial dark/paper, Space Grotesk + Inter +
JetBrains Mono, diagramas Mermaid renderizados ao vivo). Esse HTML é o entregável final apresentável ao
stakeholder — os `.md` continuam sendo a fonte de verdade para o pipeline.

---

## Padrão do Relatório Consolidado (HTML)

O `relatorio-consolidado.html` segue um design system editorial fixo — não é gerado com estilo livre a cada execução. Regras obrigatórias:

| Token | Valor |
|-------|-------|
| Fundo claro / seções escuras | `--paper:#faf7f2` / `--dark:#141312` |
| Cor de destaque / gradiente | `--accent:#FF5800` (laranja) → `--aurora:#890078` (roxo) |
| Tipografia | Space Grotesk (display/headings) · Inter (corpo) · JetBrains Mono (labels/meta uppercase) |
| Estrutura | Hero com título tachado (`.strike`) → band de métrica north-star (fundo escuro) → 5 `<section class="phase">` numeradas `00`–`04` (uma por fase, número grande stroke-only sticky à esquerda) → marquee de palavras-chave → footer escuro com status-dot |
| Componentes | `.tbl` (tabelas com header escuro), `.gate` (cards de critério/gate/ressalva), `.callout` (gradiente laranja→roxo para destaques), `.story` (cada user story do backlog), `.adr` (borda esquerda roxa, uma por ADR) |
| Diagramas | Mermaid.js via CDN (`<script src=".../mermaid.min.js">` + `<pre class="mermaid">`), renderizados **ao vivo** no navegador — nunca como imagem estática |
| Conteúdo | Os 5 `.md` transcritos **na íntegra** (todas as stories, ADRs completos com alternativas/consequências, todas as tabelas de FR/NFR/riscos) — nunca uma versão resumida |

**Como gerar:** copiar `templates/consolidated-report/base-template.html`, substituir os placeholders (`{PROJETO_TITULO}`, `{RECOMENDACAO}`, etc.) e preencher uma seção `.phase` por fase com o conteúdo completo do `.md` correspondente. Executado automaticamente por `@business-case *consolidate` (parte de `*exit`).

**Exemplo de referência:** [`docs/output/intelligent-operations-document-platform/relatorio-consolidado.html`](docs/output/intelligent-operations-document-platform/relatorio-consolidado.html).

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
DPABB-Framework/
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
│   ├── architect/architecture.md    ← TOGAF completo com 14 diagramas Mermaid
│   ├── backlog/backlog.md
│   ├── business-case/business-case.md
│   ├── discovery/discovery-report.md
│   ├── prd/prd.md
│   ├── consolidated-report/
│   │   └── base-template.html       ← Skeleton do relatório HTML final (etapa obrigatória de *exit)
│   └── html/sme-view.html           ← Developer Guide HTML navegável (SME View, opcional via @architect *html)
│
├── framework/                       ← Arquivos globais do framework
│   ├── memory.md                    ← Template de memória por projeto
│   ├── lessons-learned.md           ← Lições acumuladas entre projetos (auto-evolutivo)
│   ├── pipeline-state.json          ← Template de estado do pipeline
│   └── todo.md                      ← Template de TODO por projeto
│
├── aios-core/
│   ├── tasks/artifact-manager.md   ← Protocolo LOAD/SAVE com Read/Write tools
│   └── checklists/pipeline-quality.md
│
├── assets/icons/                    ← Ícones Microsoft oficiais
│   ├── ICONS.md                     ← Catálogo M365 + Power Platform + referência Azure
│   ├── m365/                        ← 62 SVGs Microsoft 365 (pessoas, infra, processo)
│   ├── power-platform/              ← 8 SVGs Power Platform
│   └── azure/                       ← 705 SVGs Azure Public Service Icons V23
│       └── AZURE-ICONS.md           ← Catálogo completo por categoria com Mermaid alt
│
├── workflows/
│   ├── requirements-pipeline.md    ← Pipeline completo 5 fases
│   └── quick-assessment.md         ← Pipeline resumido (sem business case)
│
└── docs/
    ├── example-run.md               ← Exemplo de execução
    └── output/                      ← Artefatos gerados por projeto (gitignored por padrão)
        └── {projeto}/
            ├── discovery-report.md
            ├── prd.md
            ├── architecture.md
            ├── backlog.md
            ├── business-case.md
            ├── memory.md
            ├── pipeline-state.json
            └── relatorio-consolidado.html  ← Etapa final: os 5 artefatos na íntegra em 1 HTML
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

**Economia de tokens estimada:** ~75% por fase — o agente lê apenas o handoff comprimido, não o artefato completo da fase anterior.

---

## Arquitetura — TOGAF + Mermaid

O `@architect` usa **TOGAF 10 ADM (Phases A–E)** e gera obrigatoriamente os seguintes diagramas Mermaid:

| Diagrama | Tipo Mermaid | Fase TOGAF |
|----------|-------------|-----------|
| Contexto (C4 L1) | `architecture-beta` | Phase A |
| Legenda de componentes Azure | HTML `<img>` + SVGs locais | Phase A |
| Capacidades de negócio | `mindmap` | Phase B |
| Processo To-Be | `flowchart TD` | Phase B |
| ER Conceitual | `erDiagram` | Phase C1 |
| Fluxo de dados | `flowchart LR` | Phase C1 |
| Container (C4 L2) | `architecture-beta` | Phase C2 |
| Sequência principal | `sequenceDiagram` | Phase C2 |
| Tecnologia / Infra | `architecture-beta` | Phase D |
| Rede e segurança | `flowchart TB` | Phase D |
| CI/CD | `flowchart LR` | Phase D |
| Roadmap de entrega | `gantt` | Phase E |
| Ramp-up de equipe | `gantt` | Phase E |
| Onboarding por papel | `flowchart TD` | Phase E |

### Ícones em diagramas Mermaid (`azure:` e `mdi:`)

```
azure:api-management       azure:app-service          azure:functions
azure:kubernetes-service   azure:sql-database         azure:cosmos-db
azure:cache-for-redis      azure:storage-accounts     azure:key-vault
azure:monitor              azure:application-insights azure:service-bus
azure:event-hubs           azure:front-door            azure:devops
azure:azure-active-directory  azure:openai            azure:cognitive-services
mdi:web  mdi:cellphone  mdi:account-group  mdi:database  mdi:server
```

### Ícones locais SVG (HTML `<img>` — Team Plan, RACI, Legenda)

| Coleção | Localização | Quantidade |
|---------|------------|-----------|
| Microsoft 365 | `assets/icons/m365/` | 62 SVGs |
| Power Platform | `assets/icons/power-platform/` | 8 SVGs |
| Azure Services | `assets/icons/azure/` | 705 SVGs (30 categorias) |

---

## Team Plan (Phase E)

O `@architect` gera automaticamente na Phase E um **plano completo de equipe**:

| Seção | Conteúdo |
|-------|---------|
| **E.4 Composição** | Tabela com ícones M365 por papel — PO, Arquiteto, Tech Lead, Devs, DevOps, QA, Data Engineer, UX, Scrum Master |
| **E.5 Ramp-up** | Gantt com quando cada papel entra no projeto + marcos (Kick-off, Sprint 0, MVP, Go-live) |
| **E.6 RACI** | Matriz de responsabilidades (R/A/C/I) com ícones M365 nos cabeçalhos |
| **E.7 Custo** | Tabela custo/mês por papel, dedicação, duração, total — com multiplicador CLT ×1,7 |
| **E.8 Onboarding** | Flowchart bifurcado por papel com tempo de integração por função |
| **E.9 Comunicação** | Ferramentas e canais por categoria (daily, sprint, demo, tracking, code) |

---

## Auto-Evolutivo — Lessons Learned

Ao final de cada pipeline completo, o `@business-case` atualiza `framework/lessons-learned.md` com novas lições. Todo agente lê as lições da sua fase (`grep [FASE]`) antes de executar.

```
lessons-learned.md
├── [DISCOVERY] — Padrões de elicitação eficaz
├── [PRD]       — NFRs com métrica mensurável
├── [ARCHITECT] — Monolito para equipes pequenas
├── [BACKLOG]   — Stories técnicas no Sprint 1
└── [PIPELINE]  — Handoff escrito economiza >40% tokens
```

---

## Pipeline State Machine

O `pipeline-state.json` por projeto controla o estado de cada fase e bloqueia execução fora de ordem:

```json
{
  "current_phase": "architect",
  "pipeline_status": "in_progress",
  "phases": {
    "discovery":      { "status": "completed", "artifact_exists": true },
    "prd":            { "status": "completed", "artifact_exists": true },
    "architect":      { "status": "in_progress" },
    "backlog":        { "status": "pending", "depends_on": ["prd", "architect"] },
    "business_case":  { "status": "pending", "depends_on": ["discovery", "prd", "architect", "backlog"] }
  }
}
```

---

## Artefatos por Fase

Cada fase produz um artefato em `docs/output/{projeto}/`:

| Fase | Artefato | Conteúdo típico |
|------|---------|----------------|
| Discovery | `discovery-report.md` | Stakeholders, problema, regras de negócio, riscos, itens pendentes |
| PRD | `prd.md` | FRs com critérios BDD, NFRs com métricas, personas, rastreabilidade |
| Architect | `architecture.md` | TOGAF A–E, 14 diagramas Mermaid, 3+ ADRs, Team Plan E.4–E.9 |
| Backlog | `backlog.md` | Épicos, stories, story points, Sprint 1 plan, rastreabilidade FR→Story |
| Business Case | `business-case.md` | TCO, ROI, payback, análise de sensibilidade, recomendação GO/NO-GO |

---

## Tecnologias

- **Runtime:** Claude Code (Anthropic) com AIOS Framework
- **Diagramas:** Mermaid.js (`architecture-beta`, `erDiagram`, `sequenceDiagram`, `gantt`, `mindmap`, `flowchart`)
- **Ícones:** Microsoft Azure Icons V23 (705 SVGs) · Microsoft 365 Icons (62 SVGs) · Power Platform Icons (8 SVGs)
- **Metodologia:** TOGAF 10 ADM · C4 Model · MoSCoW · BDD (Given/When/Then) · Story Points
- **Estado:** JSON state machine por projeto com dependências entre fases
