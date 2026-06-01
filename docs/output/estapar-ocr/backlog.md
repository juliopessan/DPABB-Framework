# Backlog — Estapar OCR/Orquestrador — Fechamento de Caixa

**Agente:** @backlog (Ben)  
**Data:** 2026-05-31  
**Projeto:** estapar-ocr  
**Complexidade:** STANDARD  
**Sprints estimados:** 8 sprints de 2 semanas  
**Velocidade base estimada:** 35 pts/sprint (equipe de 9 pessoas, STANDARD)

---

## Resumo Executivo

| Métrica | Valor |
|---------|-------|
| Total de Épicos | 8 |
| Total de Stories | 54 |
| Total de Story Points | 273 pts |
| Sprint 1 (Must Have técnico) | 45 pts |
| Sprints estimados | 8 sprints (~4 meses) |
| MVP (PDV/Estapar + 1 banco) | Sprint 3 (~6 semanas) |

---

## EPIC-TECH — Foundation & Security

> WP-01 | Sprint 1 | Pré-requisito para todos os outros épicos

| ID | Story | FRs/NFRs | Pts | Sprint | Prioridade |
|----|-------|---------|-----|--------|-----------|
| TECH-001 | **Setup repositório + Azure DevOps CI/CD** — criar repo, pipeline CI com lint/test/build/push ACR, 3 environments (dev/staging/prod) com aprovações | NFR-03 | 5 | S1 | Must Have |
| TECH-002 | **Provisionar APIM + Entra ID + Auth dois fatores** — configurar API Management com JWT validation, Entra ID App Registration, scopes, Managed Identity para Functions | NFR-04, FR-02 | 5 | S1 | Must Have |
| TECH-003 | **Provisionar Azure SQL + Blob Storage + Service Bus** — criar instâncias, schema completo (fechamento, relatório, comprovante, auditoria, config), lifecycle policy para blob | NFR-06/07/08 | 8 | S1 | Must Have |
| TECH-004 | **Setup Azure Document Intelligence** — provisionar recurso, pipeline de upload de amostras, treinamento de Custom Model base com layouts PDV/Estapar e BB | NFR-01, ADR-002 | 8 | S1 | Must Have |
| TECH-005 | **Config Service + Key Vault** — Config Service com parâmetros configuráveis (tolerância R$5,00, tabela ticket máximo, status Retaguarda), Key Vault para secrets | NFR-05/12 | 5 | S1 | Must Have |
| TECH-006 | **Spike: Mapeamento API Retaguarda** — documentar endpoints disponíveis (GET garagens, POST fechamento, GET ID comprovante), criar mock para testes | FR-01, FR-20 | 3 | S1 | Must Have |
| TECH-007 | **Spike: Treinamento OCR — Layouts PDV** — testar Document Intelligence com amostras reais de todos os 11 sistemas PDV; documentar precisão por campo | NFR-01 | 5 | S1 | Must Have |

**Subtotal EPIC-TECH: 39 pts | Sprint 1**

---

## EPIC-OCR-CORE — OCR Pipeline Base

> WP-02 | Sprints 1–2 | Infraestrutura de extração reutilizada por todos os adapters

| ID | Story | FRs | Pts | Sprint | Prioridade |
|----|-------|-----|-----|--------|-----------|
| OCR-001 | **Pipeline de ingestão de documentos** — endpoint para receber PDF/imagem do Portal, validar formato/tamanho, salvar no Blob, enfileirar no Service Bus | FR-03 | 5 | S1 | Must Have |
| OCR-002 | **Consumer Service Bus → Document Intelligence** — trigger por fila, enviar documento ao Document Intelligence, receber campos + scores de confiança, logar resultado | NFR-01/02 | 8 | S2 | Must Have |
| OCR-003 | **Modelo de confiança por campo** — estrutura de dados com campo, valor, score; rota para revisão manual quando score < 90% | NFR-01 | 5 | S2 | Must Have |
| OCR-004 | **Log estruturado de auditoria** — logar cada step do pipeline com correlation_id, garagem, sistema, campo, resultado, timestamp | NFR-07 | 5 | S2 | Must Have |
| OCR-005 | **Retry e Dead-Letter Queue** — configurar retry 3x no Service Bus; processar DLQ com alerta no App Insights | NFR-10 | 3 | S2 | Must Have |

**Subtotal EPIC-OCR-CORE: 26 pts | Sprints 1–2**

