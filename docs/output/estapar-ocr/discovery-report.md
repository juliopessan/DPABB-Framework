# Discovery Report — Estapar OCR/Orquestrador — Fechamento de Caixa

**Agente:** @discovery (Scout)  
**Data:** 2026-05-31  
**Projeto:** estapar-ocr  
**Fonte primária:** Especificação Funcional v1.1 — Selma Rodrigues (30/01/2026)  
**Status:** Completed

---

## 1. Contexto e Problema de Negócio

### 1.1 Empresa e Domínio

A **Estapar** é uma das maiores operadoras de estacionamentos do Brasil, operando centenas de garagens com sistemas de ponto de venda (PDV) heterogêneos. O fechamento diário de caixa — chamado de **Fechamento de Prestação** — é um processo crítico realizado manualmente, envolvendo a leitura e validação de relatórios financeiros emitidos por diferentes sistemas PDV, além de comprovantes de depósito bancário de múltiplos bancos.

### 1.2 Problema Atual (As-Is)

O processo atual de fechamento envolve:

1. **Operadores de garagem** geram relatórios financeiros em PDF/imagem a partir de seus sistemas locais (10 sistemas distintos)
2. **Analistas de backoffice** recebem esses documentos manualmente, validam valores, e lançam dados no sistema Retaguarda
3. Para garagens que receberam depósitos em dinheiro, **comprovantes bancários** de até 9 tipos distintos de banco/modalidade devem ser analisados e cruzados com o relatório financeiro
4. Todo esse processo é **manual, propenso a erro e de alto volume**

**Impacto estimado do problema:**
- Retrabalho por erros de digitação/leitura
- Atraso no fechamento contábil diário
- Inconsistência entre dados do relatório PDV e valores lançados no Retaguarda
- Risco de fraude/desvio não detectado automaticamente

### 1.3 Solução Proposta (To-Be)

Automação via dois componentes:

| Componente | Função |
|-----------|--------|
| **OCR** | Extração e interpretação de dados de relatórios financeiros e comprovantes bancários (PDF/imagem) |
| **Orquestrador** | Coordenação do fluxo: valida regras de negócio, cruza dados, integra com Portal e Retaguarda |

**Três frentes funcionais** (este documento cobre frentes 1 e 2):
1. Fechamento por prestação (relatório financeiro)
2. Envio de comprovante bancário / carro forte
3. Relato de exceções *(fora de escopo neste documento)*

---

## 2. Stakeholders

### 2.1 Mapa de Stakeholders

| Papel | Nome | Interesse | Poder de Decisão |
|-------|------|-----------|-----------------|
| Aprovador / Sponsor | Alcir Ricardo de Oliveira | ROI da automação, SLA de fechamento | Alto |
| Aprovador / Sponsor | Flavio Abade dos Santos | Conformidade operacional | Alto |
| Aprovador / Sponsor | Flavio de Queiroz Ramalho | Integração Retaguarda | Alto |
| Aprovadora / Negócio | Livia Dias Cordeiro | Regras de negócio corretas | Alto |
| Autora da Especificação | Selma Rodrigues | Fidelidade ao processo atual | Médio |
| Operador de Garagem | — (persona) | Facilidade de uso, rapidez | Médio |
| Analista de Backoffice | — (persona) | Redução de trabalho manual | Médio |
| Desenvolvedor / TI | — (persona) | Clareza de regras, testabilidade | Médio |

### 2.2 Personas Principais

#### Persona 1 — Responsável de Garagem (Enviador)
- **Contexto:** Gerente ou operador de uma garagem Estapar
- **Tarefa:** Ao final do dia, gera o relatório financeiro pelo sistema PDV local e o envia ao Portal
- **Dor:** Processo manual de envio; necessidade de reenvio em caso de erro
- **Necessidade:** Confirmação clara de que o fechamento foi aceito; identificação imediata do erro quando rejeitado

#### Persona 2 — Analista de Fechamento (Validador)
- **Contexto:** Equipe central de backoffice da Estapar
- **Tarefa:** Supervisiona o processo, age em casos de exceção, valida manualmente quando necessário
- **Dor:** Volume alto de garagens; layouts diferentes por sistema PDV; erro humano na leitura
- **Necessidade:** Dashboard de status de fechamento; alertas de divergência; histórico auditável

