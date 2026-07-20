# Business Case — Intelligent Operations Document Platform

**Versão:** 1.0
**Data:** 2026-07-19
**Autor:** @business-case (Biz)
**Inputs:** [discovery-report.md](discovery-report.md) · [prd.md](prd.md) · [architecture.md](architecture.md) · [backlog.md](backlog.md)
**Status:** Draft

> **Nota de escopo:** todos os valores monetários abaixo são **estimativas ilustrativas** construídas a partir do Team Plan da Arquitetura (§E.7) e de premissas de volume do RFP-001 (fictício, para teste do framework). Nenhum custo foi validado com fornecedor real ou com a organização — devem ser tratados como baseline de discussão, não como orçamento aprovado.

---

## 1. Resumo Executivo

A Northstar processa hoje ~180.000 páginas, 65.000 e-mails e 12.000 documentos estruturados/mês de forma manual e fragmentada, sem rastreabilidade de fonte-a-ação nem controle formal sobre uso de IA em decisão material. O investimento estimado de piloto (~R$ 827 mil em custo único + ~R$ 540 mil/ano recorrente) é justificado por uma economia projetada de ~R$ 1,0 milhão/ano em retrabalho e re-digitação manual nos documentos em escopo do piloto, com payback estimado em ~14-16 meses. Dado o requisito não-negociável de gate humano para dado crítico (FR-012), o risco de governança de IA é mitigado por design. **Recomendação: GO com ressalvas**, condicionado à confirmação do sistema financeiro/CRM alvo e dos volumes reais na Discovery detalhada.

---

## 2. Contexto e Problema

Ver `discovery-report.md § 4`. Processo manual gera re-digitação, classificação inconsistente, ageing de exceções sem dono e risco de decisão material baseada em IA não revisada.

---

## 3. Solução Proposta

Pipeline event-driven modular (architecture.md, ADR-001) com: intake governado (mailbox + SharePoint), classificação/extração híbrida (Document Intelligence + Azure OpenAI + regras determinísticas — ADR-002), gate obrigatório de aprovação humana para campo crítico (FR-012), roteamento a 1 sistema financeiro e 1 CRM, e auditoria imutável fonte-a-ação. Entrega em 6 Epics / 176 pontos, com Sprint 1 de fundação + intake (47 pts).

---

## 4. Análise de Custo

### 4.1 Desenvolvimento

```
Custo Dev = Custo de Equipe (Team Plan) para 176 pts ao longo de ~6 meses de construção
          = R$ 597.000 (architecture.md § E.7)
```

| Item | Valor (R$) |
|------|-----------:|
| Equipe de entrega (PO, Arquiteto, Tech Lead, Devs, AI/ML, QA, DevOps, Data Eng, SM) | 597.000 |
| Discovery e design detalhados (7 semanas, parte já coberta na equipe acima) | incluído |
| **Subtotal Desenvolvimento** | **597.000** |

### 4.2 Infraestrutura e Operação

| Item | Custo mensal estimado (R$) | Observação |
|------|---------------------------:|------------|
| Azure OpenAI + Document Intelligence (consumo) | 12.000 | Volume de piloto, escala com documentos processados |
| App Service + Functions + Service Bus | 8.000 | Múltiplos ambientes (dev/test/prod) |
| Cosmos DB + SQL Database + Blob Storage | 9.000 | Retenção e redundância geo |
| Monitor + App Insights + Key Vault | 3.000 | Observabilidade e segurança |
| **Subtotal mensal (produção + ambientes não-produtivos)** | **32.000** | ~R$ 384.000/ano |

Durante os ~6 meses de build (ambientes dev/test/staging ativos): **~R$ 150.000** (tiers reduzidos antes do go-live).

### 4.3 Licenças e Ferramentas

| Item | Custo | Observação |
|------|------:|------------|
| Azure DevOps (licenças de equipe) | incluído no custo de equipe | |
| Suporte e manutenção pós-go-live (15% do custo de dev/ano) | R$ 89.550/ano | Estimativa padrão de mercado |
| Treinamento e handover | R$ 40.000 (único) | Materiais + sessões com reviewers |
| Testes de segurança e privacidade (assurance) | R$ 30.000 (único) | Pentest + revisão de privacidade pré-go-live |

### 4.4 Consolidado

