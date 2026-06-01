# Architecture — Estapar OCR/Orquestrador — Fechamento de Caixa

**Versão:** 1.0  
**Data:** 2026-05-31  
**Autor:** @architect (Aria)  
**Metodologia:** TOGAF 10 — ADM Phases A–E  
**PRD:** [prd.md](prd.md)  
**Status:** Draft

---

## Phase A — Architecture Vision

### A.1 Declaração de Problema de Negócio

**Capacidade atual (As-Is):** Fechamento de prestação diária realizado manualmente — operadores enviam relatórios em PDF/imagem de 11 sistemas PDV distintos, analistas validam e digitam dados no Retaguarda.

**Capacidade alvo (To-Be):** OCR extrai dados automaticamente de qualquer layout, orquestrador aplica regras de negócio por sistema e valida comprovantes bancários de 9+ tipos, gravando no Retaguarda sem intervenção humana na operação padrão.

**Gap:** Falta de camada inteligente de extração (OCR) e orquestração (regras por sistema) entre o Portal e o Retaguarda.

### A.2 Princípios Arquiteturais

| # | Princípio | Implicação |
|---|-----------|-----------|
| PA-01 | Security by Design | TLS 1.2+, auth dois fatores Portal↔Orquestrador, AES-256 em repouso desde o MVP |
| PA-02 | Observability First | Logs estruturados + métricas em cada step do pipeline; alerta < 5 min em falha crítica |
| PA-03 | Extensibility by Adapter | Novo PDV ou banco = novo adapter, sem tocar no core do orquestrador |
| PA-04 | Async Processing | Processamento OCR em fila — evita timeout e garante resiliência (max 3 retries) |
| PA-05 | Configuration over Code | Tolerância R$5,00 e tabela de ticket máximo configuráveis via admin sem deploy |
| PA-06 | Audit Everything | 100% das ações com timestamp, usuário/sistema, resultado — retenção 90 dias |

### A.3 Stakeholder Architecture Concerns

| Stakeholder | Preocupação | Como a arquitetura endereça |
|-------------|------------|---------------------------|
| Alcir Oliveira / Flavio Abade | ROI e confiabilidade operacional | Pipeline assíncrono com retry; precisão OCR ≥ 90% por campo |
| Flavio Ramalho | Integração com Retaguarda legado | Adapter pattern isola Retaguarda — mudanças na API não afetam o core |
| Livia Cordeiro | Fidelidade às regras de negócio | Strategy pattern por sistema PDV — cada regra é testável isoladamente |
| Analista de Fechamento | Visibilidade de exceções | App Insights + Dashboard de processamento; alertas automáticos |
| Responsável de Garagem | Feedback rápido sobre erros | Response em ≤ 30s com descrição detalhada do campo com problema |

### A.4 Diagrama de Contexto (C4 Level 1)

```mermaid
architecture-beta
  group internet(mdi:web)[Internet / Operadores]
  group cloud(azure:azure)[Azure Cloud — Estapar OCR]
  group legacy(mdi:server)[Sistemas Legados]

  service garagem(mdi:account-group)[Responsável de Garagem] in internet
  service analista(mdi:monitor)[Analista de Fechamento] in internet

  service portal(azure:app-service)[Portal Estapar] in cloud
  service apim(azure:api-management)[API Gateway] in cloud
  service orch(azure:functions)[Orquestrador] in cloud
  service ocr(azure:cognitive-services)[OCR Engine] in cloud
  service bus(azure:service-bus)[Fila de Processamento] in cloud
  service storage(azure:storage-accounts)[Document Storage] in cloud
  service monitor(azure:monitor)[Monitor + App Insights] in cloud

  service retaguarda(mdi:database)[Sistema Retaguarda] in legacy
  service aad(azure:azure-active-directory)[Entra ID] in cloud

  garagem:R --> L:portal
  analista:R --> L:portal
  portal:R --> L:apim
  apim:R --> L:orch
  orch:B --> T:bus
  bus:R --> L:ocr
  ocr:R --> L:storage
  orch:R --> L:retaguarda
  orch:T --> B:aad
  orch:L --> R:monitor
```

### A.5 Classificação de Complexidade

| Dimensão | Score (1-5) | Justificativa |
|----------|------------|--------------|
| Escopo | 4 | 32 FRs, 11 sistemas PDV, 9+ bancos |
| Integração | 3 | 2 sistemas externos (Portal + Retaguarda) + OCR Engine |
| Infraestrutura | 3 | Cloud Azure, filas, storage temporário — sem on-premise novo |
| Conhecimento | 3 | OCR como serviço gerenciado; regras de negócio detalhadas na spec |
| Risco | 4 | Pendências P1-P4 não resolvidas; diversidade de layouts |
| **Total** | **17/25** | |

**Classe:** STANDARD  
**Estilo recomendado:** Monolito Modular (orquestrador único com módulos independentes por domínio)

---

### A.6 Legenda de Componentes Azure

<div align="center">

#### Camada de Entrada e Processamento

| <img src="../../../../assets/icons/azure/devops/10042-icon-service-API-Management-Services.svg" width="40"><br>**API Management** | <img src="../../../../assets/icons/azure/compute/10029-icon-service-Function-Apps.svg" width="40"><br>**Orquestrador (Functions)** | <img src="../../../../assets/icons/azure/learning/03438-icon-service-Azure-OpenAI-Service.svg" width="40"><br>**OCR (Cognitive Services)** | <img src="../../../../assets/icons/azure/integration/10836-icon-service-Service-Bus.svg" width="40"><br>**Service Bus (Fila)** |
|:---:|:---:|:---:|:---:|
| `azure:api-management` | `azure:functions` | `azure:cognitive-services` | `azure:service-bus` |

#### Camada de Dados e Segurança

