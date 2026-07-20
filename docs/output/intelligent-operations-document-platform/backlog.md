# Backlog — Intelligent Operations Document Platform

**Versão:** 1.0
**Data:** 2026-07-19
**Autor:** @backlog (Ben)
**PRD:** [prd.md](prd.md) | **Arquitetura:** [architecture.md](architecture.md)
**Status:** Draft

---

## Epic EPIC-00: Foundation & Security (Técnico)
**Objetivo:** Prover base de infraestrutura, identidade e pipeline de CI/CD sobre a qual todos os demais epics são construídos.
**FRs cobertos:** Suporte transversal a NFR-001, NFR-007, NFR-008, NFR-013
**Estimativa total:** 34 pts

### STORY-00.01: Provisionar ambientes dev/test/prod no Azure
**Como** Platform Engineering
**Quero** ambientes segregados de dev, test e produção
**Para** isolar mudanças antes do go-live

**Critérios de Aceitação:**
- Given uma nova infraestrutura, When provisionada via IaC, Then existem 3 ambientes isolados com naming e RBAC próprios
- Given um deploy em dev, When promovido, Then não afeta produção

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** — | **Rastreabilidade:** NFR-001, NFR-013 (técnica, sem FR direto)

### STORY-00.02: Configurar Entra ID + RBAC por papel
**Como** Platform Engineering
**Quero** SSO corporativo com MFA e papéis (Reviewer, Admin, Auditor)
**Para** garantir least privilege (ADR-003)

**Critérios de Aceitação:**
- Given um usuário corporativo, When faz login, Then passa por MFA via Entra ID
- Given um papel RBAC, When atribuído, Then o usuário só acessa recursos permitidos ao papel

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** STORY-00.01 | **Rastreabilidade:** NFR-007

### STORY-00.03: Configurar Key Vault e gestão de secrets
**Como** Platform Engineering
**Quero** secrets e chaves geridos centralmente
**Para** eliminar credenciais em código/configuração

**Estimativa:** S (3pts) | **Prioridade:** Must Have | **Dependências:** STORY-00.01 | **Rastreabilidade:** NFR-008

### STORY-00.04: Pipeline CI/CD com lint, testes e scan de segurança
**Como** Tech Lead
**Quero** pipeline automatizado dev→staging→prod
**Para** entregar com qualidade e segurança consistentes

**Critérios de Aceitação:**
- Given um PR, When aberto, Then lint + testes + scan de secrets rodam automaticamente
- Given merge em main, When pipeline passa, Then deploy automático em staging

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** STORY-00.01 | **Rastreabilidade:** NFR-012

### STORY-00.05: Observabilidade base (Monitor + App Insights + alertas)
**Como** Platform Engineering
**Quero** logs, métricas, traces e alertas desde o Sprint 1
**Para** detectar falhas e violação de SLA cedo

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** STORY-00.01 | **Rastreabilidade:** NFR-013

---

## Epic EPIC-01: Intake Governado
**Objetivo:** Ingerir documentos de mailbox e SharePoint com ID único, deduplicação e tratamento de falhas.
**FRs cobertos:** FR-001, FR-002, FR-003, FR-004, FR-005
**Estimativa total:** 29 pts

### STORY-01.01: Ingerir anexos de shared mailbox
**Como** plataforma
**Quero** capturar e-mails/anexos da mailbox compartilhada
**Para** iniciar o processamento sem intervenção manual

**Critérios de Aceitação:**
- Given um e-mail novo na mailbox configurada, When o conector roda, Then um evento de intake é criado
- Given um anexo não suportado, When recebido, Then é sinalizado sem travar o restante da fila

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** STORY-00.01 | **Rastreabilidade:** FR-001

### STORY-01.02: Ingerir documentos do SharePoint
**Como** plataforma
**Quero** capturar documentos de bibliotecas SharePoint configuradas
**Para** cobrir a segunda fonte obrigatória do piloto

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** STORY-00.01 | **Rastreabilidade:** FR-001

### STORY-01.03: Atribuir ID de processamento único
**Como** plataforma
**Quero** gerar um ID imutável por evento de intake
**Para** permitir rastreamento fonte-a-ação

**Estimativa:** XS (1pt) | **Prioridade:** Must Have | **Dependências:** STORY-01.01, STORY-01.02 | **Rastreabilidade:** FR-002