#### Persona 3 — Portal (Sistema)
- **Contexto:** Frontend/API Gateway entre o responsável da garagem e o orquestrador
- **Papel:** Recebe documentos, repassa ao orquestrador, apresenta resultados ao usuário

---

## 3. Escopo e Limites

### 3.1 Dentro do Escopo

| # | Funcionalidade | Prioridade |
|---|---------------|-----------|
| 1 | Busca de garagens com fechamento pendente (até 5 por consulta) | Must Have |
| 2 | Leitura OCR de relatório financeiro — 10 sistemas PDV | Must Have |
| 3 | Validação e cruzamento de dados do relatório financeiro | Must Have |
| 4 | Gravação dos dados validados no Retaguarda | Must Have |
| 5 | Leitura OCR de comprovante de depósito — 5 bancos + 4 modalidades | Must Have |
| 6 | Cruzamento valor relatório financeiro × comprovante (tolerância R$5,00) | Must Have |
| 7 | Validação de data do comprovante (regra D+1) | Must Have |
| 8 | Detecção de ID de depósito duplicado no Retaguarda | Must Have |
| 9 | Leitura de comprovante com múltiplos depósitos | Must Have |
| 10 | Validação de relatório de contingência (soma com financeiro principal) | Must Have |
| 11 | Suporte a garagens integradas e não-integradas | Must Have |
| 12 | Autenticação em dois fatores via Portal | Must Have |
| 13 | Detalhamento de erros de leitura/validação | Must Have |

### 3.2 Fora do Escopo (versão 1)

- Relato de exceções (frente 3) — documentado separadamente
- Interface visual do Portal (especificado separadamente)
- Treinamento de modelos de OCR (uso de serviço existente)
- Emissão de relatórios contábeis
- Integração com sistemas externos além do Retaguarda

### 3.3 Itens Pendentes / Em Aberto

| # | Item | Responsável | Impacto |
|---|------|------------|---------|
| P1 | Tabela de valores máximos de ticket/selo (RN08) | Negócio | ALTO — bloqueia validação de ticket médio |
| P2 | Status corretos a usar no Retaguarda após gravação (RN11) | TI/Retaguarda | ALTO — define estado do fechamento |
| P3 | Status do fechamento quando exceção identificada (RN14) | TI/Retaguarda | ALTO |
| P4 | Confirmação: carro forte é leitura OCR ou entrada manual? (RN18) | Negócio/TI | ALTO |
| P5 | Banco Inter — existe comprovante? Qual o layout? | Negócio | MÉDIO |
| P6 | Carro forte Modelo 02 — leitura manual ou OCR? | Negócio | MÉDIO |
| P7 | Padrão de nome da garagem no cofre inteligente Modelo 01 | TI | MÉDIO |
| P8 | Exemplo de TAG para SoftCase com arrecadação | Negócio | BAIXO |
| P9 | Cofre inteligente Modelo 02 — possibilidade de relatório extraído | TI | BAIXO |

---

## 4. Sistemas Envolvidos

### 4.1 Sistemas PDV — Relatório Financeiro

| Sistema | Formato | Campos-chave | Particularidades |
|---------|---------|-------------|-----------------|
| PDV / Estapar | Imagem/PDF | 12 campos | Rotativo = arrecadação − tags − selos − mensalistas − cartão pré-pago |
| Nepos | Imagem | 12 campos | Rotativo = total estadia − isenções; capturar todas linhas TAG |
| WPS - PDF | PDF | 9 campos | Desconsiderar tags e mensalistas; qtd=0 ignora |
| WPS - Fita | Imagem (fita) | 6 campos | Qtd zerada subtrai do total |
| Link | PDF multi-página | 14 campos | Qtd rotativo = soma pagamentos + pagantes; selos sem qtd → qtd=1 |
| Parco | Imagem | 7 campos | Rotativo = arrecadação (mesmo valor) |
| Prossiga / Perto | Imagem | 9 campos | Rotativo = arrecadação − tags − depósito |
| SoftCase | Imagem | 2 cenários | Sem arrecadação (movimento zero) ou com arrecadação (subtrai mensalistas) |
| CloudPark / Cosiensa | Imagem | 8 campos | Padrão, sem particularidades |
| AVC | Imagem | 5 campos | Rotativo = arrecadação total |
| RotaPark / Aucon | Imagem | 3 campos | Rotativo = arrecadação; valor e qtd depósito recebidos via Portal |

