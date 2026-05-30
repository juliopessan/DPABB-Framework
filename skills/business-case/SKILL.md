# Skill: Business Case Analysis

**Agente:** @business-case (Biz)  
**Trigger:** `*analyze`  
**Input:** Todos os docs de fases anteriores  
**Output:** `docs/output/{projeto}/business-case.md`

---

## Objetivo

Consolidar todos os outputs do pipeline em uma análise de negócio que quantifica o investimento, os benefícios e emite uma recomendação GO/NO-GO fundamentada.

---

## Pré-condições

- [ ] `discovery-report.md` aprovado
- [ ] `prd.md` aprovado
- [ ] `architecture.md` aprovado
- [ ] `backlog.md` aprovado com estimativas

---

## Perguntas de Input Necessárias

Antes de calcular, coletar do usuário:

1. "Qual é o custo médio por story point da equipe? (ou custo diário dos devs e velocidade do time)"
2. "Qual é o benefício primário quantificável que este projeto gera? (receita nova? economia de custo? redução de churn?)"
3. "Há benchmark de mercado para comparar? (ex: solução SaaS similar)"
4. "Qual é o threshold de ROI aceitável para a empresa? (ex: mínimo 15% em 24 meses)"

---

## Execução

### Etapa 1 — Consolidação
Ler todos os docs e extrair:
- Total de story points (backlog)
- Complexidade técnica (architecture)
- Principais riscos (discovery + architecture)
- KPIs de sucesso (discovery + prd)

### Etapa 2 — Custo de Desenvolvimento
```
CapEx = Total_SPs × Custo_por_SP
Custo_por_SP = (Custo_diário_dev × Fator_encargos) / Velocidade_média
```
Adicionar buffer de 20% para riscos.

### Etapa 3 — Custo Operacional
Estimar OpEx mensal por categoria:
- Cloud: baseado na arquitetura proposta
- Licenças: ferramentas identificadas na stack
- Suporte: 15-20% do CapEx ao ano

### Etapa 4 — Benefícios
Para cada benefício do discovery/prd:
- Quantificar em R$
- Documentar premissa e fonte
- Classificar como conservador/base/otimista

### Etapa 5 — ROI e Payback
```
ROI = (Benefícios_totais - TCO) / TCO × 100
Payback = mês em que Saldo_Acumulado > 0
NPV = Σ (Benefício_mês - Custo_mês) / (1 + taxa)^mês
```
Taxa desconto sugerida: 1% ao mês (12% ao ano)

### Etapa 6 — Análise de Sensibilidade
Calcular ROI e payback para:
- Cenário pessimista: benefícios -30%, custos +20%
- Cenário base: premissas base
- Cenário otimista: benefícios +20%, custos base

### Etapa 7 — Riscos de Negócio
Para cada risco do discovery/architecture:
- Classificar probabilidade × impacto financeiro
- Propor mitigação com custo estimado

### Etapa 8 — Alternativas
Sempre comparar:
- Build (proposta atual)
- Buy (SaaS equivalente ou mais próximo)
- Não fazer nada (custo de status quo)

### Etapa 9 — Recomendação
Baseada em critérios objetivos:
- ROI vs threshold da empresa
- Payback period aceitável
- Riscos mitigáveis
- Alternativas superiores?

### Etapa 10 — Resumo Executivo
1 página máxima com: problema, solução, investimento, retorno, recomendação.

### Etapa 11 — Geração
Preencher template: `templates/business-case/business-case.md`  
Salvar em: `docs/output/{projeto}/business-case.md`

---

## Pipeline Completo

Ao finalizar, comunicar:

```
Pipeline requirements-intel concluído! 🎯

Projeto: {NOME}
Recomendação: GO ✅ | GO com ressalvas ⚠️ | REVISÃO 🔄 | NO-GO ❌

Documentos gerados:
✅ docs/output/{projeto}/discovery-report.md
✅ docs/output/{projeto}/prd.md
✅ docs/output/{projeto}/architecture.md
✅ docs/output/{projeto}/backlog.md
✅ docs/output/{projeto}/business-case.md

ROI estimado: ___% em 24 meses
Payback: ___ meses
Investimento total: R$ ___
```