### STORY-01.04: Detectar documentos duplicados
**Como** plataforma
**Quero** identificar submissões equivalentes
**Para** evitar processamento e roteamento duplicado

**Critérios de Aceitação:**
- Given dois documentos com mesmo hash/critério configurável, When o segundo é ingerido, Then é marcado como duplicado e não roteado automaticamente

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** STORY-01.03 | **Rastreabilidade:** FR-003

### STORY-01.05: Retry e fila de exceção de intake
**Como** Platform Engineering
**Quero** retry automático com limite e fila de exceção para falhas permanentes
**Para** nenhum documento se perder silenciosamente

**Estimativa:** S (3pts) | **Prioridade:** Must Have | **Dependências:** STORY-01.03 | **Rastreabilidade:** FR-004, FR-005

### STORY-01.06 (técnica): Configurar Service Bus com dead-letter queue
**Como** Platform Engineering
**Quero** fila assíncrona com dead-letter
**Para** suportar retry/exceção sem perda de mensagem

**Estimativa:** S (3pts) | **Prioridade:** Must Have | **Dependências:** STORY-00.01 | **Rastreabilidade:** técnica (suporta FR-004, FR-005)

---

## Epic EPIC-02: Classificação e Extração
**Objetivo:** Classificar documentos por taxonomia e extrair campos com confiança, fonte e versão rastreáveis.
**FRs cobertos:** FR-006, FR-007, FR-008, FR-009, FR-010, FR-020, FR-021
**Estimativa total:** 42 pts

### STORY-02.01: Classificar documento por taxonomia configurável
**Como** plataforma
**Quero** atribuir um tipo de documento com score de confiança
**Para** direcionar o pipeline de extração correto

**Estimativa:** L (13pts) | **Prioridade:** Must Have | **Dependências:** EPIC-01 | **Rastreabilidade:** FR-006

### STORY-02.02: Extrair campos configurados por tipo de documento (fatura)
**Como** Finance Reviewer
**Quero** que os campos de fatura sejam extraídos automaticamente
**Para** eliminar re-digitação manual

**Critérios de Aceitação:**
- Given uma fatura classificada, When extraída, Then os campos prioritários (fornecedor, valor, vencimento) aparecem com confiança individual

**Estimativa:** L (13pts) | **Prioridade:** Must Have | **Dependências:** STORY-02.01 | **Rastreabilidade:** FR-007

### STORY-02.03: Reter confiança, fonte e timestamp por campo
**Como** auditor
**Quero** que todo campo extraído carregue proveniência
**Para** permitir auditoria fonte-a-ação

**Estimativa:** S (3pts) | **Prioridade:** Must Have | **Dependências:** STORY-02.02 | **Rastreabilidade:** FR-008

### STORY-02.04: Suportar correção humana de valor extraído
**Como** reviewer
**Quero** corrigir um valor extraído incorretamente
**Para** garantir que o dado final esteja correto antes do roteamento

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** STORY-02.03 | **Rastreabilidade:** FR-009

### STORY-02.05: Versionar modelo/prompt/regra por resultado
**Como** Tech Lead
**Quero** rastrear qual versão gerou cada resultado
**Para** permitir auditoria de mudanças de modelo (ADR-002)

**Estimativa:** S (3pts) | **Prioridade:** Must Have | **Dependências:** STORY-02.01 | **Rastreabilidade:** FR-010

### STORY-02.06: Suporte a EN/PT/IT na classificação e extração
**Como** analista multi-país
**Quero** processar documentos em inglês, português e italiano
**Para** cobrir as 3 geografias do piloto

**Estimativa:** L (13pts) | **Prioridade:** Should Have | **Dependências:** STORY-02.02 | **Rastreabilidade:** FR-020

### STORY-02.07: Configurar novo tipo de documento sem redeploy
**Como** Platform Engineering
**Quero** adicionar um tipo de documento via configuração de taxonomia
**Para** suportar expansão futura sem alterar código core

**Estimativa:** M (8pts, mas registrada como técnica de plataforma) | **Prioridade:** Should Have | **Dependências:** STORY-02.01 | **Rastreabilidade:** FR-021

> Nota: soma nominal do Epic é 13+13+3+8+3+13+8 = 61pts; ajustada para 42pts no total considerando reuso de componentes entre stories (ver estimativa consolidada da arquitetura, WP-03).

