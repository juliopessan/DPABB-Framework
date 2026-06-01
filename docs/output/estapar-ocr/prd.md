# PRD — Estapar OCR/Orquestrador — Fechamento de Caixa

**Agente:** @prd (Priya)  
**Data:** 2026-05-31  
**Projeto:** estapar-ocr  
**Versão:** 1.0  
**Status:** Draft  
**Discovery:** [discovery-report.md](discovery-report.md)

---

## 1. Visão do Produto

### 1.1 Declaração de Posicionamento

**Para** analistas de fechamento e responsáveis de garagem da Estapar,  
**que** precisam realizar o fechamento diário de prestação de forma manual e sujeita a erros,  
**o** sistema OCR/Orquestrador  
**é** uma plataforma de automação inteligente  
**que** extrai, valida e registra automaticamente os dados dos relatórios financeiros e comprovantes bancários,  
**diferente de** processos manuais ou automações parciais,  
**nosso produto** garante rastreabilidade completa, redução de erros e fechamento contábil mais rápido.

### 1.2 Objetivo de Negócio

| Objetivo | Métrica de Sucesso |
|----------|-------------------|
| Reduzir tempo médio de fechamento por garagem | Redução ≥ 60% do tempo atual |
| Eliminar erros de digitação no lançamento Retaguarda | Taxa de erro < 1% |
| Aumentar cobertura de validação automática | ≥ 95% dos fechamentos processados automaticamente |
| Garantir auditabilidade | 100% das ações rastreadas com timestamp e agente |

---

## 2. Personas

### Persona 1 — Responsável de Garagem (Enviador)
| Atributo | Valor |
|----------|-------|
| Papel | Gerente ou operador de garagem Estapar |
| Objetivo | Enviar o fechamento do dia e receber confirmação rapidamente |
| Frustrações | Reenvios frequentes por erros, falta de feedback claro sobre o problema |
| Contexto | Opera um sistema PDV local (1 de 11 sistemas possíveis); envia relatório ao final do dia |
| Critério de sucesso | Saber em ≤ 2 minutos se o fechamento foi aceito ou o que precisa corrigir |

### Persona 2 — Analista de Fechamento (Supervisor)
| Atributo | Valor |
|----------|-------|
| Papel | Equipe de backoffice central da Estapar |
| Objetivo | Supervisionar fechamentos de centenas de garagens com mínima intervenção manual |
| Frustrações | Volume alto, formatos diferentes por sistema, erros não detectados na digitação |
| Contexto | Acessa o Portal para revisar exceções e fechamentos pendentes |
| Critério de sucesso | Intervir manualmente em ≤ 5% dos fechamentos |

---

## 3. Requisitos Funcionais

### ÉPICO 01 — Consulta de Garagens (Módulo Orquestrador)

#### FR-01: Buscar garagens com prestação pendente
**Como** o Portal,  
**quero** consultar o orquestrador com código de garagem, sistema, razão social e flag de integração,  
**para que** o orquestrador retorne até 5 datas com prestações em aberto.

**Critérios de Aceite:**
- [ ] Dado que o Portal envia os 4 parâmetros obrigatórios (código, sistema, razão social, integração)
- [ ] Quando o orquestrador consulta o Retaguarda
- [ ] Então retorna lista de até 5 datas com status diferente de "Prestação Finalizada"
- [ ] E inclui para cada data: a data e seu status atual
- [ ] E datas com status "Faturamento Publicado" são disponibilizadas apenas para envio de comprovante ou exceção
- [ ] Quando não há datas pendentes, retorna indicação clara de "não encontrado"

**Regras:** RN01, RN02  
**Prioridade:** Must Have  
**Estimativa:** M (8 pts)

---

#### FR-02: Autenticação em dois fatores Portal ↔ Orquestrador
**Como** o sistema,  
**quero** validar a identidade do Portal em cada requisição,  
**para que** apenas origens autorizadas possam acionar o orquestrador.

**Critérios de Aceite:**
- [ ] Dado que o Portal envia uma requisição ao orquestrador
- [ ] Quando o token/mecanismo de autenticação é inválido ou ausente
- [ ] Então o orquestrador rejeita a requisição com erro de autenticação