### 4.2 Bancos — Comprovante de Depósito

| Banco/Tipo | Modelos | ID Depósito | Valor | Favorecido |
|-----------|---------|------------|-------|-----------|
| Banco do Brasil | 1 | Numeração completa | ✅ | ✅ |
| Santander | 2 | M1: 6 últimos dígitos; M2: data+valor+hora ±1min | ✅ | ✅ |
| Caixa Econômica | 1 | 4 primeiros + máscara 0XXXX000000 | ✅ | ✅ |
| Itaú | 4 | M1/M2: N/A; M3: completo; M4: 4 dígitos antes dos 7 finais | ✅ | ✅ |
| Bradesco | 1 | 4 últimos dígitos máquina + 3 últimos N.transação | ✅ | ✅ |
| Autoatendimento | 1 | N/A | ✅ | ✅ |
| Carro forte | 3 | N/A — informado manualmente | ⚠️ M1/M3 apenas favorecido | ✅ |
| Cofre inteligente | 2 | N/A | ✅ | ✅ |
| Múltiplos depósitos | 1 | N° envelope | ✅ (soma ou individual ±R$5) | ✅ |

### 4.3 Sistemas Integrados

| Sistema | Papel | Tipo de Integração |
|---------|-------|-------------------|
| **Portal** | Frontend/orquestrador de interface | API (dispara e consome orquestrador) |
| **Retaguarda** | Sistema central de backoffice | API (consulta + gravação de fechamento) |
| **OCR Engine** | Motor de extração de texto | Serviço interno/externo |

---

## 5. Regras de Negócio Críticas

### 5.1 Fluxo Principal — Fechamento por Prestação

```
Portal → Orquestrador → Retaguarda (consulta garagens pendentes)
  ↓
Orquestrador recebe relatório financeiro do Portal
  ↓
OCR extrai dados → Orquestrador valida regras por sistema PDV
  ↓
Decisão:
  ├─ Sem contingência + com depósito → solicitar comprovante bancário
  ├─ Sem contingência + sem depósito → oferecer envio de exceção
  ├─ Com contingência → solicitar relatório de contingência
  ├─ Ticket médio acima do limite → obrigar relato de exceção
  └─ Divergência/ilegível → reportar erro detalhado
  ↓
Usuário confirma dados → Orquestrador grava no Retaguarda
```

### 5.2 Regras de Negócio Numeradas

| RN | Descrição | Fase |
|----|-----------|------|
| RN01 | Orquestrador recebe: código garagem, sistema, razão social, flag integração | Busca |
| RN02 | Listar até 5 datas com prestação em aberto por garagem; status ≠ "Prestação Finalizada" | Busca |
| RN03 | Autenticação em dois fatores entre Portal e Orquestrador | Busca |
| RN04 | Um documento/sistema por vez; finalizar para iniciar outro | Fechamento |
| RN05 | Depósito necessário quando valor ≠ R$0,00 no relatório financeiro | Fechamento |
| RN06 | Contingência: sistema diferente de SoftCase possui relatório de contingência | Fechamento |
| RN07 | Capturar todos os produtos listados na relação de produtos Retaguarda | Fechamento |
| RN08 | Ticket médio acima de [PENDENTE] → obrigar exceção | Fechamento |
| RN09 | Detalhar erro: período diferente, horário fora de range, garagem diferente, ilegível, info obrigatória ausente | Fechamento |
| RN10 | Gravar conforme produtos da relação Retaguarda | Gravação |
| RN11 | Status após gravação = "Documento recebido" [PENDENTE validação status] | Gravação |
| RN12 | Contingência: somar qtd e valor por produto (rotativo+rotativo, depósito+depósito, etc.) | Contingência |
| RN13 | Tolerância de R$5,00 entre relatório financeiro e comprovante depósito | Comprovante |
| RN14 | Status com exceção identificada = [PENDENTE] | Comprovante |
| RN15 | ID duplicado no Retaguarda → reportar que comprovante já vinculado | Comprovante |
| RN16 | Data comprovante = data fechamento + 1 dia (D+1) | Comprovante |
| RN17 | Múltiplos depósitos: verificar individual OU soma; se nenhum, identificar mais próximo | Comprovante |
| RN18 | Carro forte: leitura OCR ou entrada manual? [PENDENTE] | Comprovante |