---

## Epic EPIC-03: Confiança e Validação Humana
**Objetivo:** Aplicar thresholds de confiança e garantir gate obrigatório de aprovação humana para campos críticos.
**FRs cobertos:** FR-011, FR-012, FR-013
**Estimativa total:** 24 pts

### STORY-03.01: Aplicar thresholds de confiança configuráveis
**Como** Platform Engineering
**Quero** configurar thresholds por tipo de campo
**Para** decidir a rota (automática vs. revisão) de forma ajustável

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** EPIC-02 | **Rastreabilidade:** FR-011

### STORY-03.02: Gate de aprovação obrigatório para campo crítico
**Como** Compliance/DPO
**Quero** que nenhum campo crítico de baixa confiança seja roteado sem aprovação humana
**Para** cumprir o controle de governança de IA do RFP (BR-005)

**Critérios de Aceitação:**
- Given um campo crítico com confiança abaixo do threshold, When avaliado, Then fica bloqueado em "pending_approval"
- Given uma tentativa de roteamento automático de campo crítico não aprovado, When detectada, Then é rejeitada pelo sistema

**Estimativa:** L (13pts) | **Prioridade:** Must Have | **Dependências:** STORY-03.01 | **Rastreabilidade:** FR-012

### STORY-03.03: Fila de revisão com ações aprovar/rejeitar/corrigir/escalar
**Como** Finance Reviewer / Customer Service Analyst
**Quero** agir sobre itens da minha fila
**Para** desbloquear o roteamento com decisão registrada

**Estimativa:** M (8pts, mas parte já coberta por STORY-02.04) — registrada como M (8pts) autônoma para ações de rejeitar/escalar | **Prioridade:** Must Have | **Dependências:** STORY-02.04 | **Rastreabilidade:** FR-013

> Total ajustado do Epic: 8+13+3 = 24pts (STORY-03.03 conta 3pts incrementais sobre a base já entregue em STORY-02.04).

---

## Epic EPIC-04: Roteamento e Integrações
**Objetivo:** Rotear resultados aprovados para o sistema financeiro e CRM com recuperação de falhas.
**FRs cobertos:** FR-014, FR-015
**Estimativa total:** 21 pts

### STORY-04.01: Rotear resultado aprovado para sistema financeiro
**Como** Finance Reviewer
**Quero** que o dado aprovado chegue automaticamente ao sistema financeiro
**Para** eliminar o passo manual de submissão

**Estimativa:** L (13pts) | **Prioridade:** Must Have | **Dependências:** EPIC-03, definição do sistema financeiro alvo (risco RT-01) | **Rastreabilidade:** FR-014

### STORY-04.02: Rotear resultado aprovado para o CRM
**Como** Customer Service Analyst
**Quero** que a solicitação aprovada crie/atualize um caso no CRM
**Para** eliminar duplicação manual de registro

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** EPIC-03, definição do CRM alvo (risco RT-01) | **Rastreabilidade:** FR-014

### STORY-04.03: Falha de roteamento recuperável e auditável
**Como** Platform Engineering
**Quero** que falhas de roteamento fiquem visíveis e reprocessáveis
**Para** nunca perder um resultado aprovado

**Estimativa:** — (incluída no dead-letter de STORY-01.06, sem pontos adicionais) | **Prioridade:** Must Have | **Dependências:** STORY-01.06 | **Rastreabilidade:** FR-015

---

## Epic EPIC-05: Busca, Auditoria e Dashboards
**Objetivo:** Fornecer busca, visão consolidada de lineage e métricas operacionais.
**FRs cobertos:** FR-016, FR-017, FR-018, FR-019
**Estimativa total:** 26 pts

### STORY-05.01: Buscar registros por data, fonte, tipo, status e ID de negócio
**Como** usuário autorizado
**Quero** buscar processamentos por múltiplos critérios
**Para** localizar rapidamente um caso específico

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** EPIC-01, EPIC-02 | **Rastreabilidade:** FR-016

### STORY-05.02: Visão unificada de fonte, extração, correções e status
**Como** auditor
**Quero** ver todo o histórico de um documento em uma tela
**Para** resolver disputas e auditorias com evidência completa

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** STORY-05.01 | **Rastreabilidade:** FR-017, FR-019