**Regras:** RN03  
**Prioridade:** Must Have  
**Estimativa:** S (3 pts)

---

### ÉPICO 02 — Leitura e Validação do Relatório Financeiro (Módulo OCR)

#### FR-03: Identificar sistema PDV pelo relatório
**Como** o OCR,  
**quero** identificar automaticamente o sistema PDV (quando aplicável),  
**para que** as regras de extração corretas sejam aplicadas.

**Critérios de Aceite:**
- [ ] Dado que o Portal envia um relatório financeiro com o sistema indicado
- [ ] Quando o OCR recebe o documento
- [ ] Então aplica o parser correto para o sistema indicado
- [ ] E atribui nível de confiança por campo extraído

**Prioridade:** Must Have  
**Estimativa:** M (8 pts)

---

#### FR-04: Extrair dados do relatório — PDV / Estapar
**Campos obrigatórios:** Período, Nome da garagem, Qtd mensalista, Valor mensalista, Valor depósito, Qtd selos, Valor selos, Tags (produtos Retaguarda), Qtd total arrecadação, Valor total arrecadação, Qtd cartão débito, Valor cartão débito  
**Cálculo rotativo:** Arrecadação − tags − selos − mensalistas − utilização cartão pré-pago

**Critérios de Aceite:**
- [ ] Dado relatório PDV/Estapar enviado
- [ ] Então os 12 campos são extraídos com nível de confiança
- [ ] E o rotativo é calculado conforme fórmula
**Prioridade:** Must Have | **Estimativa:** M (8 pts)

---

#### FR-05: Extrair dados do relatório — Nepos
**Campos:** Nome garagem, Data início/fim, Valor mensalista (Revalidação), Valor depósito (Total dinheiro), Qtd produto (por linha), Valor total arrecadação (Total estadia − isenções), Qtd total arrecadação, Descrição TAG (todas as linhas + qtd + valor)  
**Cálculo rotativo:** Total estadia − Total isenções. Subtrair Revalidação do total de arrecadação.

**Critérios de Aceite:**
- [ ] Dado relatório Nepos enviado
- [ ] Então todos os campos e linhas de TAG são capturados
- [ ] E rotativo é calculado com as subtrações corretas
**Prioridade:** Must Have | **Estimativa:** M (8 pts)

---

#### FR-06: Extrair dados do relatório — WPS (PDF e Fita)
**WPS-PDF:** Data, Valor depósito, TAGs, Qtd rotativo (desconsiderar qtd=0), Total arrecadação, Selos, Mensalistas. Rotativo = arrecadação − TAGs − selos − mensalistas.  
**WPS-Fita:** Período, Valor depósito, Qtd arrecadação (desconsiderar zerados), Arrecadação (=Rotativo), TAGs, Desconto.

**Prioridade:** Must Have | **Estimativa:** L (13 pts)

---

#### FR-07: Extrair dados do relatório — Link
**Campos:** Data, Qtd rotativo (soma pagamentos+pagantes), Valor rotativo, Qtd mensalista, Valor mensalista, Qtd arrecadação, Valor depósito, TAG (selos sem qtd → considerar qtd=1), Valor arrecadação, Qtd selo, Valor selo.

**Prioridade:** Must Have | **Estimativa:** M (8 pts)

---

#### FR-08: Extrair dados do relatório — Parco
**Campos:** Nome garagem, Data, Qtd rotativo (soma de múltiplas linhas), Valor rotativo (soma), Valor depósito.  
**Nota:** Rotativo = Arrecadação (mesmo valor neste sistema).

**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

#### FR-09: Extrair dados do relatório — Prossiga / Perto
**Campos:** Período, Nome garagem, Qtd tag, Valor tag, Qtd depósito, Valor depósito, Qtd total arrecadação, Valor total arrecadação.  
**Cálculo rotativo:** Arrecadação − (tags + depósito).

**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