| Categoria | Custo único (R$) | Custo recorrente anual (R$) |
|-----------|------------------:|------------------------------:|
| Desenvolvimento (equipe) | 597.000 | — |
| Infraestrutura durante build | 150.000 | — |
| Treinamento e handover | 40.000 | — |
| Testes de segurança/privacidade | 30.000 | — |
| Infraestrutura em produção | — | 384.000 |
| Suporte e manutenção | — | 89.550 |
| **Total** | **817.000** | **473.550** |

**TCO 3 anos (indicativo):** 817.000 + 3 × 473.550 = **R$ 2.237.650**

---

## 5. Análise de Benefícios

### 5.1 Benefícios Quantificáveis

Premissa: ~20.000 documentos/mês em escopo do piloto (faturas de fornecedor, evidências de pagamento, anexos de solicitação de cliente) atualmente processados manualmente a ~8 minutos/documento (classificação + re-digitação), custo totalmente carregado de ~R$ 45/hora.

```
Custo manual atual = 20.000 docs × (8/60)h × R$45/h ≈ R$ 120.000/mês
Redução esperada com automação (70% dos documentos com alta confiança, roteamento automático) ≈ R$ 84.000/mês
Economia anualizada ≈ R$ 1.008.000/ano
```

| Benefício | Estimativa anual (R$) |
|-----------|------------------------:|
| Redução de re-digitação e retrabalho manual | 1.008.000 |
| Redução de tempo de resolução de exceções (ageing) | Qualitativo (não quantificado nesta fase) |
| Redução de disputa/tempo de auditoria via lineage completo | Qualitativo |

### 5.2 Benefícios Qualitativos

- Rastreabilidade fonte-a-ação para 100% dos registros aprovados (mitiga risco regulatório).
- Visibilidade operacional única (dashboards) para tomada de decisão do scale-up.
- Arquitetura reutilizável para novos domínios pós-piloto (contratos, novos ERPs/CRMs).
- Redução do risco de decisão material baseada em IA não revisada (controle de governança, não só eficiência).

---

## 6. ROI e Payback

```
Investimento Ano 1 = Custo único + Custo recorrente ano 1
                    = R$ 817.000 + R$ 473.550 = R$ 1.290.550

Benefício Ano 1 (ramp-up: 50% nos primeiros 6 meses de piloto, 100% nos 6 meses seguintes)
                    ≈ 0,5 × 504.000 + 1,0 × 504.000 = R$ 756.000

ROI Ano 1 = (Benefício - Investimento) / Investimento
          = (756.000 - 1.290.550) / 1.290.550 ≈ -41% (Ano 1, esperado — investimento concentrado no piloto)

Payback simples (considerando só o custo único de R$ 817.000 contra benefício mensal pleno de R$ 84.000)
          ≈ 817.000 / 84.000 ≈ 9,7 meses após go-live plenamente rampado
          ≈ ~14-16 meses corridos desde o início do projeto (considerando ramp-up gradual)
```

**NPV (3 anos, taxa de desconto 10% a.a., benefício pleno a partir do mês 12):**

| Ano | Fluxo líquido (R$) | Fator desconto | Valor presente (R$) |
|-----|--------------------:|:---------------:|----------------------:|
| 0 (build) | -817.000 | 1,00 | -817.000 |
| 1 | 756.000 - 473.550 = 282.450 | 0,91 | 257.030 |
| 2 | 1.008.000 - 473.550 = 534.450 | 0,83 | 443.594 |
| 3 | 1.008.000 - 473.550 = 534.450 | 0,75 | 400.838 |
| **NPV** | | | **R$ 284.462** |

> NPV positivo em 3 anos sob as premissas acima — favorável, mas sensível ao volume real validado em Discovery (ver §7).

### Análise de Sensibilidade

| Cenário | Premissa | ROI Ano 1 | Payback (meses) | NPV 3 anos (R$) |
|---------|----------|:---:|:---:|---:|
| Pessimista (-30% benefícios / volume real 50% do estimado) | 10.000 docs/mês em vez de 20.000 | -58% | ~24 | -66.000 (negativo) |
| Base (conforme premissas acima) | 20.000 docs/mês, 70% automação | -41% | ~15 | 284.462 |
| Otimista (+20% benefícios / volume real 100-120% do estimado) | 24.000 docs/mês, 75% automação | -30% | ~11 | 612.000 |

> No cenário pessimista o NPV se torna negativo — reforça a ressalva de validar volume real antes de comprometer o orçamento pleno (§9, ressalva 2).

