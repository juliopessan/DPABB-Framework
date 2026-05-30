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

- [ ] Cada NFR coberto por decisão arquitetural
- [ ] Mínimo 3 ADRs documentados
- [ ] Riscos técnicos com mitigação
- [ ] Diagrama C4 Context e Container descritos
- [ ] Estratégia de segurança explicitada
- [ ] Estratégia de escalabilidade documentada
- [ ] Dependências externas com SLAs listados

**Gate:** 7/7 para GO

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
