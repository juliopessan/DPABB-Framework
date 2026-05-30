# Architecture — {PROJETO}

**Versão:** 1.0  
**Data:** {DATA}  
**Autor:** @architect (Aria)  
**PRD:** [prd.md](../prd/prd.md)  
**Status:** Draft | In Review | Approved

---

## 1. Visão Geral

> Resumo da abordagem arquitetural e principais decisões técnicas.

---

## 2. Classificação de Complexidade

| Dimensão | Score (1-5) | Justificativa |
|----------|------------|--------------|
| Escopo (arquivos afetados) | | |
| Integração (APIs externas) | | |
| Infraestrutura | | |
| Conhecimento da equipe | | |
| Risco e criticidade | | |
| **Total** | **/25** | |

**Classe:** SIMPLE (≤8) / STANDARD (9-15) / COMPLEX (≥16)

---

## 3. Estilo Arquitetural

**Escolha:** {Monolito Modular / Microservices / Event-Driven / Serverless / BFF+SPA / CQRS+ES}

**Justificativa:**
- 
- 

**Alternativas descartadas:**
- {Alternativa}: {motivo da rejeição}

---

## 4. Stack Tecnológica

| Camada | Tecnologia | Justificativa | NFR coberto |
|--------|-----------|--------------|------------|
| Frontend | | | |
| Backend / API | | | |
| Banco de Dados | | | |
| Autenticação | | | |
| Infraestrutura | | | |
| CI/CD | | | |
| Monitoramento | | | |
| Cache | | | |

---

## 5. Componentes e Responsabilidades

### 5.1 Diagrama C4 — Context
```
[Usuário] → [Sistema {PROJETO}] → [Sistemas Externos]
```

### 5.2 Diagrama C4 — Container
```
[Frontend SPA] → [API Gateway] → [Service A]
                              ↘ [Service B] → [Database]
```

### 5.3 Componentes Principais

| Componente | Responsabilidade | Tecnologia | Expõe |
|-----------|-----------------|-----------|-------|
| | | | API REST / Events |

---

## 6. Modelo de Dados (Alto Nível)

### Entidades Principais

```
{Entidade A}
  ├── campo_1: tipo
  └── campo_2: tipo

{Entidade B}
  ├── campo_1: tipo
  └── entidade_a_id → Entidade A
```

### Estratégia de Armazenamento

| Dado | Tecnologia | Justificativa |
|------|-----------|--------------|
| Dados transacionais | | |
| Sessões e cache | | |
| Arquivos e mídia | | |
| Logs e telemetria | | |

---

## 7. Integrações Externas

| Sistema | Tipo | Protocolo | SLA | Fallback |
|---------|------|-----------|-----|---------|
| | | REST/GraphQL/MQ | | |

---

## 8. Segurança e Autenticação

| Aspecto | Solução | NFR coberto |
|---------|---------|------------|
| Autenticação | | NFR-002 |
| Autorização | | |
| Criptografia em trânsito | TLS 1.3 | |
| Criptografia em repouso | | |
| LGPD / Dados pessoais | | NFR-005 |
| Gestão de segredos | | |

---

## 9. Escalabilidade e Performance

| Estratégia | Componente | Meta NFR |
|-----------|-----------|---------|
| Horizontal scaling | | NFR-004 |
| Cache | | NFR-001 |
| CDN | | |
| Connection pooling | | |

---

## 10. ADRs — Architecture Decision Records

### ADR-001: {Título}
**Status:** Accepted  
**Contexto:** ...  
**Decisão:** ...  
**Alternativas:** ...  
**Consequências:** ✅ ... / ⚠️ ...  
**Rastreabilidade:** NFR-001

### ADR-002: {Título}
**Status:** Accepted  
**Contexto:** ...  
**Decisão:** ...  
**Alternativas:** ...  
**Consequências:** ✅ ... / ⚠️ ...  
**Rastreabilidade:** NFR-002

---

## 11. Riscos Técnicos

| Risco | Probabilidade | Impacto | Mitigação |
|-------|--------------|---------|----------|
| | Alta/Média/Baixa | Alto/Médio/Baixo | |

---

## 12. Cobertura de NFRs

| NFR | Solução Arquitetural | Status |
|-----|---------------------|--------|
| NFR-001 Performance | Cache + CDN + Query optimization | ✅ |
| NFR-002 Segurança | Auth + RBAC + TLS | ✅ |
| NFR-003 Disponibilidade | Multi-AZ + Health checks | ✅ |

---

## Histórico de Versões

| Versão | Data | Autor | Mudança |
|--------|------|-------|---------|
| 1.0 | {DATA} | @architect | Versão inicial |