---

## 7. Análise de Risco

| Risco de Negócio | Probabilidade | Impacto | Classificação | Mitigação |
|-------------------|:---:|:---:|:---:|-----------|
| Volume real de documentos menor que a premissa (20k/mês) | Média | Alto | ALTO | Validar volume real na Discovery antes de aprovar orçamento final |
| Acurácia de extração insuficiente (< 85%) eleva revisão manual e reduz economia | Média | Alto | ALTO | Golden-set de testes por idioma/tipo desde Sprint 2 (RT-02 na arquitetura) |
| Atraso na definição do sistema financeiro/CRM alvo posterga benefícios de roteamento (RT-01) | Alta | Alto | CRÍTICO | Priorizar decisão no kick-off; sequenciar backlog para não bloquear entrega de valor de intake/classificação |
| Achado crítico de segurança/privacidade no pré-go-live atrasa lançamento | Média | Alto | ALTO | Test de segurança e privacidade dedicado antes do go-live (§4.3) |
| Resistência de adoção por reviewers | Baixa | Médio | MÉDIO | Treinamento dedicado e meta de 80% de proficiência pós-treinamento (critério de sucesso do PRD) |

---

## 8. Alternativas Consideradas

| Critério | Build (proposto) | Buy (RPA/IDP de prateleira) | Integrate (BPO terceirizado) |
|----------|:---:|:---:|:---:|
| Controle sobre gate de governança de IA (FR-012) | Alto | Baixo — produtos de prateleira raramente expõem gate customizável | Baixo — depende do fornecedor de BPO |
| Custo inicial | Alto (R$ 817k) | Médio (licenciamento + configuração) | Baixo (custo variável por volume) |
| Time-to-market | ~6 meses até piloto | Potencialmente mais rápido, mas com customização limitada | Rápido, mas sem ganho estrutural de longo prazo |
| Rastreabilidade fonte-a-ação nativa | Alta (requisito de design) | Depende do produto — frequentemente limitada | Baixa — depende de relatórios do fornecedor |
| Vendor lock-in | Nenhum (arquitetura própria sobre Azure) | Alto (produto proprietário) | Alto (dependência operacional do BPO) |
| Reuso para domínios futuros (contratos, etc.) | Alto | Médio | Baixo |

**Conclusão:** Build é recomendado porque o requisito não-negociável de governança de IA (FR-005/FR-012, RQ-005) e de rastreabilidade fonte-a-ação (NFR-010) dificilmente são atendidos de forma auditável por produtos de prateleira ou por BPO terceirizado sem visibilidade de processo.

---

## 9. Recomendação

**GO com ressalvas.**

Justificativa: NPV positivo em 3 anos (R$ 284.462) e payback ~14-16 meses estão dentro de faixa aceitável para um investimento com forte componente de governança/compliance (não apenas eficiência). As ressalvas obrigatórias antes de comprometer o orçamento final:

1. Confirmar sistema financeiro e CRM alvo (risco RT-01/CRÍTICO) — sem isso, WP-05 e parte dos benefícios quantificados (roteamento automático) não se realizam no prazo.
2. Validar os volumes reais de documentos em escopo (atualmente estimativa do RFP, não medida) antes de aprovar o dimensionamento final de capacidade (NFR-004) e o cálculo de benefício.
3. Confirmar dados de categoria especial/sensível na amostra antes de finalizar a política de retenção (C1.4 da arquitetura).

---

## 10. Próximos Passos

- [ ] Validar premissas de volume e sistemas alvo na Discovery aprofundada com os stakeholders de Finance e Customer Operations
- [ ] Aprovar orçamento de piloto (~R$ 817k único + ~R$ 474k/ano) sujeito às ressalvas acima
- [ ] Iniciar Sprint 0/Foundation (EPIC-00) em paralelo à confirmação das integrações
- [ ] Definir baseline real de acurácia/tempo de processamento manual antes do go-live para medir os critérios de sucesso do PRD §10

---

## Handoff → Lessons Learned

Ver atualização em `framework/lessons-learned.md` (2 lições adicionadas nesta execução).

---

## Histórico de Versões

| Versão | Data | Autor | Mudança |
|--------|------|-------|---------|
| 1.0 | 2026-07-19 | @business-case (Biz) | Versão inicial — GO com ressalvas, NPV 3 anos positivo |