| <img src="../../../../assets/icons/azure/databases/10130-icon-service-SQL-Database.svg" width="40"><br>**SQL Database (Audit/Config)** | <img src="../../../../assets/icons/azure/storage/10086-icon-service-Storage-Accounts.svg" width="40"><br>**Blob Storage (Docs)** | <img src="../../../../assets/icons/azure/security/10245-icon-service-Key-Vaults.svg" width="40"><br>**Key Vault** | <img src="../../../../assets/icons/azure/identity/10230-icon-service-Azure-Active-Directory.svg" width="40"><br>**Entra ID** |
|:---:|:---:|:---:|:---:|
| `azure:sql-database` | `azure:storage-accounts` | `azure:key-vault` | `azure:azure-active-directory` |

#### Observabilidade e DevOps

| <img src="../../../../assets/icons/azure/monitor/00001-icon-service-Monitor.svg" width="40"><br>**Azure Monitor** | <img src="../../../../assets/icons/azure/devops/00012-icon-service-Application-Insights.svg" width="40"><br>**App Insights** | <img src="../../../../assets/icons/azure/devops/10261-icon-service-Azure-DevOps.svg" width="40"><br>**Azure DevOps** | <img src="../../../../assets/icons/azure/containers/10105-icon-service-Container-Registries.svg" width="40"><br>**Container Registry** |
|:---:|:---:|:---:|:---:|
| `azure:monitor` | `azure:application-insights` | `azure:devops` | `azure:container-registry` |

</div>

---

## Phase B — Business Architecture

### B.1 Mapa de Capacidades de Negócio

```mermaid
mindmap
  root(Estapar OCR/Orquestrador)
    Fechamento por Prestação
      Consultar garagens pendentes
      Ler relatório financeiro OCR
      Validar regras por sistema PDV
      Processar contingência
      Gravar no Retaguarda
    Comprovante de Depósito
      Ler comprovante OCR por banco
      Validar valor vs relatório
      Validar data D+1
      Detectar ID duplicado
      Processar múltiplos depósitos
    Gestão de Configurações
      Tabela de ticket máximo
      Tolerância de depósito
      Relação de produtos Retaguarda
    Observabilidade e Auditoria
      Log de todas as ações
      Dashboard de fechamentos
      Alertas de divergência
      Rastreabilidade de documentos
```

### B.2 Fluxo de Processo — To-Be

```mermaid
flowchart TD
  A([🚀 Início: Responsável de Garagem]) --> B[Portal: selecionar garagem e data]
  B --> C[Orquestrador: consultar Retaguarda]
  C --> D[Retornar datas pendentes]
  D --> E[Portal: usuário envia relatório financeiro]
  E --> F[Fila: enfileirar documento]
  F --> G[OCR: extrair dados por sistema PDV]
  G --> H{Orquestrador: validar campos obrigatórios}

  H -->|Faltam campos| I[Retornar erro detalhado ao Portal]
  I --> E

  H -->|OK| J{Verificar necessidade de depósito}
  J -->|Valor > R$0| K[Solicitar comprovante bancário]
  J -->|Valor = R$0| L[Oferecer relato de exceção]

  H -->|Sistema ≠ SoftCase| M[Solicitar relatório de contingência]
  M --> N[OCR: processar contingência]
  N --> O[Somar qtd/valor por produto]
  O --> P

  K --> Q[OCR: extrair dados comprovante por banco]
  Q --> R{Validar valor e ID}
  R -->|OK ± R$5,00| P[Confirmar dados com usuário]
  R -->|Divergência > R$5,00| S[Reportar divergência / solicitar reenvio]
  S --> K

  P --> T[Orquestrador: gravar no Retaguarda]
  T --> U([✅ Fechamento concluído])

  style A fill:#0078d4,color:#fff
  style U fill:#107c10,color:#fff
  style I fill:#d13438,color:#fff
  style S fill:#ca5010,color:#fff
```

### B.3 Matriz Ator × Capacidade

| Capacidade | Responsável de Garagem | Analista de Fechamento | Portal (Sistema) | Orquestrador (Sistema) |
|-----------|:---:|:---:|:---:|:---:|
| Consultar garagens pendentes | — | Leitura | CRUD | Executa |
| Enviar relatório financeiro | CRUD | — | Repasse | Recebe |
| Extrair dados OCR | — | — | — | Executa |
| Validar regras negócio | — | Revisão exceções | — | Executa |
| Enviar comprovante | CRUD | — | Repasse | Recebe |
| Confirmar dados extraídos | Confirmação | — | Interface | — |
| Gravar no Retaguarda | — | — | — | Executa |
| Configurar parâmetros | — | Leitura | — | Admin |

---

## Phase C — Information Systems Architecture

### C1 — Data Architecture

#### C1.1 Diagrama ER Conceitual

```mermaid
erDiagram
  GARAGEM {
    uuid id PK
    string codigo
    string razao_social
    string sistema_pdv
    boolean integrada
    string status_atual
  }

  FECHAMENTO {
    uuid id PK
    uuid garagem_id FK
    date data_fechamento
    string status
    string sistema_pdv
    timestamp criado_em
    timestamp atualizado_em
    uuid criado_por FK
  }

  RELATORIO_FINANCEIRO {
    uuid id PK
    uuid fechamento_id FK
    string tipo_documento
    string caminho_storage
    decimal valor_rotativo
    int qtd_rotativo
    decimal valor_deposito
    decimal valor_arrecadacao
    int qtd_arrecadacao
    float confianca_ocr
    string status_ocr
    timestamp processado_em
  }

  PRODUTO_LANCAMENTO {
    uuid id PK
    uuid relatorio_id FK
    string codigo_produto
    string descricao
    int quantidade
    decimal valor
  }

  COMPROVANTE_DEPOSITO {
    uuid id PK
    uuid fechamento_id FK
    string banco
    string modelo
    string id_deposito
    date data_comprovante
    decimal valor
    string favorecido
    string caminho_storage
    float confianca_ocr
    boolean id_duplicado
    string status_validacao
  }

  AUDITORIA {
    uuid id PK
    uuid fechamento_id FK
    string agente
    string acao
    jsonb payload
    string resultado
    timestamp executado_em
  }

  CONFIG_PARAMETROS {
    uuid id PK
    string chave
    string valor
    string descricao
    timestamp atualizado_em
  }

  USUARIO {
    uuid id PK
    string nome
    string email
    string role
  }

  GARAGEM ||--o{ FECHAMENTO : "possui"
  FECHAMENTO ||--o{ RELATORIO_FINANCEIRO : "contém"
  FECHAMENTO ||--o{ COMPROVANTE_DEPOSITO : "contém"
  FECHAMENTO ||--o{ AUDITORIA : "registra"
  RELATORIO_FINANCEIRO ||--o{ PRODUTO_LANCAMENTO : "detalha"
  USUARIO ||--o{ FECHAMENTO : "opera"
```

