# Memory — {PROJETO}

> Arquivo gerado e atualizado automaticamente pelo pipeline.  
> **Propósito:** Evitar re-elicitação de contexto já coletado. Cada agente lê este arquivo antes de agir.  
> **Regra:** Nunca deletar entradas. Apenas adicionar ou marcar como `[SUPERADO]`.

---

## Meta

| Campo | Valor |
|-------|-------|
| Projeto | {PROJETO} |
| Início do pipeline | {DATA_INICIO} |
| Última atualização | {DATA_UPDATE} |
| Fase atual | discovery / prd / architect / backlog / business-case / done |
| Token savings estimado | {N} tokens economizados vs cold start |

---

## Contexto Acumulado

> Decisões, fatos e contexto que NUNCA precisam ser coletados novamente.

### Sobre o Projeto
- **Problema:** {resumo em 1-2 linhas do discovery}
- **Solução proposta:** {resumo da arquitetura}
- **Stakeholders chave:** {lista}
- **Prazo e orçamento:** {valores}

### Restrições Fixas (não negociáveis)
- **Tecnológicas:** {lista}
- **Regulatórias:** {LGPD, PCI, etc.}
- **Organizacionais:** {lista}

### Decisões Tomadas (não reabrir)
| Decisão | Fase | Razão | Alternativas rejeitadas |
|---------|------|-------|------------------------|
| | discovery | | |
| | architect | | |

---

## Artefatos Produzidos

| Artefato | Caminho | Status | Hash/Versão |
|----------|---------|--------|-------------|
| discovery-report.md | `docs/output/{projeto}/discovery-report.md` | ⏳ / ✅ / 🔄 | |
| prd.md | `docs/output/{projeto}/prd.md` | ⏳ | |
| architecture.md | `docs/output/{projeto}/architecture.md` | ⏳ | |
| backlog.md | `docs/output/{projeto}/backlog.md` | ⏳ | |
| business-case.md | `docs/output/{projeto}/business-case.md` | ⏳ | |

> Status: ⏳ Pendente | ✅ Aprovado | 🔄 Em revisão | ❌ Rejeitado

---

## Handoffs Entre Fases

> Cada agente escreve aqui o que o próximo precisa saber sem re-ler tudo.

### Discovery → PRD
```
{Scout escreve aqui ao finalizar}
```

### PRD → Architect
```
{Priya escreve aqui ao finalizar}
NFRs críticos para arquitetura:
- NFR-XXX: {descrição}
```

### Architect → Backlog
```
{Aria escreve aqui ao finalizar}
Componentes principais: {lista}
Complexidade: SIMPLE / STANDARD / COMPLEX
Stories técnicas obrigatórias: {lista}
```

### Backlog → Business Case
```
{Ben escreve aqui ao finalizar}
Total SPs: {N}
Sprint 1: {N} pts
Sprints estimados: {N}
```

---

## Perguntas Respondidas (não perguntar de novo)

| # | Pergunta | Resposta | Fase |
|---|---------|---------|------|
| 1 | | | discovery |

---

## Padrões Identificados no Projeto

> Padrões específicos deste projeto para reuso interno.

- 
- 

---

## Changelog do Memory

| Data | Fase | Mudança |
|------|------|---------|
| {DATA} | discovery | Arquivo criado |
