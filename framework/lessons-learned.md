# Lessons Learned — Requirements Intel Framework

> Arquivo auto-evolutivo. Atualizado pelo @business-case ao final de cada pipeline completo.  
> **Propósito:** O framework aprende com cada projeto. Reduz tokens e melhora qualidade nas próximas execuções.  
> **Regra:** Adicionar sempre. Nunca remover. Marcar como `[OBSOLETO]` quando superado por nova lição.

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

### [ARCHITECT] Governança de IA como princípio arquitetural, não feature isolada
**Contexto:** No projeto Intelligent Operations Document Platform (RFP-001), o requisito "nenhuma decisão material apenas por IA não revisada" foi tratado como princípio arquitetural (PA-01) e refletido em todo componente (gate de confiança, auditoria, ADR-002), não como uma story isolada.  
**Aplicar quando:** RFP/PRD contém requisito explícito de supervisão humana sobre resultado de IA.  
**Lição:** Elevar o requisito de governança de IA a princípio arquitetural (Phase A) garante que ele seja considerado em todas as fases TOGAF seguintes, em vez de arriscar ser esquecido em uma única story de backlog.

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
**Causa:** Ausência de dados históricos ou benchmark.  
**Prevenção:** Perguntar sempre: "qual é a velocidade média do time por sprint?" ou usar 20pts como default conservador para times novos.

### [BUSINESS-CASE] Benefícios sem premissas documentadas
**O que acontece:** ROI calculado com benefícios otimistas não questicionados gera expectativas irreais.  
**Causa:** Pressão por números positivos.  
**Prevenção:** Cada linha de benefício tem: fonte da premissa + cenário base + cenário pessimista (-30%).

### [BACKLOG] Integração com sistema alvo não confirmado bloqueia estimativa, não a story
**O que acontece:** No projeto Intelligent Operations Document Platform, o sistema financeiro e o CRM alvo não estavam definidos no RFP (questão aberta), mas a story de roteamento (STORY-04.01/04.02) ainda pôde ser estimada e sequenciada, marcando a dependência explicitamente em vez de bloquear todo o backlog.  
**Causa:** Tentação de não estimar nada até resolver toda ambiguidade externa.  
**Prevenção:** Estimar a story com a integração mais provável/genérica, registrar a dependência explícita no backlog e sinalizar o risco no Business Case (não travar o pipeline inteiro por uma decisão pendente de terceiros).

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
| intelligent-operations-document-platform | 2026-07-19 | 5/5 (discovery→business-case) | Handoffs escritos entre todas as fases; N/A (execução única em sessão contínua) | GO com ressalvas |

---

## Próximas Melhorias Planejadas

> Ideias capturadas para versões futuras do framework.

- [ ] Template de ADR por tipo de decisão (banco, auth, infra)
- [ ] Gerador automático de diagramas Mermaid a partir do template
- [ ] Integração com Linear/Jira para criar stories automaticamente
- [ ] Versão em inglês para projetos internacionais