#### C1.2 Estratégia de Armazenamento

| Tipo de Dado | Tecnologia | Justificativa | NFR coberto |
|-------------|-----------|--------------|------------|
| Dados transacionais (fechamentos, relatórios, comprovantes) | Azure SQL Database | ACID, relacionamentos, Row-Level Security | NFR-08 |
| Documentos originais (PDFs, imagens) | Azure Blob Storage | Custo-benefício, retenção configurável, CDN | NFR-06 |
| Parâmetros de configuração | Azure SQL + Key Vault (secrets) | Configurável sem deploy; segredos criptografados | NFR-05/12 |
| Logs de auditoria e telemetria | Azure Log Analytics | Retenção 90 dias, consultas analíticas | NFR-07 |
| Tokens de autenticação | Entra ID / Key Vault | Gerenciado, expiração automática | NFR-04 |

#### C1.3 Política de Dados Pessoais (LGPD)

| Dado Pessoal | Onde Armazenado | Criptografado | Retenção | Exclusão |
|-------------|----------------|--------------|---------|---------|
| Razão social da garagem | SQL Database | Em repouso (AES-256) | Enquanto garagem ativa | Soft delete + anonimização |
| Dados bancários (favorecido, ID depósito) | SQL Database | Em repouso (AES-256) | 90 dias após fechamento | Purge automático |
| Documentos originais (PDF/imagem) | Blob Storage | Em repouso (AES-256) | ≤ 24h após processamento | Exclusão automática por lifecycle policy |
| Logs de auditoria | Log Analytics | Em repouso | 90 dias | Purge automático |

#### C1.4 Fluxo de Dados Principais

```mermaid
flowchart LR
  subgraph Entrada[Entrada]
    P[Portal]
    U[👤 Operador]
  end

  subgraph Fila[Processamento Assíncrono]
    SB[Service Bus]
    OCR[OCR Engine]
  end

  subgraph Core[Orquestrador Core]
    VAL[Validador de Regras]
    ADP[Adapter por Sistema]
    ENR[Enriquecedor]
  end

  subgraph Persistência[Persistência]
    SQL[(Azure SQL)]
    BLOB[(Blob Storage)]
    LOG[(Log Analytics)]
    RET[(Retaguarda API)]
  end

  U -->|PDF/Imagem| P
  P -->|REST| SB
  SB -->|Documento| OCR
  OCR -->|Campos + Confiança| VAL
  OCR -->|Arquivo original| BLOB
  VAL -->|Dados estruturados| ADP
  ADP -->|Regras por sistema| ENR
  ENR -->|Dados validados| SQL
  ENR -->|Gravar fechamento| RET
  ENR -->|Todas as ações| LOG
  SQL -->|Resposta| P
```

---

### C2 — Application Architecture

#### C2.1 Catálogo de Componentes

