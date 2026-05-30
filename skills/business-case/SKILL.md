# Skill: Business Case Analysis

**Agente:** @business-case (Biz)  
**Trigger:** `*analyze`  
**Input:** memory.md (handoffs de todas as fases) + backlog.md  
**Output:** `docs/output/{projeto}/business-case.md`

---

## Pré-condições

- [ ] Todas as 4 fases anteriores `status = "completed"`
- [ ] `backlog.md` com total de story points e métricas

---

## Etapa 0 — LOAD (via artifact-manager)

```
1. Read: docs/output/{projeto}/pipeline-state.json
   → Verificar todas as fases = "completed"
   → Extrair métricas do backlog (total SPs, sprint1 pts, sprints estimados)

2. Read: docs/output/{projeto}/memory.md
   → Extrair TODOS os handoffs (fonte primária de contexto)
   → Extrair KPIs do discovery (métricas de sucesso)
   → Extrair impacto financeiro do discovery
   → NÃO re-ler todos os artefatos — usar memory comprimido

3. Read: framework/lessons-learned.md
   → Grep: [BUSINESS-CASE] → aplicar lições

4. Write: pipeline-state.json
   → phases.business_case.status = "in_progress"
```

> **Economia de tokens:** O memory.md contém o contexto comprimido de 4 artefatos (~15.000 tokens → ~3.000 tokens de handoff).

---

## Etapa 1 — Coleta de Inputs Financeiros

Perguntar APENAS o que não estiver no memory:

1. "Qual é o custo médio por story point da equipe? (ou: custo/dia de um dev × velocidade)"
2. "Qual é o benefício primário quantificável? (receita nova, economia de custo, redução de churn?)"
3. "Qual é o threshold de ROI aceitável para a empresa? (ex: mínimo 15% em 24 meses)"
4. "Há um SaaS equivalente para comparar no build vs buy?" (se não mencionado no discovery)

> **Lição aplicada [BUSINESS-CASE]:** Perguntar fonte da premissa para cada benefício. Nunca aceitar estimativas sem base.

---

## Etapa 2 — Custo de Desenvolvimento

```
CapEx = Total_SPs × Custo_por_SP
Custo_por_SP = (Custo_diário_dev × Fator_encargos_1.7) / Velocidade_média
Buffer de risco = +20% sobre CapEx base
```

Criar tabela por fase (Sprint 1, Sprint 2-N, buffer).

---

## Etapa 3 — Custo Operacional

Estimar OpEx mensal baseado na arquitetura do memory:
- Cloud services (pelo tier escolhido na arquitetura)
- Licenças de ferramentas identificadas na stack
- Suporte pós-lançamento: 15% do CapEx ao ano

> Referenciar: "Stack: {do handoff Architect→Backlog}" para estimar cloud costs.

---

## Etapa 4 — Benefícios

Para cada KPI do discovery, quantificar:
```
Benefício A: {tipo}
  Cálculo: {premissa × quantidade × valor}
  Base: {fonte — entrevista/benchmark/histórico}
  Cenário pessimista (-30%): R$ {N}
  Cenário base: R$ {N}
  Cenário otimista (+20%): R$ {N}
```

> **Lição aplicada [BUSINESS-CASE]:** Benefícios sem fonte são marcados como "estimado sem base" no documento.

---

## Etapa 5 — ROI e Payback

Construir fluxo de caixa mês a mês (ao menos 24 meses):
```
ROI = (Benefícios_24m - TCO_24m) / TCO_24m × 100
Payback = primeiro mês onde saldo_acumulado > 0
NPV = Σ (benefício_t - custo_t) / (1.01)^t  [taxa 1%/mês]
```

Análise de sensibilidade: 3 cenários (pessimista / base / otimista).

---

## Etapa 6 — Riscos de Negócio

Para cada risco identificado no discovery e architecture:
- Calcular impacto financeiro em R$
- Classificar probabilidade × impacto
- Propor mitigação com custo estimado

---

## Etapa 7 — Alternativas

Sempre avaliar:
1. **Build** (proposta atual) — custo/benefício
2. **Buy/SaaS** — ferramenta equivalente + custo mensal
3. **Não fazer nada** — custo do status quo mantido

---

## Etapa 8 — Recomendação

| Critério | Valor | Threshold | Status |
|----------|-------|-----------|--------|
| ROI 24m | {N}% | > {threshold}% | ✅/❌ |
| Payback | {N} meses | < 24 meses | ✅/❌ |
| Alternativa superior? | Sim/Não | — | |
| Riscos mitigáveis? | Sim/Não | — | |

**Decisão:** GO / GO com ressalvas / REVISÃO / NO-GO

---

## Etapa 9 — SAVE (via artifact-manager)

```
1. Write: docs/output/{projeto}/business-case.md
   → Usando template: templates/business-case/business-case.md

2. Write: docs/output/{projeto}/memory.md (update)
   → Adicionar seção "Pipeline Concluído":
     "Data conclusão: {DATA}
      Recomendação: {GO/NO-GO}
      ROI 24m: {N}%
      Payback: {N} meses
      Investimento total: R$ {N}
      Tokens economizados (estimativa): {N}"
   → Atualizar Artefatos: business-case.md = ✅
   → Atualizar fase_atual: "done"

3. Write: pipeline-state.json
   → phases.business_case.status = "completed"
   → phases.business_case.recommendation = "{GO/NO-GO}"
   → phases.business_case.roi_24m = {N}
   → phases.business_case.payback_months = {N}
   → pipeline_status = "completed"
```

---

## Etapa 10 — ATUALIZAR LESSONS LEARNED (obrigatório)

```
Write: framework/lessons-learned.md (append)
```

Adicionar OBRIGATORIAMENTE:
1. **Padrões de Sucesso** — O que funcionou bem neste projeto
2. **Armadilhas** — O que causou atrito ou erros
3. **Linha na tabela "Melhorias Aplicadas"** com a versão e data
4. **Métricas do Framework** — Linha na tabela de métricas

> Esta etapa garante que o framework aprende com cada execução.

---

## Comunicar ao usuário

```
Pipeline Requirements Intel Concluído! 🎯

Projeto: {NOME}
Recomendação: {GO ✅ / GO com ressalvas ⚠️ / REVISÃO 🔄 / NO-GO ❌}

ROI 24 meses: {N}%
Payback: {N} meses  
Investimento total: R$ {N}
Benefícios 24m: R$ {N}

Artefatos gerados:
  ✅ docs/output/{projeto}/discovery-report.md
  ✅ docs/output/{projeto}/prd.md
  ✅ docs/output/{projeto}/architecture.md
  ✅ docs/output/{projeto}/backlog.md
  ✅ docs/output/{projeto}/business-case.md
  ✅ docs/output/{projeto}/memory.md (contexto completo do projeto)
  ✅ framework/lessons-learned.md (framework atualizado)

Tokens economizados (estimativa): ~{N} tokens vs cold start sem memory
```
