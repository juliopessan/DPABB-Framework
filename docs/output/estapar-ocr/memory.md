# Memory — estapar-ocr

> Arquivo gerado e atualizado automaticamente pelo pipeline.  
> **Propósito:** Evitar re-elicitação de contexto já coletado. Cada agente lê este arquivo antes de agir.  
> **Regra:** Nunca deletar entradas. Apenas adicionar ou marcar como `[SUPERADO]`.

---

## Meta

| Campo | Valor |
|-------|-------|
| Projeto | estapar-ocr |
| Início do pipeline | 2026-05-31 |
| Última atualização | 2026-05-31 |
| Fase atual | done |
| Token savings estimado | ~2.800 tokens vs cold start da spec completa |

---

## Contexto Acumulado

### Sobre o Projeto
- **Problema:** Fechamento diário de prestação da Estapar é manual, com 10 sistemas PDV e 9 tipos de comprovante bancário. Alto volume, erro humano, sem rastreabilidade automática.
- **Solução proposta:** OCR + Orquestrador para automação de extração, validação e gravação de dados no Retaguarda
- **Stakeholders chave:** Alcir Oliveira, Flavio Abade, Flavio Ramalho (aprovadores TI/negócio) + Livia Cordeiro (negócio)
- **Prazo e orçamento:** Não informado na spec

### Restrições Fixas (não negociáveis)
- **Tecnológicas:** Portal como único ponto de entrada; Retaguarda como destino (API read+write); OCR como serviço dedicado
- **Regulatórias:** LGPD (razão social + dados bancários); dados sensíveis de transações financeiras
- **Organizacionais:** Um documento por sistema por vez (RN04); autenticação dois fatores Portal↔Orquestrador (RN03)

### Decisões Tomadas (não reabrir)
| Decisão | Fase | Razão | Alternativas rejeitadas |
|---------|------|-------|------------------------|
| Tolerância depósito = R$5,00 | discovery | RN13 da spec | Nenhuma — definição de negócio |
| Data comprovante = D+1 | discovery | RN16 da spec | Nenhuma — definição de negócio |
| Contingência = soma por produto | discovery | RN12 da spec | Nenhuma — definição de negócio |
| ID duplicado bloqueia processo | discovery | RN15 da spec | Nenhuma — regra de auditoria |
| 1 documento/sistema por vez | discovery | RN04 da spec | Nenhuma — decisão de negócio |

---

## Artefatos Produzidos

| Artefato | Caminho | Status | Versão |
|----------|---------|--------|--------|
| discovery-report.md | `docs/output/estapar-ocr/discovery-report.md` | ✅ | 1.0 |
| prd.md | `docs/output/estapar-ocr/prd.md` | ✅ | 1.0 |
| architecture.md | `docs/output/estapar-ocr/architecture.md` | ✅ | 1.0 |
| backlog.md | `docs/output/estapar-ocr/backlog.md` | ✅ | 1.0 |
| business-case.md | `docs/output/estapar-ocr/business-case.md` | ✅ | 1.0 |

---

## Handoffs Entre Fases

### Discovery → PRD

```
PROJETO: estapar-ocr
PROBLEMA: Automação do Fechamento Diário de Prestação da Estapar.
          Processo manual com 10 sistemas PDV + 9 tipos de comprovante bancário.
          Alto volume, propenso a erro, sem rastreabilidade automática.

STAKEHOLDERS APROVADORES: Alcir Oliveira, Flavio Abade, Flavio Ramalho, Livia Cordeiro
PERSONAS: Responsável de Garagem (enviador) | Analista de Fechamento (supervisor)

FRENTES NO ESCOPO: 1 (fechamento por prestação) e 2 (comprovante bancário)
FRENTE 3 (relato de exceções): fora de escopo desta spec

SISTEMAS PDV (11): PDV/Estapar, Nepos, WPS-PDF, WPS-Fita, Link, Parco,
                   Prossiga/Perto, SoftCase, CloudPark/Cosiensa, AVC, RotaPark/Aucon
BANCOS (9+ tipos): BB, Santander(2 modelos), Caixa, Itaú(4 modelos), Bradesco,
                   Autoatendimento, Carro forte(3 modelos), Cofre inteligente(2 modelos),
                   Múltiplos depósitos

REGRAS CRÍTICAS:
  - Rotativo: calculado de forma distinta por sistema (ver discovery-report §4.1)
  - Tolerância depósito: R$5,00 (RN13)
  - Data comprovante: D+1 (RN16)
  - ID duplicado: bloqueia (RN15)
  - Campos obrigatórios: data, arrecadação (qtd+valor), rotativo (qtd+valor), valor depósito

ITENS PENDENTES (documentar como restrição/risco no PRD):
  P1: Tabela valores máximos ticket/selo (RN08) — NFR de validação de ticket médio
  P2: Status Retaguarda pós-gravação (RN11) — FR de integração
  P3: Status com exceção (RN14) — FR de exceção
  P4: Carro forte: OCR ou manual? (RN18) — FR de comprovante carro forte

COMPLEXIDADE: STANDARD (múltiplos layouts × regras específicas por sistema)
INTEGRAÇÕES: Portal (API in) + Retaguarda (API read+write) + OCR Engine (serviço)
NÃO EXPLORAR NO PRD: detalhes de UI/UX do Portal, SLA de SLA exato, custo de infra
```

