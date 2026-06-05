# Lessons Learned — Requirements Intel Framework

> Arquivo auto-evolutivo. Atualizado pelo @business-case ao final de cada pipeline completo.  
> **Propósito:** O framework aprende com cada projeto. Reduz tokens e melhora qualidade nas próximas execuções.  
> **Regra:** Adicionar sempre. Nunca remover. Marcar como `[OBSOLETO]` quando superado por nova lição.

---

### 2026-06-03 — Template Conformance: enforce 100% template adherence
- **Context:** User requested that agents strictly follow repository templates for discovery/PRD/architecture artifacts for all new projects; previously agents produced shorter, non-template-conformant discovery outputs.
- **Lesson:** Always apply the canonical templates under `/templates/` when generating artifacts. If template fields are missing, the agent must prompt for required values rather than omitting sections.
- **Action:** Created `/memories/template_conformance.md` to persist this preference; agents should read this memory and the corresponding template before saving any project artifact.
- **Tag:** [process|quality]

---

## Como Usar

Todo agente deve ler as lições relevantes à sua fase **antes** de executar.  
O @business-case é responsável por adicionar novas lições ao finalizar cada pipeline.

**Filtro rápido por fase:**
- `[DISCOVERY]` — Lições para @discovery
- `[PRD]` — Lições para @prd  
- `[ARCHITECT]` — Lições para @architect
- `[BACKLOG]` — Lições para @backlog
- `[BUSINESS-CASE]` — Lições para @business-case
- `[PIPELINE]` — Lições gerais do pipeline

---

## Padrões de Sucesso

> O que funciona bem e deve ser repetido.

### [DISCOVERY] Perguntas de impacto financeiro primeiro
**Contexto:** Quando stakeholders não sabem quantificar o problema, começar pelo impacto financeiro desbloqueou o restante da elicitação.  
**Aplicar quando:** Stakeholder responde vagamente ao "qual é o problema".  
**Lição:** Perguntar "quanto custa esse problema por mês?" antes de "qual é o problema?"

### [ARCHITECT] Monolito Modular para equipes < 5 devs
**Contexto:** Microservices para equipes pequenas gerou over-engineering em 3 projetos consecutivos.  
**Aplicar quando:** Equipe < 5 devs E complexidade SIMPLE ou STANDARD.  
**Lição:** Default para Monolito Modular. Documentar no ADR a opção rejeitada.

### [BACKLOG] Stories técnicas no Sprint 1 sem exceção
**Contexto:** Quando CI/CD e Auth foram deixados para Sprint 2, o Sprint 1 gerou dívida técnica que atrasou todos os outros.  
**Aplicar quando:** Sempre.  
**Lição:** TECH-001 (CI/CD) e TECH-002 (Auth) são sempre Must Have no Sprint 1.

### [PIPELINE] Handoff escrito economiza >40% dos tokens na fase seguinte
**Contexto:** Agentes que escrevem um handoff estruturado no memory.md permitem que o próximo agente inicie sem re-ler artefatos inteiros.  
**Aplicar quando:** Sempre, ao finalizar cada fase.  
**Lição:** O handoff deve ter: decisões-chave + números críticos + o que NÃO explorar.

### [DISCOVERY] Definir escopo de privacidade e nivel de agregacao no inicio evita retrabalho
**Contexto:** Em projetos com dados de comunicacao corporativa, quando o nivel de analise (agregado vs individual) nao e decidido no discovery, PRD e arquitetura precisam ser revisados depois.  
**Aplicar quando:** Casos com dados sensiveis (chat, email, colaboracao interna).  
**Lição:** Registrar no discovery a fronteira de privacidade e o nivel de agregacao permitido para todas as camadas de consumo.

### [BUSINESS-CASE] Modelar cenario base com gates obrigatorios melhora decisao executiva
**Contexto:** Recomendacoes binarias (GO/NO-GO) sem condicoes de qualidade e governanca nao refletem risco real em projetos de IA aplicada.  
**Aplicar quando:** PoCs de IA com dados sensiveis e risco de escala de custo.  
**Lição:** Emitir "GO com condicoes" com gates explicitos (compliance, qualidade de modelo, custo de escala) aumenta confianca e governanca da decisao.

---

## Armadilhas Comuns

> O que evitar. Cada item tem uma causa e prevenção.

### [DISCOVERY] Elicitar solução antes do problema
**O que acontece:** Stakeholder propõe tecnologia específica (ex: "quero um app em React Native") antes de definir o problema.  
**Causa:** Stakeholders tendem a solucionarizar prematuramente.  
**Prevenção:** Responder "entendido, exploraremos isso depois — primeiro me conta mais sobre o problema que você quer resolver".

### [PRD] NFRs sem métrica mensurável
**O que acontece:** NFRs como "o sistema deve ser rápido" passam pelo gate mas não são verificáveis.  
**Causa:** Falta de disciplina na escrita de NFRs.  
**Prevenção:** Todo NFR tem obrigatoriamente: métrica (ms, %, usuários) + método de medição.

### [ARCHITECT] ADRs sem alternativas documentadas
**O que acontece:** ADR documenta apenas a decisão, não as alternativas — impossível revisar no futuro.  
**Causa:** Pressa na documentação.  
**Prevenção:** Todo ADR tem obrigatoriamente: mínimo 2 alternativas rejeitadas com motivo técnico.

### [BACKLOG] Estimativas sem referência de velocidade
**O que acontece:** Story points estimados sem saber a velocidade do time geram roadmap irreal.  

