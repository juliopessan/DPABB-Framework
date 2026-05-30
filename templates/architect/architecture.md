# Architecture — {PROJETO}

**Versão:** 1.0  
**Data:** {DATA}  
**Autor:** @architect (Aria)  
**Metodologia:** TOGAF 10 — ADM Phases A–E  
**PRD:** [prd.md](../prd/prd.md)  
**Status:** Draft | In Review | Approved

---

## Phase A — Architecture Vision

### A.1 Declaração de Problema de Negócio

> Síntese do discovery: qual capacidade de negócio está ausente ou deficiente?

**Capacidade atual (As-Is):** ...  
**Capacidade alvo (To-Be):** ...  
**Gap:** ...

### A.2 Princípios Arquiteturais

| # | Princípio | Implicação |
|---|-----------|-----------|
| PA-01 | Security by Design | Autenticação e RBAC implementados desde o MVP |
| PA-02 | Observability First | Logs, métricas e traces em todos os serviços |
| PA-03 | API-First | Contratos definidos antes da implementação |
| PA-04 | {Princípio específico do projeto} | {implicação} |

### A.3 Stakeholder Architecture Concerns

| Stakeholder | Preocupação | Como a arquitetura endereça |
|-------------|------------|---------------------------|
| {Sponsor} | Custo de operação | Serverless / PaaS reduz ops overhead |
| {Dev team} | Velocidade de entrega | Monolito modular reduz coordenação |
| {Security} | Compliance LGPD | Criptografia em repouso + RLS |

### A.4 Diagrama de Contexto (C4 Level 1)

```mermaid
architecture-beta
  group internet(mdi:web)[Internet]
  group cloud(azure:azure)[Azure Cloud]
  group external(mdi:api)[Sistemas Externos]

  service users(mdi:account-group)[Usuários] in internet
  service ext_sys(mdi:api)[{Sistema Externo}] in external

  service apim(azure:api-management)[API Gateway] in cloud
  service app(azure:app-service)[{PROJETO} App] in cloud
  service db(azure:sql-database)[Database] in cloud
  service aad(azure:azure-active-directory)[Entra ID] in cloud
  service monitor(azure:monitor)[Monitor] in cloud

  users:R --> L:apim
  ext_sys:R --> L:apim
  apim:R --> L:app
  app:R --> L:db
  app:T --> B:aad
  app:L --> R:monitor
```

### A.5 Classificação de Complexidade

| Dimensão | Score (1-5) | Justificativa |
|----------|------------|--------------|
| Escopo | | |
| Integração | | |
| Infraestrutura | | |
| Conhecimento | | |
| Risco | | |
| **Total** | **/25** | |

**Classe:** SIMPLE / STANDARD / COMPLEX  
**Estilo recomendado:** {Monolito Modular / BFF+SPA / Microservices / Event-Driven}

---

## Phase B — Business Architecture

### B.1 Mapa de Capacidades de Negócio

```mermaid
mindmap
  root({PROJETO})
    Gestão de {Domínio A}
      {Capacidade 1}
      {Capacidade 2}
    Gestão de {Domínio B}
      {Capacidade 3}
      {Capacidade 4}
    Administração
      Usuários e Permissões
      Configurações
      Relatórios
```

### B.2 Fluxo de Processo — To-Be

```mermaid
flowchart TD
  A([🚀 Início]) --> B[{Passo 1}]
  B --> C{Decisão?}
  C -->|Sim| D[{Caminho A}]
  C -->|Não| E[{Caminho B}]
  D --> F([✅ Fim])
  E --> F

  style A fill:#0078d4,color:#fff
  style F fill:#107c10,color:#fff
```

### B.3 Matriz Ator × Capacidade

| Capacidade | {Persona 1} | {Persona 2} | {Admin} |
|-----------|------------|------------|--------|
| {Capacidade 1} | CRUD | Leitura | CRUD |
| {Capacidade 2} | — | CRUD | CRUD |

---

## Phase C — Information Systems Architecture

### C1 — Data Architecture

#### C1.1 Diagrama ER Conceitual