### 5.3 Campos Obrigatórios no Relatório Financeiro

Para qualquer sistema PDV, os seguintes campos são obrigatórios:
1. **Data** do fechamento
2. **Valor e quantidade** da arrecadação total
3. **Valor e quantidade** do rotativo
4. **Valor do depósito** (mesmo que R$0,00)

### 5.4 Cenários de Erro e Divergência

| Código | Erro | Impeditivo? |
|--------|------|------------|
| ERR-01 | Período diferente do informado | Sim |
| ERR-02 | Horário fora do range 00:00–23:59 | Não (usuário pode confirmar e seguir) |
| ERR-03 | Garagem diferente da informada | Sim |
| ERR-04 | Documento ilegível (baixa resolução) | Sim |
| ERR-05 | Informação obrigatória não encontrada | Sim |
| ERR-06 | Valor comprovante diverge > R$5,00 do relatório | Parcial (oferece reenvio) |
| ERR-07 | ID depósito duplicado no Retaguarda | Sim |
| ERR-08 | Data comprovante diferente de D+1 | Não (solicita confirmação) |

---

## 6. Requisitos Não-Funcionais Identificados

| # | Categoria | Requisito Implícito | Origem |
|---|-----------|--------------------|----|
| NFR-I01 | Precisão OCR | Nível de confiança por campo; direcionamento para correção manual quando baixo | Descrição do OCR |
| NFR-I02 | Resiliência | Reprocessamento quando aplicável | Descrição Orquestrador |
| NFR-I03 | Auditoria | Rastreabilidade completa de cada ação do orquestrador | RN15, ERR-07 |
| NFR-I04 | Segurança | Autenticação em dois fatores entre Portal e Orquestrador | RN03 |
| NFR-I05 | Disponibilidade | Processo de fechamento é diário; alta disponibilidade noturna | Criticidade do processo |
| NFR-I06 | Performance | Leitura e validação de documentos em tempo aceitável para operação | Experiência de uso |
| NFR-I07 | Extensibilidade | Novos layouts de banco/PDV adicionáveis sem alteração core | 10+ sistemas hoje |
| NFR-I08 | LGPD | Dados pessoais (razão social, informações bancárias) devem ser protegidos | Dados sensíveis |
| NFR-I09 | Multi-sistema | Suporte a garagens integradas e não-integradas ao Retaguarda | RN01 |

---

## 7. Gaps e Riscos Identificados

### 7.1 Riscos de Negócio

| # | Risco | Probabilidade | Impacto | Mitigação |
|---|-------|--------------|---------|-----------|
| R01 | Itens pendentes (P1–P9) bloqueiam desenvolvimento de regras críticas | Alta | Alto | Validar com negócio antes do sprint 1 |
| R02 | Variações de layout dentro do mesmo banco/PDV não mapeadas | Média | Alto | Fase de treinamento OCR + feedback loop |
| R03 | Carro forte Modelo 02 é preenchido à mão → OCR não lerá | Alta | Médio | Fluxo de entrada manual como fallback |
| R04 | Relação de produtos Retaguarda está "em atualização" (marcada no doc) | Média | Alto | Usar tabela parcial; bloquear entrega até completar |
| R05 | Ticket médio sem tabela de referência (RN08) | Alta | Médio | Implementar trigger configurável |
| R06 | Alta diversidade de layouts (10 PDV + 9 banco/modalidade) | — | Alto | Testes exaustivos com exemplos reais por sistema |