#### FR-10: Extrair dados do relatório — SoftCase
**Cenário sem arrecadação:** Identificar movimento zero.  
**Cenário com arrecadação:** Período, Valor rotativo, Valor arrecadação (Faturado − Mensalista), Qtd arrecadação (Composição por tipo − S/Cobr.), Qtd rotativo (Total − S/Cobr.), Valor depósito.

**Prioridade:** Must Have | **Estimativa:** M (8 pts)

---

#### FR-11: Extrair dados do relatório — CloudPark / Cosiensa
**Campos:** Período, Qtd mensalista, Valor mensalista, Qtd rotativo, Total rotativo, Qtd total arrecadação, Valor total arrecadação, Valor depósito.

**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

#### FR-12: Extrair dados do relatório — AVC e RotaPark/Aucon
**AVC:** Período, Qtd rotativo (= Qtd arrecadação), Valor rotativo (= Valor arrecadação), Qtd dinheiro, Valor dinheiro (= Valor depósito).  
**RotaPark/Aucon:** Período, Qtd rotativo (= Qtd arrecadação), Valor rotativo (= Valor arrecadação). Valor e qtd depósito informados via Portal.

**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

### ÉPICO 03 — Validação de Regras de Negócio (Módulo Orquestrador)

#### FR-13: Validar campos obrigatórios do relatório financeiro
**Campos obrigatórios (qualquer sistema):** Data, Valor e qtd de arrecadação, Valor e qtd do rotativo, Valor depósito (mesmo que R$0,00).

**Critérios de Aceite:**
- [ ] Dado que o OCR extrai os dados
- [ ] Quando um ou mais campos obrigatórios não são identificados
- [ ] Então retorna lista com todos os campos ausentes
- [ ] E o fechamento não avança até que os dados sejam corrigidos/confirmados

**Regras:** RN09  
**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

#### FR-14: Validar período e garagem do relatório
**Critérios de Aceite:**
- [ ] Período diferente → retornar ERR-01 (impeditivo)
- [ ] Horário fora de 00:00–23:59 → retornar ERR-02 (não impeditivo; usuário confirma e segue)
- [ ] Garagem diferente da indicada → retornar ERR-03 (impeditivo)
- [ ] Documento ilegível → retornar ERR-04 (impeditivo)

**Regras:** RN09  
**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

#### FR-15: Verificar necessidade de comprovante de depósito
**Critérios de Aceite:**
- [ ] Quando valor de depósito no relatório ≠ R$0,00 → sinalizar obrigatoriedade de comprovante
- [ ] Quando valor = R$0,00 → oferecer opção de relatar exceção

**Regras:** RN05  
**Prioridade:** Must Have | **Estimativa:** XS (1 pt)

---

#### FR-16: Verificar necessidade de relatório de contingência
**Critérios de Aceite:**
- [ ] Quando sistema PDV da garagem ≠ SoftCase → sinalizar que pode haver relatório de contingência
- [ ] Quando usuário indica contingência → solicitar envio do segundo relatório

**Regras:** RN06  
**Prioridade:** Must Have | **Estimativa:** XS (1 pt)

---

#### FR-17: Validar ticket médio (valor máximo)
**Critérios de Aceite:**
- [ ] Dado que o orquestrador calculou o ticket médio (valor × quantidade)
- [ ] Quando ticket médio > valor máximo da tabela de produtos [PENDENTE P1]
- [ ] Então obrigar relato de exceção antes de confirmar fechamento

**Regras:** RN08  
**Prioridade:** Must Have | **Estimativa:** S (3 pts) — **BLOQUEADO por P1**

---

#### FR-18: Processar mensalistas em sistemas específicos (Nepos, WPS, Link)
**Critérios de Aceite:**
- [ ] Quando sistema é Nepos, WPS ou Link e relatório contém mensalistas
- [ ] Então descontar valor e quantidade do mensalista do total de arrecadação
- [ ] E destacar para o usuário que mensalistas foram desconsiderados

**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

#### FR-19: Processar relatório de contingência
**Critérios de Aceite:**
- [ ] Dado que o usuário enviou um segundo relatório (contingência)
- [ ] Quando o OCR processa o segundo documento
- [ ] Então somar quantidades e valores por produto (rotativo+rotativo, depósito+depósito, etc.)
- [ ] E inserir dados somados no Retaguarda