```mermaid
erDiagram
  USUARIO {
    uuid id PK
    string nome
    string email
    string role
    timestamp criado_em
    timestamp atualizado_em
  }

  ORGANIZACAO {
    uuid id PK
    string nome
    string plano
    boolean ativo
  }

  ENTIDADE_A {
    uuid id PK
    uuid organizacao_id FK
    string titulo
    text descricao
    string status
    uuid criado_por FK
    timestamp criado_em
  }

  ENTIDADE_B {
    uuid id PK
    uuid entidade_a_id FK
    string tipo
    jsonb metadados
  }

  USUARIO ||--o{ ORGANIZACAO : "pertence a"
  ORGANIZACAO ||--o{ ENTIDADE_A : "possui"
  USUARIO ||--o{ ENTIDADE_A : "criou"
  ENTIDADE_A ||--o{ ENTIDADE_B : "contém"
```

#### C1.2 Estratégia de Armazenamento

| Tipo de Dado | Tecnologia | Justificativa | NFR coberto |
|-------------|-----------|--------------|------------|
| Dados transacionais | {ex: Azure SQL / PostgreSQL} | ACID, relacionamentos | NFR-XXX |
| Sessões e cache | {ex: Azure Cache for Redis} | Baixa latência | NFR-001 |
| Arquivos e mídia | {ex: Azure Blob Storage} | Custo-benefício, CDN integrado | NFR-XXX |
| Logs e telemetria | {ex: Azure Log Analytics} | Retenção configurável, análise | NFR-XXX |
| Dados de busca | {ex: Azure AI Search} | Full-text, facets | NFR-XXX |

#### C1.3 Fluxo de Dados Principais

```mermaid
flowchart LR
  subgraph Sources[Fontes de Dados]
    U[👤 Usuário]
    EXT[🌐 API Externa]
  end

  subgraph Processing[Processamento]
    API[API Layer]
    QUEUE[Message Queue]
    WORKER[Background Worker]
  end

  subgraph Storage[Armazenamento]
    DB[(SQL Database)]
    CACHE[(Redis Cache)]
    BLOB[(Blob Storage)]
  end

  U -->|HTTPS| API
  EXT -->|Webhook| API
  API -->|Escreve| DB
  API -->|Enfileira| QUEUE
  API -->|Cache| CACHE
  QUEUE -->|Consome| WORKER
  WORKER -->|Processa| DB
  WORKER -->|Armazena| BLOB
```

#### C1.4 Política de Dados Pessoais (LGPD)

| Dado Pessoal | Onde Armazenado | Criptografado | Retenção | Exclusão |
|-------------|----------------|--------------|---------|---------|
| Nome, email | SQL Database | Em repouso (AES-256) | Enquanto conta ativa | Soft delete + anonimização |
| Logs de acesso | Log Analytics | Em repouso | 90 dias | Purge automático |

---

### C2 — Application Architecture

#### C2.1 Catálogo de Componentes

| Componente | Responsabilidade | Tecnologia | Expõe | Consome |
|-----------|-----------------|-----------|-------|---------|
| Frontend | Interface do usuário | {ex: Next.js} | — | API REST |
| API Gateway | Roteamento, rate limit, auth | {ex: Azure APIM} | REST | Services |
| {Service A} | {domínio funcional} | {ex: Node.js} | REST | DB, Cache |
| {Service B} | Processamento async | {ex: Azure Functions} | — | Queue, Blob |
| Auth Service | Autenticação, sessões | {ex: Azure AD B2C} | OAuth2/OIDC | — |

#### C2.2 Diagrama de Container (C4 Level 2)

