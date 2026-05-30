# Workflow: Requirements Pipeline

**Trigger:** `*pipeline {nome-do-projeto}` ou `@aios-master *workflow requirements-pipeline`  
**Duração estimada:** 60-120 min (interativo) | 20-40 min (com inputs prontos)  
**Output:** 5 documentos completos em `docs/output/{projeto}/`

---

## Descrição

Pipeline sequencial completo para análise de requerimentos técnicos. Cada fase valida o output da anterior antes de prosseguir.

```
Discovery → PRD → Architect → Backlog → Business Case
  @discovery   @prd    @architect    @backlog    @business-case
```

---

## Pré-requisitos

- Ter uma ideia, problema ou iniciativa para analisar
- Opcionalmente: materiais de referência (apresentações, docs existentes)

---

## Fases

### Fase 1: Discovery
**Agente:** @discovery  
**Skill:** `skills/discovery/SKILL.md`  
**Duração:** 15-30 min

**Elicitação:**
- `elicit: true` — requer respostas do usuário (16 perguntas estruturadas)
- Modo conversacional: uma pergunta por vez

**Gate de saída:**
- [ ] Checklist de qualidade do discovery aprovado (7/7)
- [ ] `discovery-report.md` gerado

---

### Fase 2: PRD
**Agente:** @prd  
**Skill:** `skills/prd/SKILL.md`  
**Input obrigatório:** `discovery-report.md`  
**Duração:** 10-20 min

**Elicitação:**
- `elicit: conditional` — perguntas apenas se discovery incompleto

**Gate de saída:**
- [ ] Checklist de qualidade do PRD aprovado (6/6)
- [ ] Rastreabilidade 100% FR/NFR → discovery
- [ ] `prd.md` gerado

---

### Fase 3: Arquitetura
**Agente:** @architect  
**Skill:** `skills/architect/SKILL.md`  
**Input obrigatório:** `prd.md`  
**Duração:** 10-20 min

**Elicitação:**
- `elicit: conditional` — perguntas sobre stack e equipe se não especificados

**Gate de saída:**
- [ ] Checklist de qualidade da arquitetura aprovado (7/7)
- [ ] Mínimo 3 ADRs documentados
- [ ] Cobertura 100% dos NFRs
- [ ] `architecture.md` gerado

---

### Fase 4: Backlog
**Agente:** @backlog  
**Skill:** `skills/backlog/SKILL.md`  
**Input obrigatório:** `prd.md` + `architecture.md`  
**Duração:** 10-20 min

**Elicitação:**
- `elicit: minimal` — confirmar composição do Sprint 1

**Gate de saída:**
- [ ] Checklist de qualidade do backlog aprovado (7/7)
- [ ] Rastreabilidade 100% Stories → FRs
- [ ] Sprint 1 definida
- [ ] `backlog.md` gerado

---

### Fase 5: Business Case
**Agente:** @business-case  
**Skill:** `skills/business-case/SKILL.md`  
**Input obrigatório:** Todos os docs anteriores  
**Duração:** 10-20 min

**Elicitação:**
- `elicit: true` — requer custo/SP e benchmarks do usuário

**Gate de saída:**
- [ ] Checklist de qualidade aprovado (7/7)
- [ ] ROI, payback e NPV calculados
- [ ] Recomendação GO/NO-GO emitida
- [ ] `business-case.md` gerado

---

## Regras do Pipeline

1. **Sequência obrigatória** — Fases não podem ser puladas
2. **Gate de qualidade** — Cada fase passa por checklist antes de prosseguir
3. **Rastreabilidade** — Cada fase herda e referencia outputs anteriores
4. **Elicitação honest** — Se informação estiver faltando, perguntar; não inventar

---

## Modo Rápido (Quick Assessment)

Para projetos SIMPLE ou quando o tempo é crítico:

```
@discovery *start → (respostas em bloco) → @prd *generate → @architect *design → @backlog *breakdown
```

Business case opcional no modo rápido. Ver `workflows/quick-assessment.md`.

---

## Estrutura de Output

```
docs/output/{projeto}/
├── discovery-report.md
├── prd.md
├── architecture.md
├── backlog.md
└── business-case.md
```

---

## Comandos de Controle

```bash
# Iniciar pipeline completo
*pipeline {nome-do-projeto}

# Iniciar fase específica
@discovery *start
@prd *generate
@architect *design
@backlog *breakdown
@business-case *analyze

# Ver status do pipeline
*status

# Retomar fase específica
*resume {fase}  # discovery | prd | architect | backlog | business-case
```