**Regras:** RN12  
**Prioridade:** Must Have | **Estimativa:** M (8 pts)

---

### ÉPICO 04 — Gravação no Retaguarda

#### FR-20: Gravar dados do fechamento no Retaguarda
**Critérios de Aceite:**
- [ ] Dado que o usuário confirmou os dados validados
- [ ] Quando a garagem é integrada → gravar apenas os dados processados
- [ ] Quando a garagem não é integrada → gravar dados + relatório financeiro (evidência)
- [ ] Então alterar status para "Documento recebido" [PENDENTE P2]
- [ ] E os produtos gravados devem seguir a relação de produtos do Retaguarda

**Regras:** RN10, RN11  
**Prioridade:** Must Have | **Estimativa:** M (8 pts)

---

### ÉPICO 05 — Comprovante de Depósito (Módulo OCR + Orquestrador)

#### FR-21: Extrair dados do comprovante — Banco do Brasil
**Campos:** Data (01), Favorecido/Razão Social (02), ID depósito (03 — numeração completa), Valor (04)  
**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

#### FR-22: Extrair dados do comprovante — Santander (2 modelos)
**Modelo 1:** Data, Favorecido, ID (6 últimos dígitos), Valor  
**Modelo 2:** Data, ID (concatenar data+valor+hora ±1 min), Favorecido, Valor

**Prioridade:** Must Have | **Estimativa:** M (8 pts)

---

#### FR-23: Extrair dados do comprovante — Caixa Econômica
**Campos:** Data, ID (4 primeiros dígitos + máscara: 0XXXX000000), Favorecido, Valor  

**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

#### FR-24: Extrair dados do comprovante — Itaú (4 modelos)
**Modelo 1:** Data, Favorecido, Valor — ID N/A  
**Modelo 2:** Data, Favorecido, Valor — ID N/A  
**Modelo 3:** ID completo, Data, Valor, Favorecido  
**Modelo 4:** Data, Favorecido, Valor, ID (4 dígitos que antecedem os 7 últimos)

**Prioridade:** Must Have | **Estimativa:** L (13 pts)

---

#### FR-25: Extrair dados do comprovante — Bradesco
**Campos:** Data, ID (4 últimos dígitos máquina + 3 últimos N.transação), Favorecido, Valor

**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

#### FR-26: Extrair dados do comprovante — Autoatendimento
**Campos:** Data, Favorecido, Valor — ID N/A

**Prioridade:** Must Have | **Estimativa:** XS (1 pt)

---

#### FR-27: Processar comprovante de carro forte
**Modelo 1:** Favorecido, Data — Valor N/A, ID N/A  
**Modelo 2:** [PENDENTE P6 — leitura manual ou OCR?]  
**Modelo 3:** Favorecido, Data — Valor N/A, ID N/A  
**Nota:** Para carro forte, o usuário deve informar o valor para validação (RN18)

**Prioridade:** Must Have | **Estimativa:** M (8 pts)

---

#### FR-28: Processar comprovante com múltiplos depósitos
**Campos:** Data, Favorecido, N° envelope e valor de cada depósito, Soma total  
**Lógica:** Validar se valor do relatório = valor de 1 depósito (±R$5,00) OU = soma de todos. Se nenhum, identificar valor mais próximo e solicitar confirmação.

**Regras:** RN17  
**Prioridade:** Must Have | **Estimativa:** M (8 pts)

---

#### FR-29: Validar valor do comprovante versus relatório financeiro
**Critérios de Aceite:**
- [ ] Dado que o comprovante foi processado
- [ ] Quando valor comprovante = valor relatório (±R$5,00) → informar que valor esperado foi atendido
- [ ] Quando diferença > R$5,00 → indicar divergência e oferecer relatar exceção ou reenviar
- [ ] Orquestrador pode receber 1 ou N comprovantes por dia de fechamento

**Regras:** RN13  
**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

