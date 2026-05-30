# PRD — {PROJETO}

**Versão:** 1.0  
**Data:** {DATA}  
**Autor:** @prd (Priya)  
**Discovery:** [discovery-report.md](../discovery/discovery-report.md)  
**Status:** Draft | In Review | Approved

---

## 1. Visão Geral

> Uma visão concisa do produto: o que é, para quem é e qual problema resolve.

**Tagline:** "{PRODUTO} é [categoria] que permite [persona] [fazer algo] sem [dor atual]."

---

## 2. Problema e Oportunidade

> Síntese do discovery. Referência: `discovery-report.md § 4`.

---

## 3. Personas

### Persona 1: {NOME}
| Campo | Detalhe |
|-------|---------|
| Perfil | |
| Objetivo principal | |
| Dor atual | |
| Nível técnico | Básico / Intermediário / Avançado |
| Frequência de uso | |

### Persona 2: {NOME}
| Campo | Detalhe |
|-------|---------|
| Perfil | |
| Objetivo principal | |
| Dor atual | |

---

## 4. User Journeys

### Journey 1: {Nome do fluxo} (Persona: {X})

```
[Trigger] → [Passo 1] → [Passo 2] → [Passo 3] → [Resultado]
```

**Happy Path:**  
**Edge Cases:**  
**Erros e Fallbacks:**  

---

## 5. Requerimentos Funcionais

> Cada FR deve ser rastreável ao discovery. Prioridade: Must / Should / Could / Won't (MoSCoW).

| ID | Descrição | Critério de Aceitação | Prioridade | Origem |
|----|-----------|----------------------|------------|--------|
| FR-001 | | | Must | RQ-001 |
| FR-002 | | | Should | RQ-002 |

### Detalhamento dos FRs Críticos

#### FR-001: {Título}
**Descrição:** ...  
**Critérios de Aceitação:**
- Given [contexto] When [ação] Then [resultado]
- Given [contexto] When [ação] Then [resultado]

**Prioridade:** Must Have  
**Origem:** RQ-001

---

## 6. Requerimentos Não-Funcionais

| ID | Categoria | Requerimento | Métrica | Origem |
|----|-----------|-------------|---------|--------|
| NFR-001 | Performance | Tempo de resposta da API | < 300ms p95 | RQ-003 |
| NFR-002 | Segurança | Autenticação | MFA obrigatório para admin | RQ-004 |
| NFR-003 | Disponibilidade | Uptime | 99.5% mensal | RQ-005 |
| NFR-004 | Escalabilidade | Usuários simultâneos | 1000 sem degradação | |
| NFR-005 | LGPD | Dados pessoais | Consentimento, exclusão, portabilidade | |

---

## 7. Restrições

### 7.1 Técnicas
- 

### 7.2 Regulatórias
- 

### 7.3 Prazo e Orçamento
- 

---

## 8. Premissas

1. 
2. 

---

## 9. Fora de Escopo (Explícito)

> O que NÃO será feito nesta versão.

- 
- 

---

## 10. Critérios de Sucesso

| Métrica | Meta | Prazo de Medição |
|---------|------|-----------------|
| | | |

---

## 11. Rastreabilidade

| FR/NFR | Origem Discovery | Rastreado? |
|--------|-----------------|-----------|
| FR-001 | RQ-001 | ✅ |
| NFR-001 | RQ-003 | ✅ |

---

## Histórico de Versões

| Versão | Data | Autor | Mudança |
|--------|------|-------|---------|
| 1.0 | {DATA} | @prd | Versão inicial |