### PRD → Architect

```
PROJETO: estapar-ocr
TIPO: Sistema de automação com OCR + Orquestrador de processos

COMPONENTES PRINCIPAIS:
  - OCR Engine: 11 parsers PDV + 9 parsers bancários (Strategy/Adapter pattern)
  - Orquestrador: validação, roteamento e persistência (pipeline stateful)
  - Portal: API Gateway de entrada
  - Retaguarda: sistema legado de destino (API read+write)

NFRs CRÍTICOS PARA ARQUITETURA:
  NFR-01: Precisão OCR ≥ 90% por campo
  NFR-02: ≤ 30s relatório; ≤ 15s comprovante (processamento assíncrono)
  NFR-03: Disponibilidade ≥ 99,5%
  NFR-04/05/06: Auth dois fatores, TLS 1.2+, AES-256
  NFR-07: Auditoria 100% das ações
  NFR-09: Novo layout PDV/banco ≤ 5 dias dev

PADRÕES PARA ARQUITETURA:
  - Strategy Pattern por sistema PDV (11 adapters independentes)
  - Strategy Pattern por banco (9+ parsers independentes)
  - Pipeline: OCR → Validate → Enrich → Route → Persist
  - Fila para resiliência e processamento assíncrono
  - Storage temporário de documentos ≤ 24h

TOTAL: 32 FRs | 13 NFRs | Complexidade STANDARD
NÃO EXPLORAR: detalhes de UI/UX do Portal, layout de banco Inter (pendente)
```

### Architect → Backlog

```
PROJETO: estapar-ocr | COMPLEXIDADE: STANDARD

ESTILO: Monolito Modular — Azure Functions + Service Bus (ADR-001)
STACK: Azure Functions (C#/Node) + Document Intelligence + Service Bus + Azure SQL + Blob + APIM + Entra ID
OCR ENGINE: Azure Document Intelligence Custom Models (ADR-002)
AUTH: Entra ID OAuth2/JWT via APIM (ADR-003)

COMPONENTES → STORIES TÉCNICAS OBRIGATÓRIAS:
  TECH-001: Setup repositório + Azure DevOps CI/CD + 3 environments (dev/staging/prod)
  TECH-002: Provisionar APIM + Entra ID + Key Vault + Managed Identity
  TECH-003: Provisionar Azure SQL (schema completo) + Blob Storage + Service Bus
  TECH-004: Setup Azure Document Intelligence + Custom Model training pipeline
  TECH-005: Config Service (tabela de ticket máximo + tolerância configurável)

WPs → ÉPICOS:
  WP-01 → EPIC-TECH (Foundation)        — S1
  WP-02 → EPIC-OCR-CORE                 — S1-S2
  WP-03 → EPIC-ORCH-FECHAMENTO          — S2
  WP-04 → EPIC-PDV-ADAPTERS (×11)       — S2-S3
  WP-05 → EPIC-BANK-PARSERS (×9+)       — S3
  WP-06 → EPIC-ORCH-COMPROVANTE         — S3-S4
  WP-07 → EPIC-CONFIG-ADMIN             — S4
  WP-08 → EPIC-OBSERVABILIDADE          — S4

ESTIMATIVAS BASE: STANDARD → 3-5 pts/componente; adapters PDV S(3)/M(8); parsers S(3)
EQUIPE: 9 pessoas (7,3 FTEs) | custo pico R$ 118.000/mês (PJ) ou R$ 200.600/mês (CLT)

RISCOS PARA VIRAR SPIKES:
  RT-01: Treinamento OCR layouts PDV → spike 5 pts (S1)
  RT-02: Mapeamento API Retaguarda → spike 3 pts (Sprint 0)
NÃO EXPLORAR: infra detalhada, regras de UI Portal, itens pendentes P1-P4 (bloqueados)
```

### Backlog → Business Case