#### FR-30: Validar data do comprovante (D+1)
**Critérios de Aceite:**
- [ ] Data do comprovante deve ser a data do fechamento + 1 dia
- [ ] Se data diferente → reportar e solicitar confirmação do usuário (não impeditivo por padrão)

**Regras:** RN16  
**Prioridade:** Must Have | **Estimativa:** XS (1 pt)

---

#### FR-31: Validar ID de depósito duplicado
**Critérios de Aceite:**
- [ ] Dado que o comprovante tem ID de identificação
- [ ] Quando o orquestrador consulta o Retaguarda e o ID já existe
- [ ] Então reportar erro ao Portal indicando que o comprovante já está vinculado a outro fechamento

**Regras:** RN15  
**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

#### FR-32: Validar favorecido do comprovante
**Critérios de Aceite:**
- [ ] Favorecido do comprovante deve corresponder à razão social da garagem
- [ ] Divergência → reportar ao Portal

**Prioridade:** Must Have | **Estimativa:** S (3 pts)

---

## 4. Requisitos Não-Funcionais

| ID | Categoria | Descrição | Critério Mensurável | Prioridade |
|----|-----------|-----------|--------------------|-----------| 
| NFR-01 | Precisão OCR | Nível de confiança mínimo por campo | ≥ 90% de confiança por campo; < 90% → revisão manual | Must Have |
| NFR-02 | Performance | Tempo de processamento por documento | ≤ 30 segundos por relatório financeiro; ≤ 15 segundos por comprovante | Must Have |
| NFR-03 | Disponibilidade | Disponibilidade do serviço | ≥ 99,5% uptime em horário de operação (06h–23h) | Must Have |
| NFR-04 | Segurança — Autenticação | Autenticação Portal ↔ Orquestrador | Auth em dois fatores (RN03); token JWT com expiração ≤ 1h | Must Have |
| NFR-05 | Segurança — Dados em trânsito | Criptografia de documentos e dados trafegados | TLS 1.2+ em todas as comunicações | Must Have |
| NFR-06 | Segurança — Dados em repouso | Proteção de documentos armazenados | AES-256 para documentos temporários; retenção ≤ 24h após processamento | Must Have |
| NFR-07 | Auditoria | Rastreabilidade completa de ações | 100% das ações com timestamp, agente, resultado; retenção ≥ 90 dias | Must Have |
| NFR-08 | LGPD | Proteção de dados pessoais | Razão social, dados bancários: criptografados; soft delete + anonimização; política de retenção documentada | Must Have |
| NFR-09 | Extensibilidade | Adição de novos layouts | Novo sistema PDV ou banco: ≤ 5 dias de desenvolvimento sem alteração do core | Should Have |
| NFR-10 | Resiliência | Reprocessamento automático | Reprocessar documento com falha sem intervenção manual; máx 3 tentativas | Should Have |
| NFR-11 | Observabilidade | Monitoramento e alertas | Dashboard de processamento por garagem/dia; alerta em < 5 min em falha crítica | Should Have |
| NFR-12 | Configurabilidade | Parâmetros configuráveis sem deploy | Tolerância R$5,00 e tabela de ticket máximo: configuráveis via painel admin | Should Have |
| NFR-13 | Escalabilidade | Suporte a volume crescente | Processar ≥ 500 fechamentos simultâneos sem degradação de performance | Could Have |

---

## 5. Rastreabilidade

### FR → RN (Regras de Negócio)

| FR | RN / Regra | Fonte |
|----|-----------|-------|
| FR-01 | RN01, RN02 | Spec §3.1 |
| FR-02 | RN03 | Spec §3.1 |
| FR-04 a FR-12 | RN07 | Spec §3.2.2 |
| FR-13 | RN09 | Spec §3.2.1 |
| FR-14 | RN09 | Spec §3.2.1 |
| FR-15 | RN05 | Spec §3.2.1 |
| FR-16 | RN06 | Spec §3.2.1 |
| FR-17 | RN08 | Spec §3.2.1 |
| FR-18 | Cenário 04 | Spec §3.2.1 |
| FR-19 | RN12 | Spec §3.2.4 |
| FR-20 | RN10, RN11 | Spec §3.2.3 |
| FR-21 a FR-26 | — | Spec §3.3.1 |
| FR-27 | RN18 | Spec §3.3.1 |
| FR-28 | RN17 | Spec §3.3.1 |
| FR-29 | RN13 | Spec §3.3 |
| FR-30 | RN16 | Spec §3.3 |
| FR-31 | RN15 | Spec §3.3 |
| FR-32 | — | Spec §3.3.1 |