---

## EPIC-ORCH-FECHAMENTO — Orquestrador: Fluxo de Fechamento

> WP-03 | Sprint 2–3 | Core do fluxo de validação e gravação

| ID | Story | FRs | Pts | Sprint | Prioridade |
|----|-------|-----|-----|--------|-----------|
| ORCH-001 | **Consultar garagens com prestação pendente** — endpoint GET /garagens/pendentes, integrar com Retaguarda, retornar até 5 datas por garagem | FR-01 | 5 | S2 | Must Have |
| ORCH-002 | **Validar campos obrigatórios do relatório** — verificar data, arrecadação (qtd+valor), rotativo (qtd+valor), valor depósito; retornar lista de campos ausentes | FR-13 | 5 | S2 | Must Have |
| ORCH-003 | **Validar período, horário e garagem** — ERR-01 (período ≠ impeditivo), ERR-02 (horário não impeditivo + confirmação), ERR-03 (garagem ≠ impeditivo) | FR-14 | 5 | S2 | Must Have |
| ORCH-004 | **Detectar necessidade de depósito e contingência** — lógica RN05 (valor ≠ 0) e RN06 (sistema ≠ SoftCase) | FR-15/16 | 3 | S2 | Must Have |
| ORCH-005 | **Gravar fechamento no Retaguarda** — POST com dados validados; diferenciar garagem integrada vs não-integrada; atualizar status | FR-20 | 8 | S3 | Must Have |
| ORCH-006 | **Processar relatório de contingência** — receber segundo relatório, somar qtd/valor por produto com o principal, persistir dados somados | FR-19 | 8 | S3 | Must Have |
| ORCH-007 | **Processar desconto de mensalistas (Nepos/WPS/Link)** — detectar sistema, descontar mensalistas do total, destacar info ao usuário | FR-18 | 3 | S3 | Must Have |
| ORCH-008 | **Validar ticket médio** — ler tabela de ticket máximo do Config Service, comparar; obrigar exceção quando acima [BLOQUEADO por P1] | FR-17 | 3 | S3 | Must Have |
| ORCH-009 | **Endpoint de confirmação do usuário** — receber confirmação do Portal, acionar gravação no Retaguarda | FR-20 | 3 | S3 | Must Have |

**Subtotal EPIC-ORCH-FECHAMENTO: 43 pts | Sprints 2–3**

---

## EPIC-PDV-ADAPTERS — Adapters por Sistema PDV (×11)

> WP-04 | Sprints 2–4 | Um adapter por sistema com regra isolada e testável

| ID | Story | FR | Pts | Sprint | Prioridade |
|----|-------|-----|-----|--------|-----------|
| PDV-001 | **Adapter PDV/Estapar** — 12 campos; rotativo = arrecadação − tags − selos − mensalistas − cartão pré-pago | FR-04 | 8 | S2 | Must Have |
| PDV-002 | **Adapter Nepos** — 12 campos; rotativo = total estadia − isenções; capturar todas as linhas TAG | FR-05 | 8 | S3 | Must Have |
| PDV-003 | **Adapter WPS-PDF** — 9 campos; desconsiderar qtd=0; rotativo = arrecadação − tags − selos − mensalistas | FR-06 | 5 | S3 | Must Have |
| PDV-004 | **Adapter WPS-Fita** — 6 campos; arrecadação = rotativo; descontar qtd zeradas | FR-06 | 5 | S3 | Must Have |
| PDV-005 | **Adapter Link** — 14 campos; rotativo = pagamentos + pagantes; selos sem qtd → qtd=1 | FR-07 | 8 | S3 | Must Have |
| PDV-006 | **Adapter Parco** — 7 campos; rotativo = arrecadação (somar linhas) | FR-08 | 3 | S3 | Must Have |
| PDV-007 | **Adapter Prossiga/Perto** — 9 campos; rotativo = arrecadação − tags − depósito | FR-09 | 3 | S3 | Must Have |
| PDV-008 | **Adapter SoftCase** — 2 cenários: sem arrecadação (movimento zero) + com arrecadação (subtrai mensalistas) | FR-10 | 8 | S4 | Must Have |
| PDV-009 | **Adapter CloudPark/Cosiensa** — 8 campos; padrão | FR-11 | 3 | S4 | Must Have |
| PDV-010 | **Adapter AVC** — 5 campos; rotativo = arrecadação | FR-12 | 3 | S4 | Must Have |
| PDV-011 | **Adapter RotaPark/Aucon** — 3 campos; qtd/valor depósito via Portal | FR-12 | 3 | S4 | Must Have |