| Componente | Responsabilidade | Tecnologia | Expõe | Consome |
|-----------|-----------------|-----------|-------|---------|
| Portal Estapar | Interface do usuário, upload de documentos | Existente (fora de escopo) | — | API Orquestrador |
| API Gateway | Autenticação, rate limit, roteamento | Azure API Management | REST | Orquestrador Functions |
| Orquestrador (core) | Fluxo de processamento, regras de negócio | Azure Functions (C# / Node.js) | REST | OCR, Retaguarda, SQL, Service Bus |
| OCR Engine | Extração de texto e campos estruturados | Azure Document Intelligence | REST | Blob Storage |
| PDV Adapter (×11) | Regras de cálculo por sistema PDV | Módulo interno do Orquestrador | — | OCR output |
| Bank Parser (×9+) | Extração de ID/valor por banco/modelo | Módulo interno do Orquestrador | — | OCR output |
| Config Service | Parâmetros configuráveis (ticket, tolerância) | Azure SQL + Key Vault | REST interno | — |
| Retaguarda Client | Adapter para API legada do Retaguarda | Módulo do Orquestrador | — | Retaguarda REST |
| Audit Logger | Log estruturado de todas as ações | App Insights + Log Analytics | — | Todos os componentes |
| Document Store | Armazenamento temporário de documentos | Azure Blob Storage | REST | OCR, Orquestrador |

#### C2.2 Diagrama de Container (C4 Level 2)

```mermaid
architecture-beta
  group portal_zone(mdi:monitor)[Portal Zone]
  group api_zone(azure:api-management)[API Zone]
  group processing(azure:functions)[Processing Zone]
  group data_zone(azure:sql-database)[Data Zone]
  group infra_zone(azure:key-vault)[Infrastructure Zone]

  service portal(mdi:web)[Portal Estapar] in portal_zone
  service apim(azure:api-management)[API Management + Auth] in api_zone

  service orch(azure:functions)[Orquestrador Core] in processing
  service bus(azure:service-bus)[Service Bus] in processing
  service ocr(azure:cognitive-services)[Document Intelligence OCR] in processing
  service adapters(mdi:api)[PDV Adapters x11 + Bank Parsers x9] in processing

  service sql(azure:sql-database)[Azure SQL — Fechamentos + Auditoria] in data_zone
  service blob(azure:storage-accounts)[Blob Storage — Documentos] in data_zone
  service logs(mdi:magnify)[Log Analytics — Auditoria] in data_zone

  service kv(azure:key-vault)[Key Vault] in infra_zone
  service aad(azure:azure-active-directory)[Entra ID] in infra_zone
  service monitor(azure:monitor)[Monitor + App Insights] in infra_zone
  service ret(mdi:database)[Retaguarda API] in data_zone

  portal:R --> L:apim
  apim:R --> L:orch
  orch:B --> T:bus
  bus:R --> L:ocr
  ocr:R --> L:blob
  orch:R --> L:adapters
  adapters:R --> L:sql
  orch:R --> L:ret
  orch:T --> B:kv
  orch:L --> R:monitor
  orch:B --> T:logs
  apim:T --> B:aad
```

#### C2.3 Diagrama de Sequência — Fluxo Principal (Relatório Financeiro)

```mermaid
sequenceDiagram
  autonumber
  actor OP as 👤 Responsável Garagem
  participant PO as 🖥️ Portal
  participant APIM as 🔀 API Management
  participant ORCH as ⚙️ Orquestrador
  participant SB as 📨 Service Bus
  participant OCR as 🔍 Document Intelligence
  participant ADP as 🔧 PDV Adapter
  participant RET as 🗄️ Retaguarda
  participant SQL as 💾 Azure SQL

  OP->>PO: Upload relatório financeiro + código garagem + sistema PDV
  PO->>APIM: POST /fechamento/relatorio + Bearer token
  APIM->>APIM: Validar JWT (Entra ID)
  APIM->>ORCH: Requisição autenticada
  ORCH->>SB: Enfileirar documento para processamento
  ORCH-->>PO: 202 Accepted + job_id

  SB->>OCR: Processar documento
  OCR->>OCR: Extrair campos + calcular confiança por campo
  OCR-->>ORCH: Campos estruturados + scores de confiança

  ORCH->>ADP: Aplicar regras do sistema PDV (ex: PDV/Estapar)
  ADP->>ADP: Calcular rotativo, validar campos obrigatórios
  ADP-->>ORCH: Dados validados por regra

  alt Campos obrigatórios ausentes
    ORCH-->>PO: 422 + lista de campos com problema
  else Dados OK
    ORCH->>SQL: Salvar dados extraídos + log auditoria
    ORCH->>RET: Consultar dados adicionais da garagem
    RET-->>ORCH: Dados complementares
    ORCH-->>PO: 200 + dados para confirmação do usuário
    OP->>PO: Confirmar dados
    PO->>ORCH: POST /fechamento/confirmar
    ORCH->>RET: Gravar fechamento no Retaguarda
    ORCH->>SQL: Atualizar status → "Documento recebido"
    RET-->>ORCH: OK
    ORCH-->>PO: 200 Fechamento concluído
  end
```

---

## Phase D — Technology Architecture

### D.1 Stack Tecnológica

| Camada | Tecnologia | Justificativa |
|--------|-----------|--------------|
| Frontend/Portal | Existente (fora de escopo) | — |
| API Gateway | Azure API Management | Rate limit, JWT validation, CORS, logging |
| Orquestrador | Azure Functions (Consumption/Premium) | Serverless, escala automática, trigger por Service Bus |
| OCR Engine | Azure Document Intelligence | Modelos pré-treinados para documentos financeiros; Custom Model para layouts específicos |
| Fila | Azure Service Bus (Standard) | Ordenação garantida, DLQ, retry automático |
| Banco de dados | Azure SQL Database (General Purpose) | ACID, RLS, backup automático |
| Document Storage | Azure Blob Storage (Hot → Cool lifecycle) | Hot por 24h → Cool → Expiração automática |
| Auth | Azure Entra ID | JWT, OAuth2, auth dois fatores |
| Secrets | Azure Key Vault | Conexão Retaguarda, chaves de criptografia |
| Observabilidade | Azure Monitor + Application Insights | Traces, métricas, alertas, dashboards |
| Log de auditoria | Azure Log Analytics | Retenção 90 dias, queries KQL |
| CI/CD | Azure DevOps Pipelines | Build, test, deploy multi-ambiente |
| IaC | Bicep / Terraform | Infraestrutura como código |
| Container Registry | Azure Container Registry | Imagens Docker dos Function Apps |

### D.2 Diagrama de Tecnologia (Infraestrutura)

```mermaid
architecture-beta
  group vnet(azure:virtual-network)[Virtual Network — Estapar OCR]
  group pub(azure:front-door)[Public Zone]
  group app(azure:app-service)[Application Zone — Private]
  group data_z(azure:sql-database)[Data Zone — Isolated]
  group sec(azure:key-vault)[Security Zone]
  group devops_z(azure:devops)[DevOps Zone]

  service apim(azure:api-management)[API Management] in pub

  service func(azure:functions)[Function Apps — Orquestrador] in app
  service bus(azure:service-bus)[Service Bus Standard] in app
  service ocr(azure:cognitive-services)[Document Intelligence] in app

  service sql(azure:sql-database)[Azure SQL General Purpose] in data_z
  service blob(azure:storage-accounts)[Blob Storage + Lifecycle] in data_z
  service logs(azure:monitor)[Log Analytics Workspace] in data_z

  service kv(azure:key-vault)[Key Vault] in sec
  service aad(azure:azure-active-directory)[Entra ID] in sec

  service ado(azure:devops)[Azure DevOps Pipelines] in devops_z
  service acr(azure:container-registry)[Container Registry] in devops_z
  service ai(azure:application-insights)[Application Insights] in devops_z

  apim:R --> L:func
  func:B --> T:bus
  bus:R --> L:ocr
  ocr:R --> L:blob
  func:R --> L:sql
  func:T --> B:kv
  func:L --> R:ai
  func:B --> T:logs
  apim:T --> B:aad
  ado:R --> L:acr
  acr:R --> L:func
```

### D.3 Padrões de Rede e Segurança

```mermaid
flowchart TB
  subgraph Internet
    OP[👤 Operador de Garagem]
    AN[👤 Analista]
  end

  subgraph DMZ["🔓 DMZ (API Management)"]
    APIM[API Management\nJWT Validation\nRate Limit\nCORS]
  end

  subgraph PRIV["🔒 Private Zone (Functions)"]
    FUNC[Orquestrador\nFunctions]
    BUS[Service Bus]
    OCR[Document Intelligence]
  end

  subgraph DATA["🔐 Data Zone (Isolated)"]
    SQL[(Azure SQL)]
    BLOB[(Blob Storage)]
    LOGS[(Log Analytics)]
  end

  subgraph SEC["🛡️ Security Zone"]
    KV[Key Vault]
    AAD[Entra ID]
  end

  OP -->|HTTPS/TLS 1.2+| APIM
  AN -->|HTTPS/TLS 1.2+| APIM
  APIM -->|Private Endpoint| FUNC
  FUNC -->|Private Endpoint| BUS
  FUNC -->|Managed Identity| OCR
  FUNC -->|Private Endpoint| SQL
  FUNC -->|Managed Identity| BLOB
  FUNC -->|Managed Identity| KV
  FUNC -->|SDK| LOGS
  APIM -->|OAuth2| AAD

  style Internet fill:#fff3cd
  style DMZ fill:#d1ecf1
  style PRIV fill:#cce5ff
  style DATA fill:#f8d7da
  style SEC fill:#d4edda
```

### D.4 Estratégia CI/CD

```mermaid
flowchart LR
  subgraph Dev["💻 Developer"]
    CODE[Commit + PR\nno Azure DevOps]
  end

  subgraph CI["🔄 CI Pipeline"]
    LINT[Lint + TypeCheck]
    TEST[Unit Tests\nStrategy Adapters]
    SEC[Security Scan\nCredential Check]
    BUILD[Docker Build\nFunction App]
    PUSH[Push to ACR]
  end

  subgraph CD["🚀 CD Pipeline"]
    DEV[Deploy → Dev\nAutomatic]
    STG[Deploy → Staging\nManual approval]
    SMOKE[Integration Tests\nOCR + Retaguarda Mock]
    PROD[Deploy → Prod\nBlue/Green]
  end

  CODE --> LINT --> TEST --> SEC --> BUILD --> PUSH
  PUSH --> DEV --> STG --> SMOKE --> PROD
```

### D.5 Observabilidade

| Pilar | Ferramenta | O que monitora |
|-------|-----------|---------------|
| Logs estruturados | Application Insights | Cada step do pipeline com job_id, garagem, sistema, resultado |
| Métricas de negócio | Azure Monitor Workbooks | Fechamentos/dia, taxa de erro OCR, tempo médio por sistema PDV |
| Auditoria | Log Analytics (KQL) | 100% das ações com timestamp e agente |
| Alertas | Azure Monitor Alerts | Falha em processamento, fila parada, latência > 30s |
| Dashboard | Azure Workbooks | Status por garagem, fechamentos pendentes, erros por banco |
| Distributed Tracing | App Insights — correlation_id | Rastreamento E2E: Portal → APIM → Function → OCR → Retaguarda |

---

## Phase E — Opportunities & Solutions

### E.1 Work Packages

| WP | Nome | Fase TOGAF | Componentes | Sprint |
|----|------|-----------|------------|--------|
| WP-01 | Foundation: Infrastructure & Security | D | APIM, Entra ID, Key Vault, Service Bus, Blob, SQL schema, CI/CD | S1 |
| WP-02 | OCR Core: Extração Base | C2 | Document Intelligence, PDV Adapters base, Bank Parsers base | S1–S2 |
| WP-03 | Orquestrador: Fluxo de Fechamento | C2+B | Validação campos, integração Retaguarda, gravação, auditoria | S2 |
| WP-04 | OCR: Adapters PDV (11 sistemas) | C2 | Um adapter por sistema PDV com testes unitários | S2–S3 |
| WP-05 | OCR: Parsers Bancários (9+ tipos) | C2 | Um parser por banco/modelo com testes | S3 |
| WP-06 | Orquestrador: Fluxo de Comprovante | C2+B | Validação depósito, ID duplicado, D+1, múltiplos | S3–S4 |
| WP-07 | Config & Admin | C2 | Config Service, tabela de ticket máximo, tolerância configurável | S4 |
| WP-08 | Observabilidade e Dashboard | D | App Insights, Workbooks, Alertas | S4 |

### E.2 Sequência de Entrega

```mermaid
gantt
  title Roadmap de Entrega — Estapar OCR/Orquestrador
  dateFormat  YYYY-MM-DD
  section Foundation
    WP-01 Foundation & Security          :a1, 2026-07-06, 2w
  section OCR Core
    WP-02 OCR Core + Adapters Base       :a2, after a1, 3w
  section Orquestrador
    WP-03 Fluxo Fechamento               :a3, after a2, 2w
    WP-04 11 PDV Adapters                :a4, after a2, 4w
  section Comprovante
    WP-05 Parsers Bancários              :a5, after a4, 3w
    WP-06 Fluxo Comprovante              :a6, after a3, 3w
  section Config e Observ
    WP-07 Config & Admin                 :a7, after a6, 1w
    WP-08 Observabilidade                :a8, after a6, 2w
  section Marcos
    Sprint 0 concluído                   :milestone, m1, after a1, 0d
    MVP — Fechamento PDV/Estapar         :milestone, m2, after a3, 0d
    Go-live — Todos PDVs                 :milestone, m3, after a5, 0d
    Entrega Final                        :milestone, m4, after a8, 0d
```

### E.3 Build vs Buy vs Integrate

| Componente | Decisão | Produto/Serviço | Justificativa |
|-----------|---------|----------------|--------------|
| OCR Engine | Integrate | Azure Document Intelligence | Modelos pré-treinados; Custom Model para layouts específicos; sem custo de treinamento |
| API Gateway | Integrate | Azure API Management | Rate limit, CORS, auth delegada ao Entra ID |
| Autenticação | Integrate | Azure Entra ID | MFA nativo, OAuth2, SSO corporativo |
| Orquestrador (core) | Build | Azure Functions | Regras de negócio proprietárias — diferenciação |
| PDV Adapters (×11) | Build | Módulos internos | Regras específicas por sistema PDV |
| Bank Parsers (×9+) | Build | Módulos internos | Lógica de ID específica por banco/modelo |
| Fila | Integrate | Azure Service Bus | DLQ, retry, ordenação — sem custom |
| Observabilidade | Integrate | Azure Monitor + App Insights | Nativo na stack Azure |

---

## Phase E — Team Plan

> Estimativa de equipe para construção do sistema OCR/Orquestrador da Estapar.  
> Equipe dimensionada para complexidade STANDARD com 8 work packages.

### E.4 Composição da Equipe

<div align="center">

| <img src="../../../../assets/icons/m365/Presenter.svg" width="36"><br>**Product Owner** | <img src="../../../../assets/icons/m365/Hat Graduation.svg" width="36"><br>**Arquiteto** | <img src="../../../../assets/icons/m365/Person Wrench.svg" width="36"><br>**Tech Lead** | <img src="../../../../assets/icons/m365/People Team.svg" width="36"><br>**Backend Dev** | <img src="../../../../assets/icons/m365/People Team.svg" width="36"><br>**Backend Dev 2** |
|:---:|:---:|:---:|:---:|:---:|
| 1 pessoa | 1 pessoa | 1 pessoa | 1 pessoa | 1 pessoa |
| Full-time | Part-time (50%) | Full-time | Full-time | Full-time |

| <img src="../../../../assets/icons/m365/People Settings.svg" width="36"><br>**DevOps/Infra** | <img src="../../../../assets/icons/m365/Person Settings.svg" width="36"><br>**QA Engineer** | <img src="../../../../assets/icons/m365/Data Trending.svg" width="36"><br>**AI/ML Engineer** | <img src="../../../../assets/icons/m365/People Community.svg" width="36"><br>**Scrum Master** |
|:---:|:---:|:---:|:---:|
| 1 pessoa | 1 pessoa | 1 pessoa | 1 pessoa (compartilhado) |
| Part-time (50%) | Full-time | Full-time | Part-time (30%) |

**Total: 9 pessoas (7,3 FTEs)**

</div>

---

### E.5 Ramp-up da Equipe

```mermaid
gantt
  title Ramp-up da Equipe — Estapar OCR
  dateFormat  YYYY-MM-DD
  axisFormat  %d/%m

  section Análise e Design
    Product Owner              :active, po,   2026-07-01, 2026-11-30
    Arquiteto de Solução       :        arq,  2026-07-01, 6w
    Scrum Master               :        sm,   2026-07-06, 2026-11-30

  section Sprint 0 — Setup
    Tech Lead                  :active, tl,   after arq, 2026-11-30
    DevOps / Infra             :        ops,  2026-07-06, 8w

  section Sprint 1 — OCR Core
    Backend Dev 1              :active, be1,  after tl,  2026-11-30
    AI/ML Engineer             :        ai,   after tl,  2026-11-30
    QA Engineer                :        qa,   after tl,  2026-11-30

  section Sprint 2 — Adapters
    Backend Dev 2              :        be2,  after be1, 2026-11-30

  section Marcos
    Kick-off                   :milestone, m1, 2026-07-01, 0d
    Sprint 0 concluído         :milestone, m2, 2026-07-20, 0d
    MVP — PDV/Estapar funcional:milestone, m3, 2026-09-07, 0d
    Go-live — Todos os PDVs    :milestone, m4, 2026-11-02, 0d
```

---

### E.6 Matriz de Papéis e Responsabilidades (RACI)

> R=Responsável | A=Aprovador | C=Consultado | I=Informado

<div>

| Atividade | <img src="../../../../assets/icons/m365/Presenter.svg" width="24"><br>PO | <img src="../../../../assets/icons/m365/Hat Graduation.svg" width="24"><br>Arquiteto | <img src="../../../../assets/icons/m365/Person Wrench.svg" width="24"><br>Tech Lead | <img src="../../../../assets/icons/m365/People Team.svg" width="24"><br>Backend | <img src="../../../../assets/icons/m365/Data Trending.svg" width="24"><br>AI/ML | <img src="../../../../assets/icons/m365/People Settings.svg" width="24"><br>DevOps | <img src="../../../../assets/icons/m365/Person Settings.svg" width="24"><br>QA |
|-----------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Discovery / PRD | A | C | C | I | I | I | I |
| Arquitetura e ADRs | C | R | C | C | C | C | I |
| PDV Adapters (×11) | I | C | A | R | C | I | C |
| OCR Pipeline + AI | I | C | A | C | R | I | C |
| Parsers Bancários (×9) | I | C | A | R | C | I | C |
| Integração Retaguarda | I | C | A | R | I | I | C |
| Infra Azure + CI/CD | I | C | A | I | I | R | I |
| Testes e QA | I | I | A | C | C | I | R |
| Deploy e release | A | I | C | I | I | R | C |
| Sprint planning | A | I | R | C | C | I | C |

</div>

---

### E.7 Estimativa de Custo de Equipe

> Base para o Business Case. Valores de mercado Brasil 2026 (Pleno/Sênior).

| Papel | Senioridade | Dedicação | Custo/Mês (R$) | Início | Duração | Total (R$) |
|-------|------------|----------|---------------|--------|---------|-----------|
| Product Owner | Sênior | 100% | R$ 18.000 | Semana 1 | 5 meses | R$ 90.000 |
| Arquiteto de Solução | Sênior | 50% | R$ 12.000 | Semana 1 | 6 semanas | R$ 18.000 |
| Tech Lead | Sênior | 100% | R$ 22.000 | Sprint 0 | 4,5 meses | R$ 99.000 |
| Backend Dev 1 | Pleno | 100% | R$ 13.000 | Sprint 1 | 4 meses | R$ 52.000 |
| Backend Dev 2 | Pleno | 100% | R$ 13.000 | Sprint 2 | 3,5 meses | R$ 45.500 |
| AI/ML Engineer | Sênior | 100% | R$ 18.000 | Sprint 1 | 4 meses | R$ 72.000 |
| DevOps / Infra | Pleno | 50% | R$ 7.500 | Sprint 0 | 4 meses | R$ 30.000 |
| QA Engineer | Pleno | 100% | R$ 10.000 | Sprint 1 | 4 meses | R$ 40.000 |
| Scrum Master | Sênior | 30% | R$ 4.500 | Sprint 0 | 5 meses | R$ 22.500 |
| **Total mensal (pico Sprint 2)** | | | **R$ 118.000** | | | **R$ 469.000** |

> **Encargos CLT:** multiplicar por 1,7x → pico R$ 200.600/mês | total R$ 797.300  
> **PJ/Freelancer:** sem multiplicador, adicionar 12% de margem de imprevistos  
> **Infraestrutura Azure estimada:** R$ 8.000–15.000/mês (Functions + APIM + SQL + Storage + Document Intelligence)

---

### E.8 Plano de Onboarding

```mermaid
flowchart TD
  A([👤 Novo Membro]) --> B[Acesso: Azure DevOps + Repositório]
  B --> C[Acesso: Ambientes dev + staging Azure]
  C --> D[Leitura: discovery-report + prd + architecture]
  D --> E{Papel?}

  E -->|Backend Dev / Tech Lead| F[Setup local: Docker + Azure Functions Core Tools]
  E -->|AI/ML Engineer| G[Setup: Document Intelligence + Python SDK + samples]
  E -->|DevOps| H[Acesso: Azure Portal + Pipelines + ACR]
  E -->|QA| I[Acesso: staging + collection Postman + test suite]

  F --> J[Pair programming Tech Lead — 2 dias\nAdapter PDV/Estapar como exemplo]
  G --> K[Walkthrough OCR pipeline com AI/ML Lead — 2 dias]
  H --> L[Session DevOps: Bicep + pipelines — 1 dia]
  I --> M[Review de critérios de aceite com PO — 1 dia]

  J & K & L & M --> N([✅ Pronto para Sprint])

  style A fill:#0078d4,color:#fff
  style N fill:#107c10,color:#fff
```

---

### E.9 Canais e Ferramentas de Comunicação

<div align="center">

| <img src="../../../../assets/icons/m365/Chat.svg" width="32"><br>**Daily** | <img src="../../../../assets/icons/m365/Calendar Month.svg" width="32"><br>**Sprint** | <img src="../../../../assets/icons/m365/Video.svg" width="32"><br>**Demo** | <img src="../../../../assets/icons/m365/Clipboard Task List.svg" width="32"><br>**Tracking** | <img src="../../../../assets/icons/m365/Code.svg" width="32"><br>**Code** |
|:---:|:---:|:---:|:---:|:---:|
| MS Teams | Azure Boards | Teams / Zoom | Azure Boards (Sprints) | Azure DevOps Repos |
| 15min / dia | 2 semanas | Fim de sprint | Diário | Contínuo |

</div>

---

## ADRs — Architecture Decision Records

### ADR-001: Monolito Modular para o Orquestrador
**Status:** Accepted | **Data:** 2026-05-31 | **Fase TOGAF:** Phase A  
**NFRs:** NFR-09, NFR-02, NFR-03

#### Contexto
O orquestrador precisa suportar 11 sistemas PDV e 9+ tipos de comprovante bancário, cada um com regras próprias. A equipe tem 9 pessoas e o prazo é ~5 meses.

#### Decisão
Monolito Modular com Azure Functions — um único Function App com módulos internos independentes (adapters/strategies por sistema).

#### Alternativas Consideradas
| Alternativa | Por que rejeitada |
|-------------|------------------|
| Microservices (1 serviço por PDV) | Overhead operacional de 11+ serviços para equipe de 9 pessoas; latência de comunicação; complexidade de orquestração desproporcional |
| Serverless puro sem orquestração | Sem estado transacional entre steps; difícil rastrear auditoria E2E do pipeline |

#### Consequências
- ✅ Time-to-market menor; deploy único; debugging mais simples
- ✅ Adapter pattern mantém extensibilidade (NFR-09: novo PDV ≤ 5 dias)
- ⚠️ Deploy completo ao adicionar novo adapter — mitigado por testes isolados por módulo
- ⚠️ Potencial bottleneck se volume crescer muito — mitigado por fila Service Bus e escala horizontal do Functions

#### Revisão
Reavaliar para microservices se equipe crescer para > 20 devs ou volume ultrapassar 10.000 fechamentos/dia.

---

### ADR-002: Azure Document Intelligence como OCR Engine
**Status:** Accepted | **Data:** 2026-05-31 | **Fase TOGAF:** Phase C2  
**NFRs:** NFR-01, NFR-02, NFR-09

#### Contexto
O sistema precisa extrair dados de 11 layouts de relatório PDV e 9+ layouts de comprovante bancário, com confiança ≥ 90% por campo e ≤ 30s de processamento.

#### Decisão
Azure Document Intelligence (Form Recognizer) — modelos pré-treinados para documentos financeiros + Custom Models treinados para os layouts específicos da Estapar.

#### Alternativas Consideradas
| Alternativa | Por que rejeitada |
|-------------|------------------|
| Tesseract (open source) | Precisão inferior em documentos complexos; sem nível de confiança por campo; custo de manutenção alto |
| Google Document AI | Vendor lock-in fora do ecossistema Azure já adotado; custo de egress de dados |

#### Consequências
- ✅ Modelos pré-treinados aceleram o MVP; Custom Model para layouts únicos
- ✅ Confiança por campo nativa — viabiliza NFR-01
- ⚠️ Custo por página processada — mitigado por armazenamento temporário ≤ 24h
- ⚠️ Requer treinamento de Custom Model para cada novo layout — planejado como WP-02

#### Revisão
Reavaliar se precisão dos Custom Models cair abaixo de 88% após 3 meses de operação.

---

### ADR-003: Azure Entra ID para Autenticação
**Status:** Accepted | **Data:** 2026-05-31 | **Fase TOGAF:** Phase D  
**NFRs:** NFR-04, NFR-05

#### Contexto
O sistema exige autenticação em dois fatores entre Portal e Orquestrador (RN03), tokens com expiração ≤ 1h e integração com identidade corporativa da Estapar.

#### Decisão
Azure Entra ID com OAuth2/JWT — token Bearer validado no API Management antes de qualquer requisição ao Orquestrador.

#### Alternativas Consideradas
| Alternativa | Por que rejeitada |
|-------------|------------------|
| Auth JWT própria | Risco de segurança alto; sem MFA nativo; manutenção de chaves |
| Auth0 | Custo adicional fora do ecossistema Azure; ausência de SSO corporativo nativo |

#### Consequências
- ✅ MFA nativo, Conditional Access, compliance Microsoft
- ✅ Managed Identity para comunicação entre Function e outros serviços Azure (sem credenciais em código)
- ⚠️ Curva de aprendizado do SDK — mitigado pelo AI/ML + Tech Lead já familiarizados com Azure

#### Revisão
Permanente — critério de segurança não negocia.

---

## Riscos Técnicos

| # | Risco | Probabilidade | Impacto | Severidade | Mitigação | Contingência |
|---|-------|--------------|---------|-----------|----------|-------------|
| RT-01 | Layouts de banco/PDV não mapeados (novos modelos) | Alta | Alto | CRÍTICO | Treinar OCR com exemplos reais de cada sistema antes do sprint | Entrada manual como fallback por sistema |
| RT-02 | API do Retaguarda sem endpoints necessários | Média | Alto | CRÍTICO | Mapear endpoints existentes no Sprint 0; criar mock para testes | API intermediária/adapter para Retaguarda legada |
| RT-03 | Itens pendentes P1-P4 não resolvidos antes do Sprint 1 | Alta | Médio | ALTO | Escalar com aprovadores; usar valores placeholder configuráveis | Implementar com config externa; resolver em S2 |
| RT-04 | Confiança OCR < 90% em layouts de fita (baixa resolução) | Média | Alto | ALTO | Definir resolução mínima de upload; testar com amostras reais | Fluxo de revisão manual para documentos abaixo do limiar |
| RT-05 | Concatenação de ID bancário (Santander M2, Bradesco) com edge cases | Baixa | Médio | MÉDIO | Suite de testes unitários com exemplos reais por banco | Log do ID original para revisão manual |

---

## Cobertura de NFRs

| NFR | Descrição | Solução Arquitetural | Fase TOGAF | ADR |
|-----|-----------|---------------------|-----------|-----|
| NFR-01 | Precisão OCR ≥ 90% | Document Intelligence Custom Models + score por campo | C2 | ADR-002 |
| NFR-02 | ≤ 30s relatório; ≤ 15s comprovante | Processamento assíncrono via Service Bus | C2 | ADR-001 |
| NFR-03 | Disponibilidade ≥ 99,5% | Azure Functions Premium + Service Bus DLQ + retry | D | — |
| NFR-04 | Auth dois fatores | Entra ID + APIM JWT validation | D | ADR-003 |
| NFR-05 | TLS 1.2+ | Azure API Management + Private Endpoints | D | ADR-003 |
| NFR-06 | AES-256 + retenção ≤ 24h | Blob Storage encryption + Lifecycle Policy | C1+D | — |
| NFR-07 | Auditoria 100% | App Insights structured logging + Log Analytics | D | — |
| NFR-08 | LGPD | RLS no SQL, criptografia repouso, soft delete, lifecycle | C1 | ADR-002 |
| NFR-09 | Novo layout ≤ 5 dias | Adapter/Strategy pattern por PDV/banco | C2 | ADR-001 |
| NFR-10 | Reprocessamento automático | Service Bus DLQ + retry 3x + dead-letter queue | D | — |
| NFR-11 | Alertas < 5 min | Azure Monitor Alerts + App Insights | D | — |
| NFR-12 | Parâmetros configuráveis | Config Service + Key Vault; sem deploy para tolerância/ticket | C2 | — |
| NFR-13 | ≥ 500 fechamentos simultâneos | Functions Consumption Plan auto-scale + Service Bus partitioning | D | ADR-001 |

---

## Histórico de Versões

| Versão | Data | Autor | Mudança |
|--------|------|-------|---------|
| 1.0 | 2026-05-31 | @architect (Aria) | Versão inicial — TOGAF ADM Phases A–E |
