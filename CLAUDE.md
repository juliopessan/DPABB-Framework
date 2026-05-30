# Requirements Intel — AIOS Configuration

## Framework

Pipeline AIOS para análise inteligente de requerimentos técnicos.  
Cada fase produz um artefato, persiste contexto no `memory.md` e aprende com `lessons-learned.md`.

---

## Agentes Disponíveis

| Ativação | Nome | Especialidade |
|----------|------|--------------|
| `@discovery` | Scout | Elicitação, stakeholders, mapeamento de problemas |
| `@prd` | Priya | FRs, NFRs, personas, rastreabilidade |
| `@architect` | Aria | TOGAF ADM, Mermaid + Azure icons, ADRs |
| `@backlog` | Ben | Epics, Stories, Sprint 1, estimativas |
| `@business-case` | Biz | ROI, payback, GO/NO-GO |

---

## Pipeline

```
@discovery *start
    ↓ salva discovery-report.md + atualiza memory.md
@prd *generate
    ↓ lê memory.md, salva prd.md + atualiza memory.md
@architect *design (TOGAF + Mermaid Azure icons)
    ↓ lê memory.md, salva architecture.md + atualiza memory.md
@backlog *breakdown
    ↓ lê memory.md, salva backlog.md + atualiza memory.md
@business-case *analyze
    ↓ lê memory.md, salva business-case.md + atualiza lessons-learned.md
```

---

## Persistência de Artefatos

### Arquivos de Estado (por projeto)
```
docs/output/{projeto}/
├── memory.md              ← Contexto acumulado (evitar re-elicitação)
├── pipeline-state.json    ← Estado das fases + métricas
├── discovery-report.md    ← Output: @discovery
├── prd.md                 ← Output: @prd
├── architecture.md        ← Output: @architect (TOGAF + Mermaid)
├── backlog.md             ← Output: @backlog
└── business-case.md       ← Output: @business-case
```

### Arquivos de Framework (globais)
```
framework/
├── memory.md              ← Template para memory de novo projeto
├── lessons-learned.md     ← Aprendizados acumulados entre projetos
└── pipeline-state.json    ← Template de estado
```

---

## Regras de Memory

1. **Todo agente lê memory.md antes de agir** — evita re-perguntar
2. **Todo agente escreve handoff no memory.md** — evita re-leitura de artefatos
3. **Nunca re-perguntar** o que está em `§ Perguntas Respondidas`
4. **Nunca reabrir** decisão em `§ Decisões Tomadas`
5. **O state.json é a fonte de verdade** — não iniciar fase sem fase anterior completed

---

## Regras de Lessons Learned

1. **@business-case atualiza lessons-learned.md** ao final de todo pipeline completo
2. **Todo agente lê lições da sua fase** antes de executar (grep `[FASE]`)
3. **Lições nunca são deletadas** — marcar `[OBSOLETO]` quando superadas
4. **Mínimo 2 lições** adicionadas por pipeline completo

---

## Tools por Fase

| Fase | Read | Write |
|------|------|-------|
| Discovery INIT | `framework/memory.md` (template) | `docs/output/{p}/memory.md`, `pipeline-state.json` |
| Discovery SAVE | — | `discovery-report.md`, `memory.md` (update), `state.json` |
| PRD LOAD | `pipeline-state.json`, `memory.md`, `lessons-learned.md`, `discovery-report.md` | `state.json` (in_progress) |
| PRD SAVE | — | `prd.md`, `memory.md` (update), `state.json` |
| Architect LOAD | `pipeline-state.json`, `memory.md`, `lessons-learned.md`, `prd.md` | `state.json` |
| Architect SAVE | — | `architecture.md`, `memory.md`, `state.json` |
| Backlog LOAD | `pipeline-state.json`, `memory.md`, `lessons-learned.md`, `architecture.md` | `state.json` |
| Backlog SAVE | — | `backlog.md`, `memory.md`, `state.json` |
| BizCase LOAD | `pipeline-state.json`, `memory.md`, `lessons-learned.md` | `state.json` |
| BizCase SAVE | — | `business-case.md`, `memory.md`, `state.json`, `lessons-learned.md` |

---

## Arquitetura — Padrões TOGAF + Mermaid

O @architect usa TOGAF 10 ADM (Phases A-E) e gera obrigatoriamente:

| Diagrama | Tipo Mermaid | Fase TOGAF |
|----------|-------------|-----------|
| Contexto (C4 L1) | `architecture-beta` | Phase A |
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

### Ícones Microsoft/Azure (Mermaid architecture-beta)

Prefixo `azure:` para serviços Azure:
```
azure:api-management, azure:app-service, azure:functions,
azure:kubernetes-service, azure:sql-database, azure:cosmos-db,
azure:cache-for-redis, azure:storage-accounts, azure:azure-active-directory,
azure:azure-ad-b2c, azure:key-vault, azure:monitor, azure:application-insights,
azure:service-bus, azure:event-hubs, azure:front-door, azure:load-balancer,
azure:virtual-network, azure:container-registry, azure:devops,
azure:cognitive-services, azure:openai, azure:machine-learning
```

Prefixo `mdi:` para ícones genéricos Microsoft Design:
```
mdi:web, mdi:cellphone, mdi:account-group, mdi:api,
mdi:database, mdi:monitor, mdi:server, mdi:magnify
```

---

## Comandos

| Comando | Efeito |
|---------|--------|
| `*pipeline {projeto}` | Executa pipeline completo |
| `*quick {projeto}` | Pipeline resumido (sem business case completo) |
| `*status` | Mostra estado de todas as fases via state.json |
| `*resume {fase}` | Retoma fase específica (lendo memory.md) |
| `*memory` | Exibe memory.md resumido do projeto atual |
| `*lessons` | Exibe lições aplicáveis à fase atual |

---

## Princípios

1. **Rastreabilidade** — Toda FR/NFR → discovery → story → business case
2. **Memory-first** — Ler antes de perguntar. Escrever antes de sair.
3. **TOGAF rigoroso** — Architecture sempre segue ADM Phases A-E
4. **Mermaid nativo** — Todos os diagramas em Mermaid com ícones Azure reais
5. **Auto-evolutivo** — Cada pipeline melhora lessons-learned.md para o próximo
