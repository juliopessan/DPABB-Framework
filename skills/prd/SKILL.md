# Skill: PRD Generation

**Agente:** @prd (Priya)  
**Trigger:** `*generate`  
**Input:** `docs/output/{projeto}/discovery-report.md` + `docs/output/{projeto}/memory.md`  
**Output:** `docs/output/{projeto}/prd.md`

---

## Pré-condições

- [ ] `pipeline-state.json` → `phases.discovery.status = "completed"`
- [ ] `discovery-report.md` existe
- [ ] `memory.md` existe com handoff Discovery → PRD

---

## Etapa 0 — LOAD (via artifact-manager)

```
1. Read: docs/output/{projeto}/pipeline-state.json
   → Verificar discovery.status = "completed"
   → Se não: avisar "Discovery ainda não finalizado. Execute @discovery *start"

2. Read: docs/output/{projeto}/memory.md
   → Extrair handoff "Discovery → PRD"
   → Extrair restrições fixas (não re-perguntar)
   → Extrair perguntas já respondidas

3. Read: framework/lessons-learned.md
   → Grep: [PRD] → aplicar lições relevantes

4. Read: docs/output/{projeto}/discovery-report.md
   → Extrair: RQ-XXX, stakeholders, restrições, métricas de sucesso

5. Write: pipeline-state.json
   → phases.prd.status = "in_progress"
   → phases.prd.started_at = {ISO_DATE}
```

---

## Etapa 1 — Análise do Discovery

A partir do discovery-report.md, sintetizar:
- Problema central (não re-ler literalmente — comprimir)
- Lista de RQ-XXX a transformar em FR/NFR
- Personas implícitas nos stakeholders
- Restrições já documentadas

> **Não perguntar** nada que esteja no memory.md § Perguntas Respondidas.

---

## Etapa 2 — Personas

Para cada stakeholder que seja usuário do sistema:
- Criar Persona com: perfil, objetivo, dor, nível técnico, frequência
- Nomear a persona (ex: "Ana, Gestora de RH")
- Se necessário, perguntar apenas: "Para [stakeholder X], qual é a frequência de uso estimada?"

---

## Etapa 3 — User Journeys

Para cada persona principal:
- Happy path (passo a passo)
- Edge cases críticos (máximo 2-3)
- Erros e fallbacks esperados

---

## Etapa 4 — Requerimentos Funcionais

Para cada RQ do discovery:
- Expandir em 1+ FRs específicos e testáveis
- ID sequencial: FR-001, FR-002...
- Prioridade MoSCoW obrigatória
- Critério de aceitação em Given/When/Then

> **Lição aplicada [PRD]:** NFRs sem métrica mensurável são rejeitados no gate.  
> Forma correta: "< 300ms p95" não "ser rápido".

---

## Etapa 5 — NFRs

Sempre incluir (mesmo que discovery não cite):

| Categoria | ID | Métrica mínima |
|-----------|----|---------------|
| Performance | NFR-001 | Tempo de resposta da API principal < Xms p95 |
| Segurança | NFR-002 | Tipo de auth + nível de RBAC |
| Disponibilidade | NFR-003 | Uptime % mensal + RTO/RPO |
| Escalabilidade | NFR-004 | N usuários simultâneos sem degradação |
| LGPD (se aplicável) | NFR-005 | Consentimento + exclusão + portabilidade |
| Manutenibilidade | NFR-006 | Cobertura de testes mínima % |

---

## Etapa 6 — Fora de Escopo

Listar explicitamente o que NÃO será feito nesta versão.  
Incluir funcionalidades mencionadas no discovery mas excluídas.

---

## Etapa 7 — Rastreabilidade

Tabela: cada FR/NFR → RQ de origem no discovery.  
**Regra:** 100% dos FRs/NFRs Must devem ter origem documentada.

---

## Etapa 8 — Perguntas de Clarificação (se necessário)

Fazer ao usuário apenas se discovery for insuficiente:
- "O discovery menciona X como restrição — é hard constraint ou preferência?"
- "Para persona Y, qual é o fluxo mais crítico no MVP?"
- "Não encontrei SLA definido — qual é o uptime esperado?"

---

## Etapa 9 — SAVE (via artifact-manager)

```
1. Write: docs/output/{projeto}/prd.md
   → Usando template: templates/prd/prd.md

2. Write: docs/output/{projeto}/memory.md (update)
   → Adicionar handoff "PRD → Architect":
     "FRs Must: {N}, Should: {N}
      NFRs críticos: {lista com métricas}
      Restrições técnicas: {lista}
      Fora de escopo: {lista}
      Rastreabilidade: 100%"
   → Atualizar Artefatos: prd.md = ✅
   → Adicionar decisões: prioridade MoSCoW de cada FR

3. Write: pipeline-state.json
   → phases.prd.status = "completed"
   → phases.prd.completed_at = {ISO_DATE}
   → phases.prd.artifact_exists = true
   → phases.prd.handoff_written = true
   → current_phase = "architect"
```

---

## Validação

Checklist `agents/prd.md § Checklist de Qualidade` (6/6 para GO)

---

## Comunicar ao usuário

```
PRD gerado ✅

FRs: {N} ({N} Must, {N} Should, {N} Could)
NFRs: {N} (todos com métricas mensuráveis)
Personas: {N}
Rastreabilidade: 100%

Artefatos:
  ✅ docs/output/{projeto}/prd.md
  ✅ memory.md atualizado (handoff PRD→Architect)

Próxima fase: @architect *design
```
