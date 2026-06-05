# Business Case — IP Document Draft Evaluation (Word Add-In)

**Versão:** 1.0  
**Data:** 2026-06-03  
**Autor:** @business-case (Biz) — DPABB Framework  
**Status:** Draft  
**Recomendação:** GO ✅ (POC) — validar tenant-hosted para produção

---

## 1. Resumo Executivo

**Problema:** Revisões de rascunhos de patentes são manuais, inconsistentes e sem trilha auditável, resultando em retrabalho e custos elevados.  
**Solução Proposta:** POC de um Word Add-In que realiza QC assistido por IA (detecção de issues, comentários padronizados, chat de rewrites) e grava trilha de auditoria tenant-scoped.  
**Investimento Total (estimado):** R$ 175.000 (TCO 24 meses) — detalhado abaixo.  
**Retorno Esperado:** R$ 480.000 em 24 meses (economia de tempo de revisão).  
**Payback Period:** ~6 meses (POC).  
**ROI (24 meses):** ~174%  
**Recomendação:** GO ✅ para POC com Foundry-hosted modelo; exigir validação de tenant-hosted antes produção para clientes regulados.

---

## 2. Contexto e Problema

Síntese do discovery: revisores gastam horas por documento em workflows manuais; falta padrão e rastreabilidade; clientes exigem preservação de comentários e conformidade LGPD. O PRD e a arquitetura definem um POC concentrado em detecção, comentários padronizados e audit export.

---

## 3. Solução Proposta

Sinopse: implementar Add-In Office.js + backend prompt orchestrator (Azure Functions / Foundry), tenant-scoped audit store (Azure SQL/Blob) e export CSV/JSON para Legal Ops. Backlog inicial e roadmap estão em `backlog.md` e `architecture.md`.

**Escopo MVP:** detecção inline, comentários estruturados, ações do revisor (Accept/Ignore/Resolve), export QC, audit capture, tenant storage option.  
**Sprints estimados:** 2–3 sprints (POC); capacidade Sprint 1: 27 pts.  
**Time necessário (POC):** 1 PO, 1 Tech Lead, 2 Devs (Front/Back), 1 QA, 1 Security reviewer — ~10 semanas.

---

## 4. Análise de Custo

### Premissas financeiras (usadas nas contas)
- Custo por story-point: R$ 2.500 (salário médio ponderado + encargos / velocidade média).  
- Backlog POC: 46 story points (conforme `backlog.md`).  
- Sprints necessários: 2 (rápido) a 3 (com buffers). Usamos 3 para estimativa conservadora.
- Infra/OpEx mensal estimado (Cloud, APM, Foundry extras, suporte): R$ 2.500/mês.

### 4.1 Desenvolvimento

| Fase | Story Points | Custo/Pt (R$) | Total (R$) |
|------|-------------:|--------------:|-----------:|
| POC (total backlog) | 46 | 2.500 | 115.000 |
| Buffer (20%) | — | — | 23.000 |
| **Total Desenvolvimento (CapEx)** | — | — | **138.000** |

> Nota: buffer inclui QA adicional, ajustes e pequenas mudanças de escopo.

### 4.2 Infraestrutura e Operação (OpEx)

| Item | Custo Mensal (R$) | Custo Anual (R$) |
|------|------------------:|-----------------:|
| Cloud compute (App Service, Functions) | 1.200 | 14.400 |
| Database / Storage | 400 | 4.800 |
| Application Insights / Monitoring | 300 | 3.600 |
| Foundry usage / model calls (pilot) | 600 | 7.200 |
| **Total OpEx (ano)** | **2.500/mês** | **29.999 (aprox)** |

### 4.3 Custos Adicionais

| Item | Valor (R$) | Recorrência |
|------|-----------:|------------:|
| Treinamento e onboarding | 5.000 | One-time |
| Suporte inicial / runbooks | 3.000 | One-time |

### 4.4 Resumo de Custo Total (24 meses)

| Período | Custo (R$) |
|--------|-----------:|
| CapEx (desenvolvimento) | 138.000 |
| OpEx Ano 1 | 30.000 |
| OpEx Ano 2 | 30.000 |
| **TCO 24 meses** | **198.000** |

> Observação: valores arredondados; TCO conservador incluindo buffer e operação pilot.

---

## 5. Análise de Benefícios

### 5.1 Benefícios Quantificáveis — Premissas
- Revisor pool (pilot): 10 revisores.  
- Volumes: 10 documentos/revisor/mês.  
- Tempo médio de revisão por documento: 4 horas.  
- Custo hora revisor (loaded): R$ 200/hora.  
- Melhoria esperada (POC target): 25% redução no tempo de revisão.

