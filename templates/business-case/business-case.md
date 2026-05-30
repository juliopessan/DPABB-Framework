# Business Case — {PROJETO}

**Versão:** 1.0  
**Data:** {DATA}  
**Autor:** @business-case (Biz)  
**Status:** Draft | In Review | Approved  
**Recomendação:** GO ✅ | GO com ressalvas ⚠️ | REVISÃO 🔄 | NO-GO ❌

---

## 1. Resumo Executivo

> **Máximo 1 página.** O que, por que, quanto custa, qual é o retorno e a recomendação.

**Problema:** ...  
**Solução Proposta:** ...  
**Investimento Total:** R$ ___  
**Retorno Esperado:** R$ ___ (em ___ meses)  
**Payback Period:** ___ meses  
**ROI (24 meses):** ___%  
**Recomendação:** {GO / GO com ressalvas / REVISÃO / NO-GO}

---

## 2. Contexto e Problema

> Síntese do discovery. Referência cruzada com `discovery-report.md`.

---

## 3. Solução Proposta

> Síntese da arquitetura e backlog. Referência cruzada com `architecture.md` e `backlog.md`.

**Escopo MVP:** ...  
**Sprints estimados:** ___  
**Time necessário:** ___

---

## 4. Análise de Custo

### 4.1 Desenvolvimento

| Fase | Story Points | Custo/Ponto (R$) | Total (R$) |
|------|-------------|-----------------|-----------|
| Sprint 1 (MVP) | | R$ | |
| Sprint 2-N | | R$ | |
| **Total Desenvolvimento** | | | **R$** |

> **Premissa:** custo/ponto = (salário médio dev × fator encargos) / velocidade média

### 4.2 Infraestrutura e Operação (mensal)

| Item | Custo Mensal (R$) | Custo Anual (R$) |
|------|------------------|-----------------|
| Cloud computing | | |
| Banco de dados | | |
| CDN e storage | | |
| Monitoramento | | |
| Licenças de ferramentas | | |
| **Total OpEx** | **R$** | **R$** |

### 4.3 Custos Adicionais

| Item | Valor (R$) | Recorrência |
|------|-----------|------------|
| Treinamento | | One-time |
| Integração / Migração | | One-time |
| Suporte pós-lançamento | | Mensal |

### 4.4 Resumo de Custo Total

| Período | Custo (R$) |
|---------|-----------|
| CapEx (desenvolvimento) | |
| OpEx Ano 1 | |
| OpEx Ano 2 | |
| **TCO 24 meses** | **R$** |

---

## 5. Análise de Benefícios

### 5.1 Benefícios Quantificáveis

| Benefício | Cálculo | Valor Anual (R$) | Fonte |
|-----------|---------|-----------------|-------|
| Receita nova | {premissa do modelo} | | |
| Economia de processo | X horas/mês × R$/hora | | |
| Redução de churn | X clientes × ticket médio | | |
| Multas evitadas (compliance) | | | |
| **Total Benefícios/Ano** | | **R$** | |

### 5.2 Benefícios Qualitativos

- **Reputação:** ...
- **Compliance:** ...
- **Vantagem competitiva:** ...
- **Capacitação da equipe:** ...

---

## 6. ROI e Payback

### Fluxo de Caixa Projetado

| Mês | Investimento (R$) | Benefício (R$) | Saldo Acumulado (R$) |
|-----|-----------------|---------------|---------------------|
| 0 (dev) | | | |
| 1 | | | |
| 6 | | | |
| 12 | | | |
| 24 | | | |

### Indicadores

| Indicador | Valor | Interpretação |
|-----------|-------|--------------|
| ROI (24m) | ___% | |
| Payback Period | ___ meses | |
| NPV (taxa 1%/mês) | R$ ___ | |
| Break-even | Mês ___ | |

### Análise de Sensibilidade

| Cenário | Premissa | ROI | Payback |
|---------|---------|-----|---------|
| Pessimista (-30% benefícios) | | | |
| Base | | | |
| Otimista (+20% benefícios) | | | |

---

## 7. Análise de Risco

| Risco | Prob | Impacto | Severidade | Mitigação | Custo de Mitigação |
|-------|------|---------|------------|----------|-------------------|
| Atraso no desenvolvimento | Média | Alto | ALTO | Buffer de 20% no cronograma | +R$ |
| Adoção baixa pelos usuários | Baixa | Alto | MÉDIO | Piloto com grupo beta | |
| Mudança regulatória | Baixa | Médio | BAIXO | Monitoramento trimestral | |

---

## 8. Alternativas Consideradas

### Alternativa A: Build (Recomendada)
- **Custo:** R$ ___
- **Prós:** Controle total, customização, sem vendor lock-in
- **Contras:** Custo inicial alto, tempo de desenvolvimento

### Alternativa B: Buy (SaaS)
- **Custo:** R$ ___ /mês
- **Prós:** Time-to-market curto, OpEx previsível
- **Contras:** Vendor lock-in, customização limitada, {funcionalidade crítica} não disponível

### Alternativa C: Não fazer nada
- **Custo:** R$ 0 (inicial)
- **Impacto:** {impacto do problema persistindo} = R$ ___ em perdas anuais

---

## 9. Recomendação

**Decisão:** {GO / GO com ressalvas / REVISÃO / NO-GO}

**Justificativa:**
- ROI de ___% em 24 meses é {acima / abaixo} do threshold da empresa (___%)
- Payback de ___ meses é {aceitável / elevado} para o perfil de risco
- Riscos são {mitigáveis / críticos}
- Alternativas buy avaliadas são {inferiores / superiores} por {razão}

**Condições (se GO com ressalvas):**
1. 
2. 

---

## 10. Próximos Passos

| Ação | Responsável | Prazo |
|------|------------|-------|
| Aprovação do business case | Sponsor | |
| Formação do time | RH / Gerência | |
| Sprint 1 kick-off | Tech Lead | |
| Review do MVP | Stakeholders | |

---

## Histórico de Versões

| Versão | Data | Autor | Mudança |
|--------|------|-------|---------|
| 1.0 | {DATA} | @business-case | Versão inicial |
