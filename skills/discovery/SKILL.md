# Skill: Discovery

**Agente:** @discovery (Scout)  
**Trigger:** `*start` ou `/discovery`  
**Output:** `docs/output/{projeto}/discovery-report.md`

---

## Pré-condições

- Nenhuma fase anterior (é a primeira fase)
- Projeto nomeado pelo usuário

---

## Etapa 0 — INIT (primeira execução)

```
1. Verificar se docs/output/{projeto}/ existe
   → Se não: criar diretório

2. Verificar se docs/output/{projeto}/memory.md existe
   → Se não: criar a partir de framework/memory.md (substituir {PROJETO})

3. Verificar se docs/output/{projeto}/pipeline-state.json existe
   → Se não: criar a partir de framework/pipeline-state.json (substituir {projeto})

4. Read: framework/lessons-learned.md
   → Grep: [DISCOVERY] → aplicar lições relevantes desta sessão

5. Read: docs/output/{projeto}/memory.md
   → Se já existe (retomada): carregar perguntas respondidas, não repetir

6. Write: pipeline-state.json
   → phases.discovery.status = "in_progress"
   → phases.discovery.started_at = {ISO_DATE}
```

---

## Etapa 1 — Contextualização

> Antes de iniciar: ler `memory.md § Perguntas Respondidas` e pular as que já têm resposta.

Faça as perguntas abaixo de forma conversacional, uma a uma:

1. "Qual é o nome do projeto e em qual empresa ou contexto ele será desenvolvido?"
2. "Descreva o problema principal em 2-3 frases. Qual é a dor real?"
3. "Por que resolver isso agora? Qual foi o gatilho?"

---

## Etapa 2 — Stakeholders

4. "Quem são os principais stakeholders? Liste: quem paga, quem usa, quem aprova."
5. "Qual é o objetivo principal de cada stakeholder com este projeto?"
6. "Existe alguém que pode bloquear ou resistir ao projeto? Por quê?"

---

## Etapa 3 — Problema em Profundidade

7. "Como o problema é resolvido hoje?"
8. "Qual é o impacto financeiro ou operacional do problema não resolvido?"
9. "O que seria uma solução ideal na visão dos stakeholders?"
10. "Há tentativas anteriores de resolver esse problema? O que não funcionou?"

> **Lição aplicada [DISCOVERY]:** Se resposta for vaga, perguntar "quanto custa isso por mês?" antes de continuar.

---

## Etapa 4 — Restrições

11. "Há tecnologias obrigatórias ou proibidas?"
12. "Há integrações com sistemas existentes obrigatórias?"
13. "Há restrições regulatórias? (LGPD, PCI, SOC2, setor regulado?)"
14. "Qual é o prazo indicativo e o orçamento disponível?"

---

## Etapa 5 — Métricas

15. "Como o sucesso será medido em 6 meses? Quais são os KPIs?"
16. "Qual é o mínimo aceitável para o MVP?"

---

## Etapa 6 — Geração do Artefato

Preencher template: `templates/discovery/discovery-report.md`  
Substituir todos os `{PLACEHOLDER}` com dados coletados.

---

## Etapa 7 — SAVE (via artifact-manager)

```
1. Write: docs/output/{projeto}/discovery-report.md

2. Write: docs/output/{projeto}/memory.md (update)
   → Preencher: Contexto Acumulado (problema, stakeholders, restrições)
   → Preencher: Handoff Discovery → PRD:
     "Problema: {resumo}
      RQs levantados: {N}
      Restrições críticas: {lista}
      Prazo/orçamento: {valores}
      KPIs: {lista}"
   → Preencher: Perguntas Respondidas (todas as 16)
   → Preencher: Artefatos Produzidos → discovery-report.md = ✅
   → Preencher: Decisões Tomadas (restrições fixas)
   → Atualizar: fase_atual = "prd"

3. Write: pipeline-state.json
   → phases.discovery.status = "completed"
   → phases.discovery.completed_at = {ISO_DATE}
   → phases.discovery.artifact_exists = true
   → phases.discovery.handoff_written = true
   → current_phase = "prd"
   → memory.questions_answered = 16
```

---

## Validação antes de SAVE

Checar: `agents/discovery.md § Checklist de Qualidade` (7/7 para GO)

---

## Comunicar ao usuário

```
Discovery concluído ✅

Problema: {1 linha}
Stakeholders: {N} identificados
Requerimentos de alto nível: {N}
Riscos críticos: {lista breve}

Artefatos salvos:
  ✅ docs/output/{projeto}/discovery-report.md
  ✅ docs/output/{projeto}/memory.md (iniciado)
  ✅ docs/output/{projeto}/pipeline-state.json

Próxima fase: @prd *generate
```