### 7.2 Riscos Técnicos

| # | Risco | Impacto | Mitigação |
|---|-------|---------|-----------|
| RT01 | Documentos de baixa resolução → falha de OCR em imagens de fita | Alto | Definir resolução mínima aceitável; alertar usuário |
| RT02 | Concatenação de IDs de depósito (Santander M2, Bradesco) → edge cases | Médio | Suite de testes unitários por banco |
| RT03 | Tolerância de R$5,00 pode gerar falsos positivos/negativos | Médio | Parametrizar e logar todos os casos limítrofes |
| RT04 | API do Retaguarda pode não ter todos os endpoints necessários | Alto | Mapear endpoints existentes antes do sprint 1 |

---

## 8. Decisões Assumidas (baseadas na especificação)

| Decisão | Base |
|---------|------|
| OCR como serviço dedicado (não integrado ao orquestrador) | Descrição arquitetural da spec |
| Um documento por sistema por vez (sem paralelismo de fechamento) | RN04 |
| Portal como único ponto de entrada (usuário nunca interage direto com orquestrador) | Fluxo macro |
| Retaguarda como sistema de destino (read + write) | RN01, RN10, RN11 |
| Contingência = soma de quantidades e valores por produto | RN12 |
| Tolerância de depósito = R$5,00 (não negociável por ora) | RN13 |
| D+1 como regra padrão de data de comprovante | RN16 |

---

## 9. Handoff para @prd (Scout → Priya)

```
PROJETO: estapar-ocr
PROBLEMA: Automação do Fechamento Diário de Prestação da Estapar.
          Processo manual com 10 sistemas PDV + 9 tipos de comprovante bancário.
          Alto volume, propenso a erro, sem rastreabilidade automática.

STAKEHOLDERS APROVADORES: Alcir Oliveira, Flavio Abade, Flavio Ramalho, Livia Cordeiro
PERSONAS: Responsável de Garagem (enviador) | Analista de Fechamento (supervisor)

FRENTES: Este documento cobre frentes 1 (fechamento) e 2 (comprovante bancário)
FRENTE 3 (exceções): fora de escopo desta especificação

SISTEMAS PDV (10): PDV/Estapar, Nepos, WPS-PDF, WPS-Fita, Link, Parco,
                   Prossiga/Perto, SoftCase, CloudPark/Cosiensa, AVC, RotaPark/Aucon
BANCOS (9+):        BB, Santander(2), Caixa, Itaú(4), Bradesco, Autoatendimento,
                    Carro forte(3), Cofre inteligente(2), Múltiplos depósitos

REGRAS CRÍTICAS:
  - Rotativo: calculado de forma distinta por sistema (ver RN07 + tabela 3.2.2)
  - Tolerância depósito: R$5,00 (RN13)
  - Data comprovante: D+1 (RN16)
  - ID duplicado: bloqueia (RN15)
  - Campos obrigatórios: data, arrecadação (qtd+valor), rotativo (qtd+valor), valor depósito

ITENS PENDENTES (bloqueiam PRD em partes):
  P1: Tabela valores máximos ticket/selo (RN08) → NFR de validação de ticket médio
  P2: Status Retaguarda pós-gravação (RN11) → FR de integração
  P3: Status com exceção (RN14) → FR de exceção
  P4: Carro forte: OCR ou manual? (RN18) → FR de comprovante

COMPLEXIDADE ESTIMADA: STANDARD (10 layouts PDV × 9 tipos de comprovante × regras de negócio específicas)
INTEGRAÇÃO CHAVE: Portal (API in) + Retaguarda (API out) + OCR Engine (serviço)
```

---

## 10. Aprovação do Discovery

| Critério | Status |
|---------|--------|
| Problema de negócio documentado | ✅ |
| Stakeholders mapeados | ✅ |
| Escopo definido (in/out) | ✅ |
| Regras de negócio extraídas | ✅ |
| Sistemas integrados identificados | ✅ |
| Itens em aberto documentados | ✅ |
| Riscos identificados | ✅ |
| Handoff para @prd escrito | ✅ |
