# Skill: Backlog Breakdown

**Agente:** @backlog (Ben)  
**Trigger:** `*breakdown`  
**Input:** `docs/output/{projeto}/prd.md` + `docs/output/{projeto}/architecture.md`  
**Output:** `docs/output/{projeto}/backlog.md`

---

## Objetivo

Decompor o PRD e a arquitetura em um backlog estruturado de Epics e Stories, estimado, priorizado e rastreável.

---

## Pré-condições

- [ ] `prd.md` aprovado com FRs priorizados
- [ ] `architecture.md` aprovado com componentes definidos
- [ ] Complexidade técnica avaliada pelo @architect

---

## Execução

### Etapa 1 — Identificação de Epics
Agrupar FRs relacionados em Epics coesos:
- Cada Epic = um domínio funcional
- Epic deve ter objetivo claro e valor independente
- Regra: sem Epics com mais de 5-7 stories (quebrar se necessário)

### Etapa 2 — Decomposição em Stories
Para cada FR, criar 1+ stories:
- Usar formato: "Como [persona], Quero [ação], Para [benefício]"
- Story deve ser pequena o suficiente para completar em 1-3 dias
- Critérios de aceitação em Given/When/Then

### Etapa 3 — Estimativa
Para cada story, estimar usando tabela de referência:
- XS=1, S=3, M=8, L=13, XL=21 (XL deve ser quebrada)
- Considerar complexidade técnica da arquitetura

### Etapa 4 — Priorização MoSCoW
- Must: essencial para o produto funcionar
- Should: importante, mas pode lançar sem
- Could: nice-to-have
- Won't: fora desta versão

### Etapa 5 — Backlog Técnico
Adicionar stories de infra que não têm FR direto:
- Setup de repositório e CI/CD (sempre Must, blocker)
- Autenticação e autorização (sempre Must)
- Monitoramento e logging (Should)
- Environments (dev/staging/prod) (Must)

### Etapa 6 — Dependências
Mapear dependências entre stories:
- Identificar blockers (TECH-001 bloqueia tudo)
- Identificar sequência obrigatória
- Detectar e eliminar dependências circulares

### Etapa 7 — Sprint 1 (MVP)
Selecionar stories para Sprint 1:
- Apenas Must Have
- Máximo 40-60 story points
- Happy path completo de ao menos 1 persona
- Incluir TECH-001 e TECH-002

### Etapa 8 — Rastreabilidade
Tabela mapeando cada story para o FR/NFR que cobre.

### Etapa 9 — Geração
Preencher template: `templates/backlog/backlog.md`  
Salvar em: `docs/output/{projeto}/backlog.md`

---

## Regras de Qualidade

- Nenhuma story sem critério de aceitação
- Nenhuma story XL sem decomposição
- Sprint 1 deve ter escopo atingível (não otimista demais)
- 100% dos FRs Must Have cobertos por stories

---

## Handoff para @business-case

```
Backlog concluído.

Epics: {N}
Stories totais: {N} ({N} Must, {N} Should, {N} Could)
Story points totais: {N}
Sprint 1 (MVP): {N} pts / {N} stories / {N} sprints estimados

Histórias técnicas incluídas: TECH-001 (CI/CD), TECH-002 (Auth)...

Pronto para @business-case *analyze
```
