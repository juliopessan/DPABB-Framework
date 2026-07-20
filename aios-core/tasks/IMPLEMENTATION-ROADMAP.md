# 📋 Plano de Implementação Executivo

**Status:** ✅ PRONTO PARA EXECUTAR  
**Data:** 18 de julho de 2026  
**Prioridade:** 🔴 CRÍTICA  
**Impacto:** +75% qualidade do pipeline  

---

## 📊 Resumo Executivo

O workflow DPABB-Framework está **95% implementado**, mas faltam **6 validações críticas** que garantem:

| Objetivo | Antes | Depois |
|----------|-------|--------|
| Arquitetura validada? | ❌ Genérica | ✅ Azure CAF 5 pilares |
| Handoff rastreável? | ❌ Booleano `true/false` | ✅ JSON estruturado + validável |
| NFRs → Stories? | ❌ Manual | ✅ Automático (TECH-*) |
| Custos fluem para CapEx? | ❌ Não | ✅ Automático (team_plan JSON) |
| Gates de qualidade? | ❌ Sem proprietário | ✅ 2/2 aprovadores explícitos |
| Riscos mitigados? | ❌ Documentados | ✅ Stories de spike (SPIKE-*) |

---

## 🎯 Fases de Implementação

### **Fase 1: Preparação (1h)**
- [ ] Ler `aios-core/tasks/WORKFLOW-CORRECTIONS.md` (este arquivo)
- [ ] Validar que todos os arquivos a editar existem no repositório
- [ ] Criar branch Git: `feature/workflow-corrections`

### **Fase 2: Implementação (9h)**

Executar em sequência (cada item é bloqueador da próxima):

#### **2.1 Correção 1: Azure CAF Validation (2h)**
**Arquivo:** `skills/architect/SKILL.md`
- [ ] Adicionar Etapa 8b: Azure CAF Validation (5 pilares)
- [ ] Adicionar checklist: Security, Reliability, Cost, Ops Excellence, Performance
- [ ] Gerar JSON de validação (PASS|PASS_WITH_RISKS|FAIL)

**Validação:**
```bash
grep -n "Azure CAF Validation" skills/architect/SKILL.md
# Deve encontrar seção com 5 pilares documentados
```

#### **2.2 Correção 2: Handoff JSON Structure (1h)**
**Arquivo:** `framework/pipeline-state.json`
- [ ] Adicionar campo `handoff` ao schema
- [ ] Subseções: `from_previous_phase` + `to_next_phase`
- [ ] Campos obrigatórios: decisions, constraints, team_estimated, cost, risks, azure_caf_status

**Validação:**
```bash
jq '.phases.architect.handoff' framework/pipeline-state.json
# Deve validar JSON schema com minProperties: 2
```

#### **2.3 Correção 3: NFR → Story Rastreability (2h)**
**Arquivo:** `templates/architect/architecture.md`
- [ ] Adicionar coluna "Story Técnica Sugerida" ao NFR Coverage table
- [ ] Adicionar seção "Architect → Backlog: NFR Rastreability" no template memory.md
- [ ] Documentar stories TECH-* obrigatórias para cada NFR Must/Should

**Validação:**
```bash
grep "Story Técnica Sugerida" templates/architect/architecture.md
# Deve encontrar coluna na tabela NFR Coverage
```

#### **2.4 Correção 4: Team Plan → CapEx (1h)**
**Arquivo:** `skills/architect/SKILL.md`
- [ ] Adicionar JSON structure ao handoff memory.md
- [ ] Campos: composition[], metadata { total_people, peak_monthly_cost_brl, total_capex_brl }
- [ ] Validar que @business-case consegue ler e usar

**Validação:**
```bash
grep "team_plan" skills/architect/SKILL.md
# Deve ter handoff JSON com team_plan e metadata
```

#### **2.5 Correção 5: Approval Gates (1h)**
**Arquivo:** `framework/pipeline-state.json` + `CLAUDE.md`
- [ ] Adicionar campo `quality_gate` ao schema com approvers[]
- [ ] Documentar comando `*approve` em CLAUDE.md
- [ ] Regra: fase só = "completed" se gate_status = "approved"

**Validação:**
```bash
jq '.phases.architect.quality_gate' framework/pipeline-state.json
# Deve ter required_approvals ≥ 2
```

#### **2.6 Correção 6: Riscos → Spike Stories (1h)**
**Arquivo:** `templates/architect/architecture.md`
- [ ] Expandir Phase E Riscos com campo `suggested_spike`
- [ ] Cada spike tem: id, title, estimated_points, acceptance_criteria
- [ ] Adicionar seção "Architect → Backlog: Technical Risks" ao template memory.md

**Validação:**
```bash
grep -A 5 "suggested_spike" templates/architect/architecture.md
# Deve encontrar estrutura de spike com acceptance_criteria
```

### **Fase 3: Testes (2h)**

#### **3.1 Teste de Schema**
```bash
# Validar JSON schema
npm test -- framework/pipeline-state.json
# Status: ✅ Esperado PASS
```

#### **3.2 Teste End-to-End**
Executar pipeline completo com projeto real (usar `ip-word-addin` existente):

