# Memory — Intelligent Operations Document Platform

> Arquivo gerado e atualizado automaticamente pelo pipeline.
> **Propósito:** Evitar re-elicitação de contexto já coletado. Cada agente lê este arquivo antes de agir.
> **Regra:** Nunca deletar entradas. Apenas adicionar ou marcar como `[SUPERADO]`.

---

## Meta

| Campo | Valor |
|-------|-------|
| Projeto | intelligent-operations-document-platform |
| Início do pipeline | 2026-07-19 |
| Última atualização | 2026-07-19 |
| Fase atual | done |
| Token savings estimado | N/A — execução em sessão única contínua (todas as fases lidas em contexto compartilhado) |

---

## Contexto Acumulado

### Sobre o Projeto
- **Problema:** Intake e processamento de documentos operacionais (faturas, evidências de pagamento, anexos de solicitação de cliente) fragmentado entre mailbox/SharePoint/planilhas, sem rastreabilidade nem gate de governança de IA.
- **Solução proposta:** Pipeline event-driven modular (intake → classificação/extração híbrida IA+regras → gate de confiança/aprovação humana → roteamento → auditoria imutável), Azure, TOGAF COMPLEX (19/25).
- **Stakeholders chave:** COO (sponsor), Finance Ops Director, Customer Ops Director, Enterprise Architect, DPO, InfoSec Lead, Platform Engineering.
- **Prazo e orçamento:** ~25 semanas indicativas (RFP §18); orçamento não fixado — estimado em ~R$ 817k único + ~R$ 474k/ano recorrente no Business Case.

### Restrições Fixas (não negociáveis)
- **Tecnológicas:** ambiente preferencial Azure; ambientes dev/test/prod segregados; nenhuma decisão material apenas por IA generativa não revisada (FR-012).
- **Regulatórias:** residência de dados UK/UE-EEA, minimização/retenção configurável, sem PII em log de diagnóstico por padrão, WCAG 2.1 AA.
- **Organizacionais:** piloto restrito a Finance Operations e Customer Operations; fora de escopo substituição de ERP/CRM, aprovação financeira autônoma, interpretação jurídica autônoma.

### Decisões Tomadas (não reabrir)
| Decisão | Fase | Razão | Alternativas rejeitadas |
|---------|------|-------|----------------------------|
| Estilo arquitetural event-driven modular (ADR-001) | architect | COMPLEX (19/25), precisa de assíncrono + retry sem overhead de microservices plenos | Monolito modular síncrono; Microservices completos K8s |
| Classificação/extração híbrida IA+regras (ADR-002) | architect | RQ-005 exige que LLM nunca decida roteamento sozinho | Somente LLM genérico; somente OCR+regras sem IA generativa |
| Entra ID + RBAC + Conditional Access (ADR-003) | architect | NFR-007, federação com IdP corporativo existente | Auth própria customizada; IdP terceiro fora do ecossistema Microsoft |
| Build (não Buy/Integrate) recomendado | business-case | Gate de governança de IA e rastreabilidade fonte-a-ação dificilmente atendidos por produto de prateleira/BPO | Buy (RPA/IDP de prateleira); Integrate (BPO terceirizado) |

---

## Artefatos Produzidos

| Artefato | Caminho | Status | Hash/Versão |
|----------|---------|--------|-------------|
| discovery-report.md | `docs/output/intelligent-operations-document-platform/discovery-report.md` | ✅ | v1.0 |
| prd.md | `docs/output/intelligent-operations-document-platform/prd.md` | ✅ | v1.0 |
| architecture.md | `docs/output/intelligent-operations-document-platform/architecture.md` | ✅ | v1.0 |
| backlog.md | `docs/output/intelligent-operations-document-platform/backlog.md` | ✅ | v1.0 |
| business-case.md | `docs/output/intelligent-operations-document-platform/business-case.md` | ✅ | v1.0 |

---

## Handoffs Entre Fases

### Discovery → PRD
```
Problema central: intake fragmentado, re-digitação, sem rastreabilidade, risco de IA não revisada.
12 requerimentos de alto nível (RQ-001 a RQ-012) mapeados diretamente do RFP-001.
Questões abertas críticas: sistema financeiro e CRM alvo NÃO definidos (bloqueia parte do roteamento).
```

### PRD → Architect
```
21 FRs, 15 NFRs definidos, todos rastreáveis a RQ-XXX.
NFRs críticos para arquitetura:
- NFR-001: disponibilidade ≥99,5%
- NFR-003: processamento ≤60s p95
- NFR-004: ≥250k páginas/mês sem redesenho
- NFR-010: lineage 100% dos aprovados
FR-012 é o requisito de governança mais crítico: gate humano obrigatório para campo crítico.
```

### Architect → Backlog
```
Componentes principais: Intake Orchestration, Classification & Extraction, Validation Work Queue,
Routing Service, Audit & Provenance Store, Dashboards.
Complexidade: COMPLEX (19/25).
Stories técnicas obrigatórias: Entra ID/RBAC, Key Vault, Service Bus+dead-letter,
Audit Store append-only, CI/CD com golden-set de acurácia, redação de PII em log.
```

### Backlog → Business Case
```
Total SPs: 176
Sprint 1: 47 pts (Foundation + Entra ID + Key Vault + CI/CD + intake mailbox + ID único + Service Bus + auditoria base)
Sprints estimados: ~4-5 sprints de 2 semanas
Risco crítico ainda aberto: sistema financeiro/CRM alvo não confirmado (RT-01), bloqueia EPIC-04 completo.
```

---

## Perguntas Respondidas (não perguntar de novo)

| # | Pergunta | Resposta | Fase |
|---|----------|----------|------|
| 1 | Qual o problema central? | Intake fragmentado, re-digitação manual, sem rastreabilidade, risco de decisão baseada em IA não revisada | discovery |
| 2 | Qual o ambiente cloud preferencial? | Microsoft Azure (alternativa aceita com justificativa) | discovery |
| 3 | Quais idiomas o piloto deve suportar? | Inglês, português e italiano | discovery |
| 4 | Pode uma decisão material ser tomada só por IA generativa? | Não — gate humano obrigatório (FR-012) | discovery/prd |
| 5 | Qual sistema financeiro e CRM alvo? | **Não definido** — questão aberta crítica (RT-01), a confirmar em Discovery detalhada | discovery |

---

## Padrões Identificados no Projeto

- Requisito de governança de IA elevado a princípio arquitetural (PA-01), não tratado como feature isolada — ver lição `[ARCHITECT]` em lessons-learned.md.
- Ambiguidade de sistema-alvo de integração não bloqueou o backlog inteiro — apenas a story e o epic de roteamento foram marcados como dependentes — ver lição `[BACKLOG]` em lessons-learned.md.

---

## Changelog do Memory

| Data | Fase | Mudança |
|------|------|---------|
| 2026-07-19 | discovery | Arquivo criado |
| 2026-07-19 | prd | Handoff PRD→Architect adicionado |
| 2026-07-19 | architect | Handoff Architect→Backlog adicionado; 3 ADRs registrados em Decisões Tomadas |
| 2026-07-19 | backlog | Handoff Backlog→Business Case adicionado |
| 2026-07-19 | business-case | Pipeline marcado como `done`; lições adicionadas ao framework |