```mermaid
architecture-beta
  group fe(mdi:monitor)[Frontend]
  group be(azure:app-service)[Backend]
  group data(azure:sql-database)[Data Layer]
  group infra(azure:azure)[Infrastructure]

  service spa(mdi:web)[{PROJETO} Web App] in fe
  service mobile(mdi:cellphone)[Mobile App] in fe

  service apim(azure:api-management)[API Management] in be
  service svc_a(azure:app-service)[{Service A}] in be
  service svc_b(azure:functions)[{Service B} Functions] in be
  service bus(azure:service-bus)[Service Bus] in be

  service db(azure:sql-database)[Azure SQL Database] in data
  service cache(azure:cache-for-redis)[Redis Cache] in data
  service blob(azure:storage-accounts)[Blob Storage] in data
  service search(mdi:magnify)[AI Search] in data

  service aad(azure:azure-active-directory)[Entra ID / B2C] in infra
  service kv(azure:key-vault)[Key Vault] in infra
  service monitor(azure:monitor)[Monitor + App Insights] in infra
  service cr(azure:container-registry)[Container Registry] in infra

  spa:R --> L:apim
  mobile:R --> L:apim
  apim:R --> L:svc_a
  apim:B --> T:svc_b
  svc_a:R --> L:db
  svc_a:T --> B:cache
  svc_a:B --> T:bus
  svc_b:R --> L:bus
  svc_b:L --> R:blob
  svc_a:R --> L:search
  svc_a:T --> B:kv
  svc_a:L --> R:monitor
  apim:T --> B:aad
```

#### C2.3 Diagrama de Sequência — Fluxo Principal

```mermaid
sequenceDiagram
  autonumber
  actor U as 👤 {Persona Principal}
  participant SPA as 🖥️ Web App
  participant APIM as 🔀 API Gateway
  participant SVC as ⚙️ {Service A}
  participant DB as 🗄️ Database
  participant CACHE as ⚡ Redis Cache
  participant AAD as 🔐 Entra ID

  U->>SPA: {Ação do usuário}
  SPA->>APIM: POST /{recurso} + Bearer token
  APIM->>AAD: Validar JWT
  AAD-->>APIM: ✅ Token válido (claims)
  APIM->>SVC: {Requisição processada}
  
  SVC->>CACHE: GET {chave}
  alt Cache hit
    CACHE-->>SVC: Dados em cache
  else Cache miss
    SVC->>DB: SELECT/INSERT/UPDATE
    DB-->>SVC: Resultado
    SVC->>CACHE: SET {chave} TTL=5min
  end
  
  SVC-->>APIM: 200 OK + Response body
  APIM-->>SPA: JSON Response
  SPA-->>U: ✅ UI atualizada
```

---

## Phase D — Technology Architecture

### D.1 Diagrama de Tecnologia (C4 Level 3 — Infrastructure)

```mermaid
architecture-beta
  group vnet(azure:virtual-network)[Virtual Network]
  group pub(azure:application-gateway)[Public Zone]
  group priv(azure:app-service)[Private Zone]
  group data(azure:sql-database)[Data Zone]
  group sec(azure:key-vault)[Security Zone]
  group devops(azure:devops)[DevOps]

  service fd(azure:front-door)[Azure Front Door + WAF] in pub
  service apim(azure:api-management)[API Management] in pub

  service app(azure:app-service)[App Service Plan] in priv
  service func(azure:functions)[Function Apps] in priv
  service bus(azure:service-bus)[Service Bus Premium] in priv

  service sqldb(azure:sql-database)[Azure SQL Business Critical] in data
  service redis(azure:cache-for-redis)[Redis Cache Standard C2] in data
  service adls(azure:storage-accounts)[ADLS Gen2] in data

  service kv(azure:key-vault)[Key Vault Premium] in sec
  service aad(azure:azure-active-directory)[Entra ID P2] in sec

  service ado(azure:devops)[Azure DevOps Pipelines] in devops
  service acr(azure:container-registry)[Container Registry] in devops
  service monitor(azure:monitor)[Azure Monitor + App Insights] in devops

  fd:R --> L:apim
  apim:R --> L:app
  apim:B --> T:func
  app:R --> L:sqldb
  app:T --> B:redis
  func:R --> L:bus
  func:B --> T:adls
  app:T --> B:kv
  apim:L --> R:aad
  ado:R --> L:acr
  acr:R --> L:app
  app:L --> R:monitor
```

### D.2 Padrões de Rede e Segurança

