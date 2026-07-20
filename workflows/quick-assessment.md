# Workflow: Quick Assessment

**Trigger:** `*quick {nome-do-projeto}`  
**Duração:** 20-40 min  
**Quando usar:** Projetos SIMPLE, spike de viabilidade, ou quando o prazo é crítico

---

## Diferença do Pipeline Completo

| Aspecto | Pipeline Completo | Quick Assessment |
|---------|-----------------|-----------------|
| Duração | 60-120 min | 20-40 min |
| Business Case | Completo com ROI | Estimativa rápida |
| Elicitação | 16 perguntas | 5 perguntas |
| ADRs | ≥3 documentados | 1 principal |
| Profundidade | Alta | Média |
| Quando usar | Projetos novos | Spike / avaliação inicial |

---

## Fases Comprimidas

### 1. Discovery Rápido (5 perguntas)
1. Qual o problema e contexto?
2. Quem são os usuários e stakeholders?
3. Qual o impacto do problema?
4. Há restrições técnicas ou regulatórias críticas?
5. Qual o prazo e orçamento indicativo?

Output: `discovery-report-quick.md` (versão resumida)

---

### 2. PRD Simplificado
Foco apenas em:
- FRs Must Have
- NFRs críticos (performance, segurança)
- Personas principais (máx 2)

Output: `prd-quick.md`

---

### 3. Arquitetura Inicial
Foco apenas em:
- Estilo arquitetural
- Stack principal
- 1 ADR (decisão mais crítica)

Output: `architecture-quick.md`

---

### 4. Backlog MVP
Foco apenas em:
- Sprint 1 (Must Have)
- Estimativa de esforço total

Output: `backlog-quick.md`

---

### 5. Estimativa de Viabilidade (opcional)
Se solicitado, gerar estimativa rápida de custo e ROI sem business case completo.

---

## Escalação para Pipeline Completo

Recomendar pipeline completo se:
- Complexidade avaliada como COMPLEX
- Investimento > R$ 200k estimado
- Múltiplas integrações críticas
- Decisão de GO/NO-GO formal necessária
- Dados de ROI requeridos pela liderança