### STORY-05.03: Registro de auditoria imutável (ator, ação, timestamp, diff)
**Como** Compliance/DPO
**Quero** que toda ação material gere um registro protegido
**Para** cumprir requisito regulatório de rastreabilidade

**Estimativa:** M (8pts) | **Prioridade:** Must Have | **Dependências:** — (transversal, iniciar no Sprint 1) | **Rastreabilidade:** FR-019

### STORY-05.04: Dashboards operacionais (volume, latência, acurácia, exceções, ageing)
**Como** COO / Diretor de área
**Quero** visualizar métricas operacionais consolidadas
**Para** decidir sobre o scale-up com dados

**Estimativa:** M (8pts, ajustado para 2pts residual pois reaproveita dados de STORY-05.03) | **Prioridade:** Should Have | **Dependências:** STORY-05.03 | **Rastreabilidade:** FR-018

---

## Resumo de Estimativas

| Epic | Pontos | FRs |
|------|--------|-----|
| EPIC-00 Foundation & Security | 34 | técnico |
| EPIC-01 Intake Governado | 29 | FR-001 a FR-005 |
| EPIC-02 Classificação e Extração | 42 | FR-006 a FR-010, FR-020, FR-021 |
| EPIC-03 Confiança e Validação Humana | 24 | FR-011 a FR-013 |
| EPIC-04 Roteamento e Integrações | 21 | FR-014, FR-015 |
| EPIC-05 Busca, Auditoria e Dashboards | 26 | FR-016 a FR-019 |
| **Total** | **176 pts** | |

**Sprints estimados:** ~4-5 sprints de 2 semanas a 40-50pts/sprint (equipe descrita em architecture.md § E.4-E.7), consistente com a janela de 12 semanas de "Foundation + AI configuration + validation workflows" do RFP §18.

---

## Sprint 1 (MVP) — Proposta

**Critério:** apenas Must Have, máximo 40-60pts, cobre happy path de Finance Reviewer + infraestrutura/autenticação.

| Story | Epic | Pontos |
|-------|------|--------|
| STORY-00.01 Ambientes dev/test/prod | EPIC-00 | 8 |
| STORY-00.02 Entra ID + RBAC | EPIC-00 | 8 |
| STORY-00.03 Key Vault | EPIC-00 | 3 |
| STORY-00.04 CI/CD | EPIC-00 | 8 |
| STORY-01.01 Ingerir mailbox | EPIC-01 | 8 |
| STORY-01.03 ID único | EPIC-01 | 1 |
| STORY-01.06 Service Bus + dead-letter | EPIC-01 | 3 |
| STORY-05.03 Auditoria imutável (base) | EPIC-05 | 8 |
| **Total Sprint 1** | | **47 pts** |

> Sprint 1 entrega a fundação de segurança/observabilidade + o primeiro segmento do intake (mailbox) com auditoria desde o dia 1, dentro do limite de 40-60pts. Extração, gate de confiança e roteamento entram a partir do Sprint 2, condicionados à confirmação do sistema financeiro/CRM alvo (risco RT-01).

---

## Dependências entre Stories (sem ciclos)

```
EPIC-00 (Foundation) → EPIC-01 (Intake) → EPIC-02 (Classificação/Extração)
                                        → EPIC-03 (Confiança/Validação) → EPIC-04 (Roteamento)
EPIC-00 → EPIC-05 (Auditoria pode iniciar em paralelo ao Sprint 1)
```

---

## Rastreabilidade Stories → FR (resumo)

| FR | Story(ies) |
|----|------------|
| FR-001 a FR-005 | STORY-01.01 a STORY-01.06 |
| FR-006 a FR-010, FR-020, FR-021 | STORY-02.01 a STORY-02.07 |
| FR-011 a FR-013 | STORY-03.01 a STORY-03.03 |
| FR-014, FR-015 | STORY-04.01 a STORY-04.03 |
| FR-016 a FR-019 | STORY-05.01 a STORY-05.04 |

**Cobertura:** 100% dos FRs Must/Should do PRD têm pelo menos 1 story associada.

---

## Histórico de Versões

| Versão | Data | Autor | Mudança |
|--------|------|-------|---------|
| 1.0 | 2026-07-19 | @backlog (Ben) | Versão inicial — 6 Epics, 176pts, Sprint 1 de 47pts |
