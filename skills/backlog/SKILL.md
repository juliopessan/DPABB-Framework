# Skill: Backlog Breakdown

**Agente:** @backlog (Ben)  
**Trigger:** `*breakdown`  
**Input:** `prd.md` + `architecture.md` + `memory.md`  
**Output:** `docs/output/{projeto}/backlog.md`

---

## Pré-condições

- [ ] `phases.prd.status = "completed"`
- [ ] `phases.architect.status = "completed"`
- [ ] `architecture.md` com work packages definidos

---

## Etapa 0 — LOAD (via artifact-manager)

```
1. Read: docs/output/{projeto}/pipeline-state.json
   → Verificar prd + architect = "completed"

2. Read: docs/output/{projeto}/memory.md
   → Extrair handoff "Architect → Backlog" (componentes, complexidade, TECH stories)
   → Extrair handoff "PRD → Architect" (FRs, NFRs priorizados)
   → NÃO re-ler prd.md completo — usar handoff comprimido

3. Read: framework/lessons-learned.md
   → Grep: [BACKLOG] → aplicar lições

4. Read: docs/output/{projeto}/architecture.md
   → Extrair: Work Packages (Phase E), catálogo de componentes

5. Write: pipeline-state.json
   → phases.backlog.status = "in_progress"
```

> **Economia de tokens:** O handoff no memory.md evita re-leitura dos 2 artefatos anteriores completos (~4.000 tokens economizados).

---

## Etapa 1 — Mapeamento WP → Epic

Para cada Work Package da arquitetura:
- Criar 1 Epic correspondente
- Adicionar Epic técnico (EPIC-TECH) para stories de infra
- Verificar cobertura de todos os FRs Must no epics funcionais

---

## Etapa 2 — Decomposição em Stories

Para cada FR Must+Should, criar stories:
- Formato: "Como [persona], Quero [ação], Para [benefício]"
- Stories devem ser completáveis em 1-3 dias
- Critérios de aceitação em Given/When/Then (mínimo 2 por story)

> **Lição aplicada [BACKLOG]:** Stories técnicas TECH-001 (CI/CD) e TECH-002 (Auth) são Must Have no Sprint 1.  
> **Lição aplicada [BACKLOG]:** Nenhuma story com > 13 pts sem decomposição documentada.

---

## Etapa 3 — Backlog Técnico (do handoff da arquitetura)

Criar stories para cada TECH da arquitetura:
```
TECH-001: Setup repositório + Azure DevOps + environments
  → Estimativa: M (8pts) | Must | Blocker de tudo
TECH-002: Provisionar serviços Azure (SQL, Redis, KV, App Insights)
  → Estimativa: M (8pts) | Must | Depende TECH-001
TECH-003: Configurar Entra ID + APIM JWT validation + RBAC seed
  → Estimativa: M (8pts) | Must | Depende TECH-001
TECH-004: {específico do projeto — do handoff}
```

---

## Etapa 4 — Estimativas

Usar tabela: XS=1, S=3, M=8, L=13, XL=21  
XL obrigatoriamente decomposta antes de continuar.

Perguntar ao usuário APENAS se não encontrado no memory:
- "Qual é a velocidade média do time por sprint (story points)?"
- Default conservador: 20 pts/sprint para time novo

---

## Etapa 5 — Priorização MoSCoW

Aplicar com base nas prioridades do PRD:
- FR Must → Story Must Have
- FR Should → Story Should Have
- FR Could → Story Could Have
- Itens técnicos críticos → Must Have independente do FR

---

## Etapa 6 — Dependências

Mapear dependências entre stories. Verificar:
- TECH-001 bloqueia todos os outros
- Auth (TECH-003) bloqueia stories de usuário autenticado
- Sem dependências circulares

---

## Etapa 7 — Sprint 1 (MVP)

Critérios de seleção:
1. Somente Must Have
2. Máximo 40-60 story points
3. Happy path completo de ao menos 1 persona
4. TECH-001, TECH-002, TECH-003 obrigatórios

Confirmar com usuário a composição do Sprint 1.

---

## Etapa 8 — Rastreabilidade

Tabela: cada story → FR ou NFR que cobre.  
Verificar: todos os FRs Must Have têm pelo menos 1 story.

---

## Etapa 9 — SAVE (via artifact-manager)

```
1. Write: docs/output/{projeto}/backlog.md
   → Usando template: templates/backlog/backlog.md

2. Write: docs/output/{projeto}/memory.md (update)
   → Adicionar handoff "Backlog → Business Case":
     "Total Epics: {N}
      Total Stories: {N} ({N} Must, {N} Should, {N} Could)
      Total Story Points: {N}
      Sprint 1: {N} pts / {N} stories
      Sprints estimados até MVP: {N}
      Velocidade base: {N} pts/sprint"
   → Atualizar Artefatos: backlog.md = ✅
   → Atualizar métricas: pipeline-state.phases.backlog.metrics

3. Write: pipeline-state.json
   → phases.backlog.status = "completed"
   → phases.backlog.metrics = { total_epics, total_stories, total_points, sprint1_points, estimated_sprints }
   → current_phase = "business_case"
```

---

## Comunicar ao usuário

```
Backlog gerado ✅

Epics: {N}
Stories: {N} ({N} Must / {N} Should / {N} Could)
Story Points: {N} total
Sprint 1 (MVP): {N} pts / {N} sprints estimados

Rastreabilidade: 100% FRs Must cobertos

Artefatos:
  ✅ docs/output/{projeto}/backlog.md
  ✅ memory.md atualizado (handoff Backlog→BusinessCase)

Próxima fase: @business-case *analyze
```