**Subtotal EPIC-PDV-ADAPTERS: 57 pts | Sprints 2–4**

---

## EPIC-BANK-PARSERS — Parsers Bancários (×9+ tipos)

> WP-05 | Sprints 3–5 | Um parser por banco/modelo com lógica de ID isolada

| ID | Story | FR | Pts | Sprint | Prioridade |
|----|-------|-----|-----|--------|-----------|
| BANK-001 | **Parser Banco do Brasil** — 4 campos; ID = numeração completa | FR-21 | 3 | S3 | Must Have |
| BANK-002 | **Parser Santander M1** — 4 campos; ID = 6 últimos dígitos | FR-22 | 3 | S3 | Must Have |
| BANK-003 | **Parser Santander M2** — 4 campos; ID = data + valor + hora ±1 min (concatenação) | FR-22 | 5 | S4 | Must Have |
| BANK-004 | **Parser Caixa Econômica** — 4 campos; ID = 4 primeiros + máscara 0XXXX000000 | FR-23 | 5 | S4 | Must Have |
| BANK-005 | **Parser Itaú M1 e M2** — 3 campos; sem ID | FR-24 | 3 | S4 | Must Have |
| BANK-006 | **Parser Itaú M3 e M4** — 4 campos; ID completo (M3) e 4 dígitos antes dos 7 finais (M4) | FR-24 | 5 | S4 | Must Have |
| BANK-007 | **Parser Bradesco** — 4 campos; ID = 4 últimos dígitos máquina + 3 últimos N.transação | FR-25 | 5 | S4 | Must Have |
| BANK-008 | **Parser Autoatendimento** — 3 campos; sem ID | FR-26 | 1 | S4 | Must Have |
| BANK-009 | **Parser Carro Forte (M1 e M3)** — 2 campos; valor informado via Portal [M2 BLOQUEADO P6] | FR-27 | 5 | S5 | Must Have |
| BANK-010 | **Parser Cofre Inteligente M1** — 3 campos; Modelo 2 em aberto [BLOQUEADO P9] | FR-27 | 3 | S5 | Must Have |
| BANK-011 | **Parser Múltiplos Depósitos** — N depósitos; lógica individual OU soma ±R$5,00; fallback valor mais próximo | FR-28 | 8 | S5 | Must Have |

**Subtotal EPIC-BANK-PARSERS: 46 pts | Sprints 3–5**

---

## EPIC-ORCH-COMPROVANTE — Orquestrador: Fluxo de Comprovante

> WP-06 | Sprints 4–6

| ID | Story | FRs | Pts | Sprint | Prioridade |
|----|-------|-----|-----|--------|-----------|
| COMP-001 | **Receber e enfileirar comprovante** — endpoint para receber comprovante do Portal, detectar banco/modelo, enfileirar para OCR | FR-21 a FR-28 | 5 | S4 | Must Have |
| COMP-002 | **Validar valor comprovante vs relatório** — comparar com tolerância R$5,00; retornar OK ou divergência com valores para o Portal | FR-29 | 5 | S5 | Must Have |
| COMP-003 | **Validar data D+1** — verificar data comprovante = data fechamento + 1; alertar se diferente (não impeditivo) | FR-30 | 3 | S5 | Must Have |
| COMP-004 | **Validar ID de depósito duplicado** — consultar Retaguarda pelo ID; bloquear se já vinculado | FR-31 | 5 | S5 | Must Have |
| COMP-005 | **Validar favorecido do comprovante** — comparar razão social da garagem com favorecido extraído | FR-32 | 3 | S5 | Must Have |
| COMP-006 | **Processar múltiplos comprovantes por fechamento** — aceitar 1 ou N comprovantes; acumular validações | FR-29 | 5 | S6 | Must Have |
| COMP-007 | **Fluxo de carro forte com entrada manual** — receber valor via Portal quando OCR não aplicável; validar contra relatório | FR-27 | 5 | S6 | Must Have |

**Subtotal EPIC-ORCH-COMPROVANTE: 31 pts | Sprints 4–6**

---

## EPIC-CONFIG-ADMIN — Configurações e Admin

> WP-07 | Sprint 6–7