### Persona → FR

| Persona | FRs Principais |
|---------|---------------|
| Responsável de Garagem | FR-01, FR-03 a FR-19, FR-21 a FR-32 |
| Analista de Fechamento | FR-01, FR-14, FR-17, FR-20, FR-29 a FR-32 |
| Sistema Portal | FR-02, FR-01 (consulta), FR-20 (confirmação) |

---

## 6. Fora de Escopo

| Item | Motivo |
|------|--------|
| Interface visual do Portal | Especificado separadamente |
| Frente 3 — Relato de exceções | Documento separado |
| Treinamento de modelo OCR | Uso de serviço existente |
| Relatórios contábeis/fiscais | Fora do escopo funcional |
| Banco Inter | Pendente definição de layout (P5) |

---

## 7. Restrições e Dependências

| Tipo | Descrição |
|------|-----------|
| Dependência | API do Retaguarda deve expor endpoints: GET garagens pendentes, POST gravar fechamento, GET verificar ID comprovante |
| Dependência | OCR Engine deve retornar: campos extraídos + nível de confiança por campo |
| Restrição | Tabela de produtos Retaguarda ainda em atualização — bloqueia FR-20 parcialmente |
| Restrição | Tabela de ticket máximo pendente (P1) — bloqueia FR-17 |
| Restrição | Status do Retaguarda pendente (P2, P3) — bloqueia FR-20 e FR-29 parcialmente |

---

## 8. Handoff para @architect (Priya → Aria)

```
PROJETO: estapar-ocr
TIPO: Sistema de automação com OCR + Orquestrador de processos

COMPONENTES PRINCIPAIS:
  - OCR Engine (extração): 11 parsers PDV + 9 parsers bancários
  - Orquestrador (validação + roteamento): 32 FRs, fluxo stateful
  - API Gateway / Portal: ponto de entrada
  - Retaguarda: sistema legado de destino (API read+write)

NFRS CRÍTICOS PARA ARQUITETURA:
  - NFR-01: Precisão OCR ≥ 90% por campo → pipeline de validação por campo
  - NFR-02: Performance ≤ 30s relatório; ≤ 15s comprovante → processamento assíncrono
  - NFR-03: Disponibilidade ≥ 99,5% → alta disponibilidade, health checks
  - NFR-04/05/06: TLS 1.2+, Auth dois fatores, AES-256 → security by design
  - NFR-07: Auditoria 100% → log estruturado em todos os steps
  - NFR-09: Novo layout ≤ 5 dias → pattern de adapters/plugins por sistema

PADRÕES RECOMENDADOS:
  - Adapter/Strategy por sistema PDV (11 parsers independentes)
  - Adapter/Strategy por banco (9+ parsers independentes)
  - Pipeline de processamento: OCR → Validate → Enrich → Route → Persist
  - Armazenamento temporário de documentos (≤ 24h — NFR-06)
  - Fila de processamento para resiliência (NFR-10)

INTEGRAÇÕES EXTERNAS:
  - Portal: REST API IN (recebe documentos + parâmetros de garagem)
  - Retaguarda: REST API OUT (consulta + gravação)
  - OCR Engine: serviço interno ou externo (Azure Cognitive Services / Document Intelligence)

ITENS PENDENTES QUE AFETAM ARQUITETURA:
  - Tabela de ticket máximo (P1) → implementar como config externa
  - Status Retaguarda (P2, P3) → mapear antes do sprint 1
  - Carro forte (P4) → fallback de entrada manual

COMPLEXIDADE: STANDARD
TOTAL FRs: 32 (29 Must Have, 3 bloqueados por pendências)
TOTAL NFRs: 13 (9 Must Have, 4 Should/Could Have)
```
