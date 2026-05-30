# Requirements Intel

Sistema AIOS para análise inteligente de requerimentos técnicos. Orquestra agentes especializados em um pipeline sequencial que transforma uma ideia bruta em backlog pronto para desenvolvimento.

## Pipeline

```
Discovery → PRD → Architect → Backlog → Business Case
```

| Fase | Agente | Output |
|------|--------|--------|
| 1. Discovery | @discovery | `discovery-report.md` |
| 2. PRD | @prd | `prd.md` |
| 3. Architect | @architect | `architecture.md` |
| 4. Backlog | @backlog | `backlog.md` |
| 5. Business Case | @business-case | `business-case.md` |

## Uso Rápido

```bash
# Pipeline completo
@aios-master *workflow requirements-pipeline

# Fase individual
@discovery *start
@prd *generate
@architect *design
@backlog *breakdown
@business-case *analyze
```

## Estrutura

```
requirements-intel/
├── agents/           # Personas e autoridades dos agentes
├── skills/           # Skills executáveis por fase
├── workflows/        # Pipelines multi-fase
├── templates/        # Templates de output por fase
├── aios-core/        # Tasks e checklists do framework
└── docs/             # Documentação e exemplos
```

## Configuração

Copie `CLAUDE.md` para o projeto alvo ou use globalmente via `~/.claude/`.