| ID | Story | FRs/NFRs | Pts | Sprint | Prioridade |
|----|-------|---------|-----|--------|-----------|
| CFG-001 | **CRUD de parâmetros de configuração** — endpoint admin para ler/atualizar tabela de ticket máximo, tolerância de depósito e status Retaguarda | NFR-12 | 5 | S6 | Must Have |
| CFG-002 | **Tabela de produtos Retaguarda** — carregamento e atualização da relação de produtos; validação dos produtos no relatório financeiro | FR-20, NFR-12 | 5 | S6 | Must Have |
| CFG-003 | **Painel de status de fechamentos** — API para listar fechamentos por garagem/data/status; filtros por pendência | NFR-11 | 8 | S7 | Should Have |

**Subtotal EPIC-CONFIG-ADMIN: 18 pts | Sprints 6–7**

---

## EPIC-OBSERVABILIDADE — Monitor, Alertas e Dashboard

> WP-08 | Sprint 7–8

| ID | Story | NFRs | Pts | Sprint | Prioridade |
|----|-------|------|-----|--------|-----------|
| OBS-001 | **Dashboard de fechamentos** — Azure Workbook com KPIs: fechamentos/dia por garagem, taxa de erro OCR, tempo médio por sistema PDV | NFR-11 | 8 | S7 | Should Have |
| OBS-002 | **Alertas automáticos** — alerta < 5 min em falha crítica (fila parada, falha Retaguarda, OCR < 80% confiança) | NFR-11 | 5 | S7 | Must Have |
| OBS-003 | **Rastreamento distribuído E2E** — correlation_id em todas as chamadas; trace completo Portal → APIM → Function → OCR → Retaguarda | NFR-07 | 5 | S8 | Must Have |
| OBS-004 | **Relatório de auditoria** — query KQL exportável com todas as ações por fechamento; exportação CSV | NFR-07 | 3 | S8 | Should Have |

**Subtotal EPIC-OBSERVABILIDADE: 21 pts | Sprints 7–8**

---

## Sprint 1 — Plan Detalhado

> **Meta:** Fundação técnica operacional + OCR pipeline base para PDV/Estapar  
> **Capacidade:** 45 pts (equipe reduzida — Sprint 0 de setup)

| # | Story | Responsável | Pts |
|---|-------|------------|-----|
| 1 | TECH-001: CI/CD + 3 environments | DevOps | 5 |
| 2 | TECH-002: APIM + Entra ID + Auth | DevOps + Tech Lead | 5 |
| 3 | TECH-003: SQL + Blob + Service Bus | DevOps + Backend Dev 1 | 8 |
| 4 | TECH-004: Document Intelligence Setup | AI/ML Engineer | 8 |
| 5 | TECH-005: Config Service + Key Vault | Backend Dev 1 | 5 |
| 6 | TECH-006: Spike — Mapeamento API Retaguarda | Tech Lead | 3 |
| 7 | TECH-007: Spike — OCR com layouts PDV | AI/ML Engineer | 5 |
| 8 | OCR-001: Pipeline ingestão documentos | Backend Dev 1 | 5 |
| — | **Total Sprint 1** | | **44 pts** |

**Critérios de Done do Sprint 1:**
- [ ] Pipeline CI/CD rodando com deploy automático para dev
- [ ] APIM validando JWT do Entra ID
- [ ] Schema SQL criado e migrado
- [ ] Document Intelligence processando relatório PDV/Estapar com ≥ 85% confiança
- [ ] Blob Storage com lifecycle policy de 24h configurado
- [ ] Mapeamento da API Retaguarda documentado
- [ ] Config Service com parâmetros base carregados

---

## Mapa de Rastreabilidade

### FR → Epic → Story