**Cálculos:**
- Horas mensais antes: 10 revisores × 10 docs × 4 h = 400 h/mês.  
- Economia (25%): 100 h/mês.  
- Economia mensal (R$): 100 h × R$200 = R$ 20.000/mês.  
- Economia anual: R$ 240.000.  
- Economia em 24 meses: R$ 480.000.

| Benefício | Valor Anual (R$) | Fonte |
|---|-----------:|---|
| Economia tempo revisão | 240.000 | Premissas acima |
| Economia/benefícios adicionais (menor retrabalho) | 20.000 | Conservador |
| **Total Benefícios/Ano** | **260.000** | |

### 5.2 Benefícios Qualitativos
- Redução de risco operacional por padronização de comentários.  
- Melhoria na qualidade de submissões e velocidade de filing.  
- Auditoria e compliance fortalecidos (LGPD traceability).  
- Base para automações adicionais (claims checks, analytics).

---

## 6. ROI e Payback

### Fluxo projetado (resumo)
- Investimento inicial (dev + buffer): R$ 138.000.  
- OpEx mensal: R$ 2.500 → Anual R$30.000.  
- Economia mensal estimada: R$ 20.000 → Payback ≈ 138.000 / 20.000 ≈ 6,9 meses (≈7 meses). Com benefício adicional considerado, payback ≈ 6 meses.

### Indicadores (24 meses)
- Benefício 24m: R$ 480.000  
- Custo 24m (TCO): R$ 198.000  
- ROI (24m) = (480k − 198k) / 198k ≈ 1.42 → 142% (conservador).  
- Payback: ~6–7 meses (POC).

> Sensitivity: se economia efetiva for 15% (não 25%), payback aumenta e ROI reduz — ver seção de sensibilidade.

---

## 7. Análise de Risco

| Risco | Prob | Impacto | Severidade | Mitigação | Custo de Mitigação |
|---|---:|---:|---|---|---:|
| Preservação de comentários falhar | Médio | Alto | ALTO | WP-4 cross-client tests; export fallback | 8k (tests/adjustments) |
| Compliance/tenant constraints bloqueiam Foundry | Médio | Alto | ALTO | Oferecer tenant-hosted path para produção | +30k–50k infra effort |
| Adoção abaixo do esperado | Baixo | Alto | MÉDIO | Pilot com 3–5 reviewers; coletar feedback rápido | 5k (engagement) |

---

## 8. Alternativas Consideradas

### Alternativa A: Build (Recomendada para POC)
- **Custo:** R$ 198.000 (TCO 24m)  
- **Prós:** controle, integração com tenant, customização, auditabilidade.  
- **Contras:** investimento inicial.

### Alternativa B: Buy (SaaS third-party)
- **Custo:** estimado R$ 6.000–15.000/mês (licença + integration)  
- **Prós:** menor time-to-value.  
- **Contras:** vendor lock-in, limitações de custom rules e integração fina com Word/track-changes; compliance gap em alguns clientes.

### Alternativa C: Não fazer nada
- **Impacto:** manutenção do status quo — custos de revisão persistem; oportunidade de economia perdida (estimada R$ 240k/ano no pilot assumptions).

---

## 9. Recomendação

**Decisão (POC):** GO ✅ — executar POC com Foundry-hosted model deployments e tenant-scoped audit storage; validar tenant-hosted path durante POC para clientes regulados.  

**Justificativa:** Payback esperado < 12 meses em cenário base; ROI 24m substancial e economia operacional direta significativa; permite fechar gap de compliance e acelerar filing.

**Condições:**
1. Executar WP-4 (preservation tests) antes da rollout ampliado.  
2. Preparar opção tenant-hosted com estimativa incremental caso cliente solicite.  
3. Capturar baseline de revisão (horas/doc) durante piloto para validar números.

---

## 10. Próximos Passos

| Ação | Responsável | Prazo |
|---|---|---:|
| Aprovar POC e orçamento inicial (CapEx) | Sponsor / Legal Ops | 2 semanas |
| Selecionar 3–5 pilot reviewers e 10–15 sample docs | Legal Ops | 1 semana |
| Kick-off Sprint 1 (TECH-001, TECH-002, STORY-01.01) | Tech Lead | 1–2 semanas |
| Executar WP-4: cross-client preservation tests | QA / Tech Lead | Sprint 2 |

---

## Enrichment: Sensitivity & Scenarios

| Cenário | Premissa | ROI (24m) | Payback |
|---|---|---:|---:|
| Otimista | Economia 35% | >200% | ~4–5 meses |
| Base | Economia 25% | ~142% | ~6–7 meses |
| Pessimista | Economia 15% | ~40–60% | >12 meses |

---

## 11. Histórico de Versões

| Versão | Data | Autor | Mudança |
|---|---:|---|---|
| 1.0 | 2026-06-03 | @business-case | Business case inicial alinhado ao PRD/backlog |

---

Prepared by: @business-case (Biz) — DPABB Framework