```mermaid
flowchart TB
  subgraph Internet
    USR[👤 Usuário]
  end

  subgraph Edge["🌐 Edge (Azure Front Door)"]
    WAF[WAF + DDoS Protection]
    CDN[CDN Global]
  end

  subgraph DMZ["🔓 DMZ Zone (Public Subnet)"]
    APIM[API Management]
    AGW[Application Gateway]
  end

  subgraph APP["🔒 Application Zone (Private Subnet)"]
    APP_SVC[App Service]
    FUNC[Functions]
  end

  subgraph DATA["🔐 Data Zone (Isolated Subnet)"]
    SQL[(SQL Database)]
    REDIS[(Redis)]
  end

  USR --> WAF
  WAF --> CDN
  CDN --> APIM
  APIM --> AGW
  AGW --> APP_SVC
  AGW --> FUNC
  APP_SVC --> SQL
  APP_SVC --> REDIS

  style Internet fill:#fff3cd
  style Edge fill:#d1ecf1
  style DMZ fill:#d4edda
  style APP fill:#cce5ff
  style DATA fill:#f8d7da
```

### D.3 Estratégia CI/CD

```mermaid
flowchart LR
  subgraph Dev["💻 Developer"]
    CODE[Commit + PR]
  end

  subgraph CI["🔄 CI Pipeline (Azure DevOps)"]
    LINT[Lint + TypeCheck]
    TEST[Unit + Integration Tests]
    SEC[Security Scan]
    BUILD[Docker Build]
    PUSH[Push to ACR]
  end

  subgraph CD["🚀 CD Pipeline"]
    DEV_ENV[Deploy → Dev]
    STAGING[Deploy → Staging]
    SMOKE[Smoke Tests]
    PROD[Deploy → Prod (Blue/Green)]
  end

  CODE --> LINT --> TEST --> SEC --> BUILD --> PUSH
  PUSH --> DEV_ENV --> STAGING --> SMOKE --> PROD
```

### D.4 Observabilidade

| Pilar | Ferramenta | O que monitora |
|-------|-----------|---------------|
| Logs | Azure Log Analytics | Erros, auditoria, acesso |
| Métricas | Azure Monitor | CPU, memória, latência, throughput |
| Traces | Application Insights | Rastreamento de requisições E2E |
| Alertas | Azure Monitor Alerts | SLA violations, error spikes |
| Dashboard | Azure Workbooks | KPIs de produto e infra |

---

## Phase E — Opportunities & Solutions

### E.1 Work Packages

| WP | Nome | Fase TOGAF | Componentes | Sprint |
|----|------|-----------|------------|--------|
| WP-01 | Foundation & Security | D | APIM, AAD, KV, CI/CD | S1 |
| WP-02 | Core Domain | C2 | {Service A}, Database, Cache | S1-S2 |
| WP-03 | Async Processing | C2+D | {Service B}, Service Bus | S2 |
| WP-04 | Frontend | C2 | SPA, Mobile | S2-S3 |
| WP-05 | Observability | D | Monitor, App Insights | S3 |

### E.2 Sequência de Entrega

```mermaid
gantt
  title Roadmap de Entrega — {PROJETO}
  dateFormat  YYYY-MM-DD
  section Foundation
    WP-01: Foundation & Security  :a1, {DATA_INICIO}, 2w
  section Core
    WP-02: Core Domain            :a2, after a1, 4w
    WP-03: Async Processing       :a3, after a2, 2w
  section Frontend
    WP-04: Frontend               :a4, after a1, 6w
  section Observability
    WP-05: Observabilidade        :a5, after a2, 1w
  section Launch
    MVP Review                    :milestone, after a4, 0d
```

### E.3 Build vs Buy vs Integrate

| Componente | Decisão | Ferramenta/Produto | Justificativa |
|-----------|---------|-------------------|--------------|
| Auth | Integrate | Azure AD / Auth0 | Security, compliance, SSO nativo |
| API Gateway | Integrate | Azure APIM | Rate limit, CORS, auth delegado |
| Observabilidade | Integrate | Azure Monitor | Nativo na stack Azure |
| {Core Feature} | Build | — | Diferenciação competitiva |

---

## ADRs — Architecture Decision Records

### ADR-001: {Título — Estilo Arquitetural}
**Status:** Accepted | **Data:** {DATA} | **Fase TOGAF:** Phase A  
**NFRs:** NFR-001, NFR-003, NFR-004