### [DISCOVERY] Communication-data requires 2x governance time vs. other PoCs
**O que acontece:** Quando dados são de comunicação interna (Teams, email), legal+security+privacy levam 2-3x mais tempo validar scopes, retention e uso.
**Causa:** Reputational risk elevado; threat model complexo (re-identification, inference attacks, misuse).
**Prevenção:** Listar "compliance gates" no Discovery e assumir Week 1-2 para aprovações. Ter plano B reduzido de escopo se approvals atrasarem.

---

## Padrões do T-Mobile Sentiment Analysis PoC

> Lições específicas do projeto que podem ser generalizadas.

### [DISCOVERY + PRD] Privacy-by-design começa na definição de agregação
**Contexto:** No T-Mobile, decidir "agregado por org/time/topic, sem drill-to-individual" no Discovery economizou 40+ horas de re-arquitetura.
**Aplicar quando:** Qualquer projeto com dados sensíveis (chat, email, HR, medical, financeiro).
**Lição:** Registrar explicitamente no Discovery: (1) o que é PII neste contexto, (2) qual é o nível mínimo de agregação seguro, (3) quem aprova casos de edge.

### [PRD] Confidence labeling deve ser feature de MVP, não Phase 2
**Contexto:** Inicialmente T-Mobile considerou remover confidence scores para simplificar. Cancelado após avaliar: executivos com baixa confiança tomariam decisões erradas.
**Aplicar quando:** PoC com LLM ou modelos com incerteza inerente.
**Lição:** Confidence labeling, low-confidence flagging, e fallback suggestions são **non-negotiable** para credibilidade. Incluir no Sprint 1.

### [BUSINESS-CASE] "GO com condições" é mais responsável que GO/NO-GO binário
**Contexto:** T-Mobile ROI calcula +30%, mas risco de escala de custos é alto. Recomendação "GO with conditions: model F1 >= 0.78, privacy audit 0 findings, cost governance quotas" reflete risco real.
**Aplicar quando:** PoCs de IA com dados sensíveis, LLM consumption, ou modelos que podem degradar na escala.
**Lição:** Estruturar decisão como GO/AMBER/NO-GO com explicit gates. Comunica responsabilidade.

### [ARCHITECT] Purview governance designer deve estar no kick-off, não no UAT
**Contexto:** Governance lead juntou-se na week 10; lineage + audit foi retrofitted. Com presença early, teria economizado 20 horas.
**Aplicar quando:** Qualquer projeto com Purview, compliance, ou PII.
**Lição:** Governance/Compliance **não é fase-final**; é workstream paralela desde week 1.

### [BACKLOG] Sentiment ML requires both human validation SET e continuous monitoring harness
**Contexto:** Estimativa inicial de 8 pts para "model validation". Com dois repositórios (validation set + monitoring harness) cresceu para 16 pts (M-size).
**Aplicar quando:** Qualquer NLP/sentiment scope.
**Lição:** Separar: (1) curated validation set (labeled, Avanade-owned), (2) production monitoring harness (automated daily checks), (3) human review loop (governance gate). Cada um é story separada.
**Causa:** Ausência de dados históricos ou benchmark.  
**Prevenção:** Perguntar sempre: "qual é a velocidade média do time por sprint?" ou usar 20pts como default conservador para times novos.

### [BUSINESS-CASE] Benefícios sem premissas documentadas
**O que acontece:** ROI calculado com benefícios otimistas não questicionados gera expectativas irreais.  
**Causa:** Pressão por números positivos.  
**Prevenção:** Cada linha de benefício tem: fonte da premissa + cenário base + cenário pessimista (-30%).

---

## Melhorias Aplicadas ao Framework

> Registro de evoluções do próprio framework baseadas em lições.

| Versão | Data | Melhoria | Origem |
|--------|------|---------|--------|
| v1.1 | | | Projeto: |
| v1.0 | {DATA_CRIACAO} | Versão inicial | Bootstrap |

---

## Biblioteca de Padrões por Domínio

> Padrões de solução recorrentes por tipo de projeto.

### SaaS B2B
- Auth: sempre OAuth2 + JWT + RBAC por organização
- Multi-tenancy: row-level security no banco desde o início
- Billing: integrar Stripe no Sprint 2, não Sprint 1
- NFR típico: 99.5% uptime, < 500ms p95

### E-commerce
- Cache: Redis obrigatório para catálogo e sessão
- Pagamento: nunca armazenar dados de cartão (PCI DSS)
- NFR típico: suportar 10x tráfego em datas sazonais

### HealthTech / MedTech
- LGPD: consentimento granular obrigatório desde MVP
- Auditoria: log imutável de todas as ações sobre dados de saúde
- Auth: MFA obrigatório para profissionais de saúde

### Plataforma Interna (B2E)
- Auth: SSO corporativo (Azure AD / Google Workspace)
- Sem cadastro: usuários importados do diretório corporativo
- NFR típico: disponibilidade horário comercial, não 24/7

---

## Métricas do Framework

> Rastrear para medir evolução da qualidade.

| Projeto | Data | Fases completadas | Tokens economizados | Qualidade gate |
|---------|------|------------------|--------------------|----|
| | | | | |

---

## Próximas Melhorias Planejadas

> Ideias capturadas para versões futuras do framework.

- [ ] Template de ADR por tipo de decisão (banco, auth, infra)
- [ ] Gerador automático de diagramas Mermaid a partir do template
- [ ] Integração com Linear/Jira para criar stories automaticamente
- [ ] Versão em inglês para projetos internacionais