| FR | Épico | Story(ies) |
|----|-------|-----------|
| FR-01 | EPIC-ORCH-FECHAMENTO | ORCH-001 |
| FR-02 | EPIC-TECH | TECH-002 |
| FR-03 | EPIC-OCR-CORE | OCR-001, OCR-002 |
| FR-04 | EPIC-PDV-ADAPTERS | PDV-001 |
| FR-05 | EPIC-PDV-ADAPTERS | PDV-002 |
| FR-06 | EPIC-PDV-ADAPTERS | PDV-003, PDV-004 |
| FR-07 | EPIC-PDV-ADAPTERS | PDV-005 |
| FR-08 | EPIC-PDV-ADAPTERS | PDV-006 |
| FR-09 | EPIC-PDV-ADAPTERS | PDV-007 |
| FR-10 | EPIC-PDV-ADAPTERS | PDV-008 |
| FR-11 | EPIC-PDV-ADAPTERS | PDV-009 |
| FR-12 | EPIC-PDV-ADAPTERS | PDV-010, PDV-011 |
| FR-13 | EPIC-ORCH-FECHAMENTO | ORCH-002 |
| FR-14 | EPIC-ORCH-FECHAMENTO | ORCH-003 |
| FR-15 | EPIC-ORCH-FECHAMENTO | ORCH-004 |
| FR-16 | EPIC-ORCH-FECHAMENTO | ORCH-004 |
| FR-17 | EPIC-ORCH-FECHAMENTO | ORCH-008 [BLOQUEADO P1] |
| FR-18 | EPIC-ORCH-FECHAMENTO | ORCH-007 |
| FR-19 | EPIC-ORCH-FECHAMENTO | ORCH-006 |
| FR-20 | EPIC-ORCH-FECHAMENTO | ORCH-005, ORCH-009 |
| FR-21 | EPIC-BANK-PARSERS | BANK-001 |
| FR-22 | EPIC-BANK-PARSERS | BANK-002, BANK-003 |
| FR-23 | EPIC-BANK-PARSERS | BANK-004 |
| FR-24 | EPIC-BANK-PARSERS | BANK-005, BANK-006 |
| FR-25 | EPIC-BANK-PARSERS | BANK-007 |
| FR-26 | EPIC-BANK-PARSERS | BANK-008 |
| FR-27 | EPIC-BANK-PARSERS | BANK-009, BANK-010 |
| FR-28 | EPIC-BANK-PARSERS | BANK-011 |
| FR-29 | EPIC-ORCH-COMPROVANTE | COMP-002, COMP-006 |
| FR-30 | EPIC-ORCH-COMPROVANTE | COMP-003 |
| FR-31 | EPIC-ORCH-COMPROVANTE | COMP-004 |
| FR-32 | EPIC-ORCH-COMPROVANTE | COMP-005 |

---

## Roadmap por Sprint

| Sprint | Duração | Foco | Pontos | Épicos |
|--------|---------|------|--------|--------|
| S0 | 1 semana | Setup, mapeamento Retaguarda, onboarding | — | Pré-sprint |
| S1 | 2 sem | Foundation + OCR pipeline base | 44 pts | TECH + OCR-CORE (inicio) |
| S2 | 2 sem | OCR Core completo + Orquestrador base + PDV/Estapar | 38 pts | OCR-CORE + ORCH-FECHAMENTO + PDV-001 |
| S3 | 2 sem | Orquestrador fechamento completo + 5 adapters PDV | 40 pts | ORCH-FECHAMENTO + PDV-002…007 + BANK-001/002 |
| S4 | 2 sem | PDV adapters restantes + 6 parsers bancários + comprovante base | 37 pts | PDV-008…011 + BANK-003…008 + COMP-001 |
| S5 | 2 sem | Parsers bancários complexos + fluxo comprovante | 35 pts | BANK-009…011 + COMP-002…005 |
| S6 | 2 sem | Config Admin + comprovante avançado | 28 pts | CFG-001/002 + COMP-006/007 |
| S7 | 2 sem | Dashboard + alertas + painel de status | 26 pts | CFG-003 + OBS-001/002 |
| S8 | 2 sem | Rastreamento E2E + auditoria + testes E2E | 25 pts | OBS-003/004 + testes integração |
| **Total** | **~4 meses** | | **273 pts** | |

---

## Stories Bloqueadas (Dependências Externas)

| Story | Bloqueio | Item Pendente | Impacto |
|-------|---------|--------------|---------|
| ORCH-008 | Tabela de ticket máximo não definida | P1 | ORCH-008 implementado com config placeholder |
| ORCH-005 | Status Retaguarda pós-gravação não definido | P2 | Usar "Documento recebido" como placeholder |
| COMP-002 | Status com exceção não definido | P3 | Usar código genérico como placeholder |
| BANK-009 (M2) | Carro forte M2: OCR ou manual? | P4/P6 | M2 fora do escopo por ora |
| BANK-010 (M2) | Cofre inteligente Modelo 2 em aberto | P9 | M2 fora do escopo por ora |
