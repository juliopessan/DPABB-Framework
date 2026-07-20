# Checklist de Qualidade — Pipeline Completo

Use para validar o pipeline inteiro antes de entregar ao cliente/stakeholder.

---

## Discovery ✓

- [ ] Problema central declarado em 1-2 frases claras
- [ ] Stakeholders identificados com objetivos distintos (mín. 3)
- [ ] Requerimentos de alto nível listados (mín. 5)
- [ ] Restrições técnicas e regulatórias documentadas
- [ ] Premissas e riscos identificados
- [ ] Métricas de sucesso definidas e mensuráveis
- [ ] Nenhuma solução técnica prescrita (apenas o problema)

**Gate:** 7/7 para GO

---

## PRD ✓

- [ ] Todas as personas do discovery representadas
- [ ] FRs cobrem todos os problemas do discovery
- [ ] NFRs incluem performance, segurança e disponibilidade
- [ ] Rastreabilidade 100% (FR/NFR → discovery)
- [ ] Seção "Fora de Escopo" preenchida
- [ ] Critérios de sucesso mensuráveis

**Gate:** 6/6 para GO

---

## Arquitetura ✓

> Espelha `agents/architect.md § Checklist de Qualidade` (TOGAF ADM Phases A–E).

- [ ] Phase A: Visão, princípios arquiteturais e diagrama de Contexto (azure: icons)
- [ ] Phase B: Mindmap de capacidades + flowchart processo To-Be
- [ ] Phase C1: erDiagram conceitual + estratégia de dados + política LGPD
- [ ] Phase C2: Diagrama Container (architecture-beta azure:) + sequenceDiagram
- [ ] Phase D: Diagrama Tecnologia (architecture-beta azure:) + CI/CD + Rede/Segurança
- [ ] Phase E Work Packages: tabela WP + gantt de entrega + build vs buy
- [ ] Phase E Team Plan E.4–E.9: composição + ramp-up gantt + RACI + custo + onboarding
- [ ] ADRs: mínimo 3, com alternativas documentadas e condição de revisão
- [ ] NFRs: cobertura 100% com fase TOGAF e ADR mapeados
- [ ] Riscos técnicos com mitigação e dependências externas com SLAs
- [ ] Ícones M365 usados na seção Team Plan (HTML img tags)

**Gate:** 11/11 para GO

---

## Backlog ✓

- [ ] Todos os FRs cobertos por stories
- [ ] Nenhuma story XL sem decomposição
- [ ] Critérios de aceitação Given/When/Then
- [ ] Dependências mapeadas sem ciclos
- [ ] Sprint 1 com escopo realista
- [ ] Rastreabilidade 100% Stories → FR
- [ ] Stories técnicas incluídas (CI/CD, auth, monitoring)

**Gate:** 7/7 para GO

---

## Business Case ✓

- [ ] Custos com fonte e premissa documentada
- [ ] Benefícios quantificados (não suposições)
- [ ] ROI, payback e NPV calculados
- [ ] Análise de sensibilidade (3 cenários)
- [ ] Mínimo 2 alternativas avaliadas
- [ ] Recomendação clara com justificativa
- [ ] Resumo executivo em máximo 1 página

**Gate:** 7/7 para GO

---

## Rastreabilidade End-to-End ✓

| Camada | Rastreabilidade | Status |
|--------|----------------|--------|
| RQ → FR/NFR | Cada requerimento virou FR ou NFR | |
| FR → Story | Cada FR Must coberto por story | |
| Story → Sprint | Cada Must Have em algum sprint | |
| NFR → ADR | Cada NFR tem decisão arquitetural | |
| Discovery → Business Case | Benefícios baseados em impactos do discovery | |

**Gate:** Todas as linhas ✅ para pipeline APROVADO