#### Contexto
{Descrever por que a decisão foi necessária}

#### Decisão
{O que foi decidido}

#### Alternativas Consideradas
| Alternativa | Por que rejeitada |
|-------------|------------------|
| {Microservices} | Overhead operacional desproporcional para equipe de {N} devs |
| {Serverless puro} | Cold start inaceitável para {NFR de latência} |

#### Consequências
- ✅ Menor curva de aprendizado, entrega mais rápida no MVP
- ✅ Um único deploy, menor complexidade de infraestrutura inicial
- ⚠️ Potencial bottleneck em escala > {N} usuários simultâneos (mitigação: cache agressivo)

#### Revisão
Revisar se equipe crescer para > 10 devs ou se carga ultrapassar {N} rps.

---

### ADR-002: {Título — Banco de Dados}
**Status:** Accepted | **Data:** {DATA} | **Fase TOGAF:** Phase C1  
**NFRs:** NFR-002, NFR-005

#### Contexto
{Descrever pressão de dados ou compliance}

#### Decisão
{Tecnologia escolhida + tier/plano}

#### Alternativas Consideradas
| Alternativa | Por que rejeitada |
|-------------|------------------|
| {NoSQL / MongoDB} | Relacionamentos complexos entre entidades exigem ACID |
| {Auto-hosted PostgreSQL} | Custo ops, sem SLA gerenciado da Microsoft |

#### Consequências
- ✅ ACID garantido, Row-Level Security nativo para multi-tenancy
- ✅ Backup automático, alta disponibilidade gerenciada
- ⚠️ Custo maior que managed PostgreSQL (~30%), aceito pelo SLA

#### Revisão
Se custo mensal ultrapassar {threshold}, reavaliar PostgreSQL Flexible Server.

---

### ADR-003: {Título — Autenticação}
**Status:** Accepted | **Data:** {DATA} | **Fase TOGAF:** Phase D  
**NFRs:** NFR-002

#### Contexto
{Requisito de autenticação: MFA, SSO, B2C, etc.}

#### Decisão
{Solução de auth escolhida e protocolo}

#### Alternativas Consideradas
| Alternativa | Por que rejeitada |
|-------------|------------------|
| {Auth própria (JWT manual)} | Risco de segurança alto, sem suporte a MFA nativo |
| {Firebase Auth} | Vendor lock-in fora do ecossistema Azure, compliance incerta |

#### Consequências
- ✅ MFA nativo, Conditional Access, compliance Microsoft
- ✅ SSO corporativo em 1 linha de configuração
- ⚠️ Curva de aprendizado do SDK

#### Revisão
Permanente — critério de segurança não negocia.

---

## Riscos Técnicos

| # | Risco | Probabilidade | Impacto | Severidade | Mitigação | Plano de Contingência |
|---|-------|--------------|---------|-----------|----------|----------------------|
| RT-01 | | Alta/Média/Baixa | Alto/Médio/Baixo | CRÍTICO/ALTO/MÉDIO/BAIXO | | |

---

## Cobertura de NFRs

| NFR | Descrição | Solução Arquitetural | Fase TOGAF | ADR | Status |
|-----|-----------|---------------------|-----------|-----|--------|
| NFR-001 | Performance < 300ms p95 | Redis Cache + CDN + APIM caching | C2+D | ADR-001 | ✅ |
| NFR-002 | Segurança MFA + RBAC | Entra ID + APIM JWT validation | D | ADR-003 | ✅ |
| NFR-003 | Disponibilidade 99.5% | App Service + SQL Business Critical + Front Door | D | ADR-001 | ✅ |
| NFR-004 | Escala 1000 users | Horizontal scaling + Redis | C2+D | | ✅ |
| NFR-005 | LGPD | Criptografia repouso + RLS + Soft delete | C1+D | ADR-002 | ✅ |

---

## Histórico de Versões

| Versão | Data | Autor | Mudança |
|--------|------|-------|---------|
| 1.0 | {DATA} | @architect (Aria) | Versão inicial — TOGAF ADM Phases A-E |