```
PROJETO: estapar-ocr

TOTAL: 8 épicos | 54 stories | 273 story points
SPRINT 1: 44 pts (foundation + OCR base)
VELOCIDADE ESTIMADA: 35 pts/sprint
SPRINTS ESTIMADOS: 8 sprints (≈ 4 meses de desenvolvimento)
DURAÇÃO TOTAL: ~5 meses com Sprint 0 + buffer

CUSTO DE DESENVOLVIMENTO:
  Equipe: 9 pessoas (7,3 FTEs)
  Custo pico PJ: R$ 118.000/mês
  Custo pico CLT (×1,7): R$ 200.600/mês
  Total equipe PJ (5 meses): R$ ~469.000
  Total equipe CLT (5 meses): R$ ~797.300
  Infraestrutura Azure estimada: R$ 8.000–15.000/mês → R$ ~55.000 (5 meses)

MVP FUNCIONAL: Sprint 3 (~6 semanas) — PDV/Estapar + Banco do Brasil
GO-LIVE COMPLETO: Sprint 8 (~4 meses de dev)

STORIES BLOQUEADAS POR PENDÊNCIAS: 5 stories afetadas (P1, P2, P3, P6, P9)
  → Impacto: implementadas com valores placeholder configuráveis

NÃO EXPLORAR: breakdown técnico de infraestrutura, custo por usuário, ROI de segurança
```

---

## Perguntas Respondidas (não perguntar de novo)

| # | Pergunta | Resposta | Fase |
|---|---------|---------|------|
| 1 | Qual é o problema central? | Fechamento manual de prestação diária com 10+ sistemas PDV | discovery |
| 2 | Quais sistemas PDV existem? | 11 sistemas: PDV/Estapar, Nepos, WPS-PDF, WPS-Fita, Link, Parco, Prossiga, SoftCase, CloudPark, AVC, RotaPark | discovery |
| 3 | Quais bancos são suportados? | BB, Santander(2), Caixa, Itaú(4), Bradesco, Autoatendimento, Carro forte(3), Cofre(2), Múltiplos | discovery |
| 4 | Qual é a tolerância de divergência? | R$5,00 (RN13) | discovery |
| 5 | Qual é a regra de data do comprovante? | D+1 em relação ao fechamento (RN16) | discovery |
| 6 | O que é considerado "prestação em aberto"? | Status diferente de "Prestação Finalizada" | discovery |
| 7 | Quantas garagens o orquestrador lista por consulta? | Até 5 datas por garagem (RN01) | discovery |
| 8 | O que é relatório de contingência? | Segundo documento com layout diferente do sistema principal; ocorre em garagens ≠ SoftCase | discovery |
| 9 | Como é feita a soma na contingência? | Somar qtd e valor por produto (rotativo+rotativo, depósito+depósito, etc.) | discovery |
| 10 | O que é obrigatório no relatório financeiro? | Data, arrecadação (qtd+valor), rotativo (qtd+valor), valor depósito | discovery |
| 11 | O horário fora do range é impeditivo? | Não — usuário pode confirmar e seguir | discovery |
| 12 | Quem são os stakeholders aprovadores? | Alcir Oliveira, Flavio Abade, Flavio Ramalho, Livia Cordeiro | discovery |
| 13 | Qual é o escopo deste documento? | Frentes 1 e 2 — fechamento por prestação e comprovante bancário | discovery |
| 14 | Garagem SoftCase tem contingência? | Não — RN06: contingência apenas para sistema diferente de SoftCase | discovery |
| 15 | Como o ID do Santander Modelo 2 é formado? | Concatenar data + valor + hora ±1 minuto | discovery |
| 16 | Como o ID do Bradesco é formado? | 4 últimos dígitos máquina + 3 últimos N.transação | discovery |
| 17 | O RotaPark informa qtd/valor de depósito via Portal? | Sim — sistema não inclui no relatório | discovery |
| 18 | O Link sem qtd de selo usa qual valor? | 1 (considerar quantidade 01) | discovery |

---

## Padrões Identificados no Projeto

- **Multi-layout**: cada sistema PDV tem lógica própria de cálculo do rotativo — abstrair em adapters por sistema
- **Multi-banco**: cada banco tem lógica própria de ID — abstrair em parsers por banco/modelo
- **Tolerância configurável**: R$5,00 hoje mas deve ser parametrizável
- **Itens em aberto como configurações**: tabela de ticket máximo, status Retaguarda — implementar como configuração externa

---

## Changelog do Memory

| Data | Fase | Mudança |
|------|------|---------|
| 2026-05-31 | discovery | Arquivo criado com handoff para PRD |
