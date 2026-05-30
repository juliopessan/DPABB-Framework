# Requirements Intel — AIOS Configuration

## Framework

Este repositório usa o padrão Synkra AIOS para orquestrar agentes de análise de requerimentos técnicos.

## Agentes Disponíveis

| Ativação | Nome | Especialidade |
|----------|------|--------------|
| `@discovery` | Scout | Elicitação de requerimentos, entrevistas, mapeamento de problemas |
| `@prd` | Priya | Documentação de produto, requerimentos funcionais e não-funcionais |
| `@architect` | Aria | Arquitetura técnica, decisões de tecnologia, ADRs |
| `@backlog` | Ben | Epics, stories, estimativas, critérios de aceitação |
| `@business-case` | Biz | ROI, análise de custo-benefício, viabilidade |

## Workflow Principal

```
@discovery *start → @prd *generate → @architect *design → @backlog *breakdown → @business-case *analyze
```

## Comandos

- `*start` — Inicia a fase atual com elicitação guiada
- `*generate` — Gera o documento de output da fase
- `*review` — Revisa o output anterior
- `*pipeline` — Executa o pipeline completo
- `*status` — Mostra o status de todas as fases
- `*exit` — Encerra o agente atual

## Saídas por Fase

Todos os outputs são salvos em `docs/output/{projeto}/`:
- `discovery-report.md`
- `prd.md`
- `architecture.md`
- `backlog.md`
- `business-case.md`

## Princípios

1. **Rastreabilidade** — Todo requerimento deve ser rastreável ao discovery
2. **MECE** — Mutuamente exclusivo, coletivamente exaustivo
3. **Stakeholder-first** — Problemas antes de soluções
4. **Evidence-based** — Decisões baseadas em dados coletados, não suposições
