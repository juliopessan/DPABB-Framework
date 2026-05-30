# Skill: Architecture Design

**Agente:** @architect (Aria)  
**Trigger:** `*design`  
**Input:** `docs/output/{projeto}/prd.md`  
**Output:** `docs/output/{projeto}/architecture.md`

---

## Objetivo

Propor uma arquitetura técnica fundamentada que cubra todos os NFRs do PRD, com decisões documentadas em ADRs e trade-offs explícitos.

---

## Pré-condições

- [ ] `prd.md` existe e está aprovado
- [ ] NFRs de performance, segurança e disponibilidade definidos
- [ ] Restrições técnicas documentadas

---

## Execução

### Etapa 1 — Análise de Complexidade
Preencher a matriz de complexidade (5 dimensões, score 1-5):
- Escopo, Integração, Infraestrutura, Conhecimento, Risco
- Classificar: SIMPLE / STANDARD / COMPLEX

### Etapa 2 — Escolha do Estilo Arquitetural
Avaliar estilos com base em:
- Classificação de complexidade
- Restrições técnicas do PRD
- NFRs (especialmente escala e disponibilidade)
- Tamanho da equipe (inferir do contexto ou perguntar)

Documentar alternativas descartadas e motivo.

### Etapa 3 — Stack Tecnológica
Para cada camada (frontend, backend, banco, auth, infra, CI/CD, monitoramento):
- Propor tecnologia
- Justificar com base em NFR ou restrição
- Verificar restrições técnicas do PRD antes de propor

### Etapa 4 — Componentes
- Listar componentes principais com responsabilidades claras
- Definir contratos/interfaces entre componentes
- Descrever diagramas C4 Context e Container em texto

### Etapa 5 — Modelo de Dados
- Identificar entidades principais e seus relacionamentos
- Não prescrever DDL completo (é tarefa do @data-engineer se necessário)
- Documentar estratégia de armazenamento por tipo de dado

### Etapa 6 — Segurança
Sempre cobrir:
- Autenticação (solução e protocolo)
- Autorização (RBAC, ABAC, ou simples)
- Criptografia em trânsito e repouso
- Gestão de segredos (vault, env vars, etc.)
- LGPD se dados pessoais envolvidos

### Etapa 7 — ADRs
Criar pelo menos 3 ADRs para:
- Escolha de stack principal
- Solução de autenticação
- Estratégia de banco de dados

Formato: `agents/architect.md § Formato de ADR`

### Etapa 8 — Riscos Técnicos
Para cada risco: probabilidade, impacto, mitigação proposta

### Etapa 9 — Cobertura de NFRs
Tabela mapeando cada NFR para a decisão arquitetural que o cobre.

### Etapa 10 — Geração
Preencher template: `templates/architect/architecture.md`  
Salvar em: `docs/output/{projeto}/architecture.md`

---

## Perguntas de Clarificação

Fazer ao usuário apenas se necessário:
- "O PRD restringe tecnologia mas não especifica versão — há preferência?"
- "A equipe já usa [tecnologia X] — devo mantê-la para reduzir curva de aprendizado?"
- "Não encontrei informação sobre o tamanho da equipe — quantos devs?"

---

## Handoff para @backlog

```
Arquitetura concluída.

Estilo: {Monolito Modular / Microservices / etc.}
Stack principal: {resumo em 1 linha}
Componentes definidos: {N}
ADRs documentados: {N}
NFRs cobertos: {N}/{N}

Complexidade estimada: SIMPLE / STANDARD / COMPLEX
Riscos técnicos críticos: {lista breve}

Pronto para @backlog *breakdown
```
