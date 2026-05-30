# Task: Artifact Manager

**Trigger:** Executado por cada agente ao INICIAR e FINALIZAR sua fase.  
**Ferramentas:** `Read`, `Write`, `Grep`

---

## LOAD — Ao Iniciar Fase

### Passo 1: Verificar state
```
Read: docs/output/{projeto}/pipeline-state.json
```
- Confirmar que a fase anterior está `"status": "completed"`
- Se não: avisar usuário e aguardar

### Passo 2: Carregar memory.md
```
Read: docs/output/{projeto}/memory.md
```
Extrair:
- Contexto acumulado (evitar re-perguntar)
- Handoff da fase anterior (seção relevante)
- Decisões tomadas (não reabrir)
- Perguntas já respondidas

### Passo 3: Aplicar lessons-learned relevantes
```
Read: framework/lessons-learned.md
Grep: [FASE_ATUAL] para filtrar lições aplicáveis
```
Listar lições a aplicar nesta execução.

### Passo 4: Carregar artefatos das fases anteriores
Para cada artefato listado em `phases.{fase_anterior}.artifact`:
```
Read: {caminho do artefato}
```
Construir contexto comprimido (não re-ler tudo literalmente, sintetizar).

### Passo 5: Atualizar state — fase iniciada
```
Write: docs/output/{projeto}/pipeline-state.json
  phases.{fase_atual}.status = "in_progress"
  phases.{fase_atual}.started_at = {ISO_DATE}
  updated_at = {ISO_DATE}
```

---

## SAVE — Ao Finalizar Fase

### Passo 1: Salvar artefato da fase
```
Write: docs/output/{projeto}/{artefato}.md
```
Usando o template: `templates/{fase}/{artefato}.md`

### Passo 2: Atualizar memory.md
```
Write: docs/output/{projeto}/memory.md (append/update)
```
Adicionar:
- Novas decisões tomadas
- Handoff para próxima fase (seção específica)
- Novas perguntas respondidas
- Contexto comprimido da fase (3-5 bullet points)
- Atualizar `Artefatos Produzidos` — marcar como ✅
- Incrementar token savings (estimativa: N tokens economizados)

### Passo 3: Atualizar state — fase completada
```
Write: docs/output/{projeto}/pipeline-state.json
  phases.{fase_atual}.status = "completed"
  phases.{fase_atual}.completed_at = {ISO_DATE}
  phases.{fase_atual}.artifact_exists = true
  phases.{fase_atual}.quality_gate = "passed"
  phases.{fase_atual}.handoff_written = true
  current_phase = {proxima_fase}
  updated_at = {ISO_DATE}
```

### Passo 4 (apenas @business-case): Atualizar lessons-learned.md
```
Write: framework/lessons-learned.md (append)
```
- Adicionar padrões de sucesso descobertos
- Adicionar armadilhas encontradas
- Registrar na tabela "Melhorias Aplicadas"
- Atualizar métricas do framework

---

## Regras de Ouro

1. **Nunca re-perguntar** algo que está em `memory.md § Perguntas Respondidas`
2. **Nunca reabrir** decisão que está em `memory.md § Decisões Tomadas`
3. **Sempre escrever handoff** antes de marcar fase como completed
4. **O state.json é a fonte de verdade** — se fase anterior não está completed, não iniciar
5. **Comprimir ao carregar** — ler artefatos anteriores para sintetizar, não para repetir

---

## Estimativa de Economia de Tokens

| Situação | Tokens sem memory | Tokens com memory | Economia |
|----------|------------------|------------------|---------|
| @prd lê discovery completo | ~3.000 | ~800 (handoff) | ~73% |
| @architect re-elicita stack | ~1.500 | 0 (decisão salva) | 100% |
| @backlog lê PRD completo | ~4.000 | ~1.000 (handoff) | ~75% |
| Pipeline completo cold | ~18.000 | ~6.000 | ~67% |
