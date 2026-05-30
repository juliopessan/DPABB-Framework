# Skill: Discovery

**Agente:** @discovery (Scout)  
**Trigger:** `*start` ou `/discovery`  
**Output:** `docs/output/{projeto}/discovery-report.md`

---

## Objetivo

Conduzir uma sessão de elicitação estruturada para transformar uma ideia ou problema vago em um relatório de discovery rico em contexto, pronto para o @prd processar.

---

## Execução

### Etapa 1 — Contextualização (2-3 perguntas)

Faça as perguntas abaixo de forma conversacional, uma a uma:

1. "Qual é o nome do projeto/produto e em qual empresa ou contexto ele será desenvolvido?"
2. "Descreva o problema principal que estamos resolvendo em 2-3 frases. Qual é a dor real?"
3. "Por que resolver isso agora? Qual foi o gatilho?"

Registre as respostas antes de continuar.

### Etapa 2 — Stakeholders (2-3 perguntas)

4. "Quem são os principais stakeholders? Liste: quem paga, quem usa, quem aprova."
5. "Qual é o objetivo principal de cada stakeholder com este projeto?"
6. "Existe alguém que pode bloquear ou resistir ao projeto? Por quê?"

### Etapa 3 — Problema em Profundidade (3-4 perguntas)

7. "Como o problema é resolvido hoje? (processo manual, outra ferramenta, não resolvido?)"
8. "Qual é o impacto financeiro ou operacional do problema não resolvido?"
9. "O que seria uma solução ideal na visão dos stakeholders?"
10. "Há tentativas anteriores de resolver esse problema? O que não funcionou?"

### Etapa 4 — Restrições (2-3 perguntas)

11. "Há tecnologias obrigatórias ou proibidas no contexto da empresa?"
12. "Há integrações com sistemas existentes que são obrigatórias?"
13. "Há restrições regulatórias? (LGPD, PCI DSS, SOC2, setor regulado?)"
14. "Qual é o prazo indicativo e o orçamento disponível?"

### Etapa 5 — Métricas (1-2 perguntas)

15. "Como o sucesso será medido em 6 meses? Quais são os KPIs?"
16. "Qual é o mínimo aceitável para considerar o projeto um sucesso (MVP)?"

---

## Geração do Output

Após coletar todas as respostas, preencher o template:
`templates/discovery/discovery-report.md`

Salvar em: `docs/output/{projeto}/discovery-report.md`

---

## Validação

Antes de finalizar, executar o checklist do agente (`agents/discovery.md § Checklist de Qualidade`).

Se algum item falhar, fazer as perguntas de follow-up necessárias.

---

## Handoff para @prd

Ao finalizar, comunicar:

```
Discovery concluído. 

Resumo: {2-3 linhas do problema e contexto}
Requerimentos de alto nível identificados: {N}
Riscos críticos: {lista breve}

Pronto para @prd *generate
```