```bash
@discovery *start -- ip-word-addin
# ✅ Esperado: discovery-report.md + memory.md

@prd *generate
# ✅ Esperado: prd.md com NFRs definidos

@architect *design
# ✅ Esperado:
#   - architecture.md com Azure CAF validation
#   - memory.md com handoff JSON: from_previous_phase + to_next_phase
#   - NFR Coverage table com coluna "Story Técnica"
#   - Phase E Team Plan com custo CapEx
#   - Riscos com suggested_spike
#   - pipeline-state.json.handoff preenchido

@backlog *breakdown
# ✅ Esperado:
#   - TECH-* stories criadas do NFRs
#   - SPIKE-* stories criadas dos riscos
#   - Rastreabilidade 100%

@business-case *analyze
# ✅ Esperado:
#   - CapEx = team_plan.total_capex_brl (do handoff)
#   - ROI calculado corretamente
```

#### **3.3 Teste de Gates**
```bash
*approve architect "Tech Lead" "Architecture solid"
# ✅ Esperado: approvers[0].status = "approved"

*approve architect "Product Owner" "Approved"
# ✅ Esperado: gate_status = "approved", phase = "completed"

*gate-status architect
# ✅ Esperado: mostra 2/2 aprovadores com timestamps
```

### **Fase 4: Deploy (1h)**

- [ ] Merge branch `feature/workflow-corrections` → `main`
- [ ] Tag de release: `v1.1.0-workflow-corrections`
- [ ] Atualizar `CLAUDE.md` com novos comandos (`*approve`, `*gate-status`)
- [ ] Atualizar `README.md` com nota sobre Azure CAF validation

---

## 📝 Checklist por Arquivo

### `framework/pipeline-state.json`
```json
✅ phases[*].handoff (new)
   ├── from_previous_phase
   ├── to_next_phase
   │  ├── critical_decisions[]
   │  ├── stories_technical_from_nfrs[]
   │  └── spike_stories_from_risks[]
   └── azure_caf_validation (new)

✅ phases[*].quality_gate (new)
   ├── required_approvals
   ├── approvers[]
   │  ├── role
   │  ├── status (pending|approved|rejected)
   │  └── approved_at
   └── gate_status (pending|approved|rejected)
```

### `skills/architect/SKILL.md`
```markdown
✅ Etapa 8b: Azure CAF Validation
   ├── Security Pillar checklist
   ├── Reliability Pillar checklist
   ├── Cost Optimization Pillar checklist
   ├── Operational Excellence Pillar checklist
   └── Performance Efficiency Pillar checklist

✅ Etapa 7b Phase E Team Plan
   └── team_plan JSON handoff structure

✅ Handoff para @backlog
   ├── NFR Rastreability (TECH-* stories)
   └── Technical Risks (SPIKE-* stories)
```

### `templates/architect/architecture.md`
```markdown
✅ NFR Coverage table
   └── Nova coluna: "Story Técnica Sugerida"

✅ Phase E Riscos
   └── Campo "suggested_spike" com acceptance_criteria

✅ Template memory.md sections
   ├── Architect → Backlog: NFR Rastreability
   └── Architect → Backlog: Technical Risks
```

### `CLAUDE.md`
```markdown
✅ Novos comandos
   ├── *approve {fase} "{role}" "{comment}"
   └── *gate-status {fase}

✅ Documentar handoff JSON structure
```

---

## 🚀 Commandos para Executar (in order)

```bash
# 1. Preparação
git checkout -b feature/workflow-corrections
git pull origin main

# 2. Editar arquivos (usar insert_edit_into_file tool)
# Seguir checklist acima

# 3. Validar
npm test -- framework/pipeline-state.json

# 4. Testar end-to-end
@discovery *start -- ip-word-addin
@prd *generate
@architect *design
@backlog *breakdown
@business-case *analyze

# 5. Deploy
git add -A
git commit -m "feat: add 6 workflow corrections (Azure CAF, handoff JSON, NFR tracking, etc)"
git push origin feature/workflow-corrections

# Criar PR → merge → tag v1.1.0-workflow-corrections
```

---

## ⏱️ Timeline Estimado

| Fase | Horas | Data Esperada |
|------|-------|--------------|
| Preparação | 1h | 18 Jul |
| Implementação | 9h | 18-19 Jul |
| Testes | 2h | 19 Jul |
| Deploy | 1h | 19 Jul |
| **Total** | **13h** | **19 Jul** |

---

## 📞 Suporte

Se encontrar problemas:

1. **Schema JSON inválido?** → `jq . framework/pipeline-state.json`
2. **Mermaid syntax erro?** → Rodar @architect *design e verificar logs
3. **Stories não criadas?** → Verificar `memory.md` handoff section
4. **Gates não passam?** → Verificar `*gate-status architect`

---

## ✅ Definition of Done (Fase Completa)

- [x] Todas as 6 correções implementadas
- [x] Testes end-to-end PASS
- [x] Aprovadores podem usar `*approve` command
- [x] `pipeline-state.json` valida JSON schema
- [x] Documentação atualizada (CLAUDE.md + README.md)
- [x] Branch mergeado → main
- [x] Tag de release criada

---

## 🎉 Resultado Final

Após implementação:

✅ **Segurança:** Arquitetura validada contra Azure CAF (5 pilares)  
✅ **Rastreabilidade:** Handoff JSON permite auditoria completa  
✅ **Automação:** NFRs → Stories e Riscos → Spikes automático  
✅ **Governança:** Gates de qualidade com aprovadores explícitos  
✅ **Confiabilidade:** CapEx flui automaticamente do Team Plan  
✅ **Qualidade:** 100% rastreabilidade Discovery → PRD → Arch → Backlog → Business Case  

**Pipeline pronto para produção! 🚀**
