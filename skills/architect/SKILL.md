# Skill: Architecture Design (TOGAF + Mermaid)

**Agente:** @architect (Aria)  
**Trigger:** `*design`  
**Método:** TOGAF 10 — ADM Phases A–E  
**Input:** `docs/output/{projeto}/prd.md` + `docs/output/{projeto}/memory.md`  
**Output:** `docs/output/{projeto}/architecture.md`

---

## Pré-condições

- [ ] `pipeline-state.json` → `phases.prd.status = "completed"`
- [ ] `prd.md` existe com NFRs definidos
- [ ] `memory.md` existe com handoff PRD → Architect

---

## Etapa 0 — LOAD (via artifact-manager)

```
1. Read: docs/output/{projeto}/pipeline-state.json
   → Verificar prd.status = "completed"

2. Read: docs/output/{projeto}/memory.md
   → Extrair: handoff PRD→Architect, decisões fixas, restrições técnicas

3. Read: framework/lessons-learned.md
   → Grep: [ARCHITECT] → aplicar lições relevantes

4. Read: docs/output/{projeto}/prd.md
   → Extrair: NFRs, restrições técnicas, personas, FRs críticos

5. Write: pipeline-state.json
   → phases.architect.status = "in_progress"
   → phases.architect.started_at = {ISO_DATE}
```

---

## Etapa 1 — Phase A: Architecture Vision

1. Ler contexto do memory.md (handoff PRD→Architect)
2. Identificar capacidades de negócio ausentes (gap As-Is → To-Be)
3. Definir princípios arquiteturais (sempre incluir Security by Design e Observability First)
4. Mapear preocupações de cada stakeholder
5. **Gerar diagrama de Contexto** (Mermaid `architecture-beta` com ícones Azure)
6. Classificar complexidade (5 dimensões × score 1-5 = total /25)

---

## Etapa 2 — Phase B: Business Architecture

1. Mapear capacidades de negócio necessárias (mindmap Mermaid)
2. Desenhar fluxo To-Be do processo principal (flowchart Mermaid)
3. Preencher Matriz Ator × Capacidade com base nas personas do PRD

---

## Etapa 3 — Phase C1: Data Architecture

1. Identificar entidades principais a partir dos FRs
2. **Gerar diagrama ER** (Mermaid `erDiagram`)
3. Definir estratégia de armazenamento por tipo de dado
4. Se dados pessoais: documentar política LGPD (retenção, exclusão, criptografia)
5. Criar diagrama de fluxo de dados (flowchart Mermaid)

---

## Etapa 4 — Phase C2: Application Architecture

1. Montar catálogo de componentes com responsabilidades
2. **Gerar diagrama de Container** (Mermaid `architecture-beta` com ícones Azure)
   - Usar ícones Azure específicos por serviço (não genéricos)
   - Agrupar por zona: Frontend / Backend / Data / Infrastructure
   - Mostrar todas as dependências entre componentes
3. **Gerar diagrama de Sequência** para o fluxo principal (Mermaid `sequenceDiagram`)
   - Incluir: Actor → SPA → APIM → Service → DB → Cache → Auth
   - Mostrar cache hit/miss
   - Mostrar erros e fallbacks

---

## Etapa 5 — Phase D: Technology Architecture

1. Selecionar stack completa (frontend, backend, banco, auth, infra, CI/CD, monitor)
2. **Gerar diagrama de Tecnologia** (Mermaid `architecture-beta` — nível infraestrutura)
   - Usar ícones Azure para todos os serviços cloud
   - Mostrar zonas de rede (Public, Private, Data, Security)
3. Documentar estratégia de segurança (WAF, APIM, Entra ID, KV)
4. **Gerar diagrama de CI/CD** (Mermaid `flowchart`)
5. Definir estratégia de observabilidade

---

## Etapa 6 — ADRs

Criar mínimo 3 ADRs obrigatórios:
- **ADR-001:** Estilo arquitetural (Monolito / Microservices / etc.)
- **ADR-002:** Banco de dados (tecnologia + tier)
- **ADR-003:** Autenticação (protocolo + produto)

Cada ADR DEVE ter:
- Mínimo 2 alternativas rejeitadas com motivo técnico
- Consequências positivas E negativas (trade-offs honestos)
- Condição de revisão futura

---

## Etapa 7 — Phase E: Opportunities & Solutions

1. Definir work packages agrupando componentes por dependência
2. **Gerar diagrama Gantt** com sequência de entrega (Mermaid `gantt`)
3. Avaliar build vs buy vs integrate por componente
4. Documentar riscos técnicos (probabilidade × impacto)

---

## Etapa 8 — Cobertura de NFRs

Montar tabela mapeando cada NFR para:
- Solução arquitetural específica
- Fase TOGAF onde é endereçado
- ADR relacionado (se houver)

**Regra:** 100% dos NFRs Must/Should devem ter cobertura documentada.

---

## Etapa 9 — SAVE (via artifact-manager)

```
1. Write: docs/output/{projeto}/architecture.md
   → Usando template: templates/architect/architecture.md

2. Write: docs/output/{projeto}/memory.md (update)
   → Adicionar seção "Architect → Backlog":
     - Estilo arquitetural: {escolha}
     - Stack: {resumo}
     - Componentes: {lista}
     - Complexidade: SIMPLE / STANDARD / COMPLEX
     - Stories técnicas obrigatórias: WP-01 = TECH-001, TECH-002...
   → Atualizar Artefatos Produzidos: architecture.md = ✅
   → Atualizar Decisões Tomadas: stack, auth, banco

3. Write: pipeline-state.json
   → phases.architect.status = "completed"
   → phases.architect.completed_at = {ISO_DATE}
   → phases.architect.artifact_exists = true
   → phases.architect.handoff_written = true
   → phases.architect.diagrams = { context: true, container: true, component: true, technology: true }
   → current_phase = "backlog"
```

---

## Handoff para @backlog

Escrever no memory.md:

```
### Architect → Backlog

Estilo: {Monolito Modular / Microservices / etc.}
Stack: {resumo em 1 linha — ex: Next.js + Node.js + Azure SQL + Redis + Entra ID}
Complexidade: SIMPLE / STANDARD / COMPLEX → Estimativa base: {N} pts/componente

Componentes que viram stories técnicas (TECH-XXX):
- TECH-001: Setup repositório + Azure DevOps CI/CD + environments (dev/staging/prod)
- TECH-002: Configurar Entra ID + APIM JWT validation + RBAC seed
- TECH-003: Provisionar Azure SQL + Redis Cache + Key Vault + App Insights
- TECH-004: {componente específico do projeto}

WPs para mapear em Epics:
- WP-01 → Epic EPIC-TECH (foundation)
- WP-02 → Epic EPIC-{domínio A}
- WP-03 → Epic EPIC-{domínio B}

Riscos técnicos a virar stories de spike:
- RT-01: {risco} → Story de investigação {N} pts
```

---

## Validação antes de SAVE

Executar checklist: `agents/architect.md § Checklist de Qualidade`  
Mínimo exigido: 8/9 itens antes de marcar fase como completed.
