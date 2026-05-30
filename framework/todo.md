# TODO List — Requirements Intel

> Arquivo auto-evolutivo. Atualizado por agentes e pelo usuário ao longo do projeto.  
> **Formato:** `[ ]` pendente | `[x]` concluído | `[~]` bloqueado | `[>]` delegado | `[-]` cancelado  
> **Regra:** Nunca deletar itens concluídos — eles são histórico. Adicionar data ao completar.

---

## Meta

| Campo | Valor |
|-------|-------|
| Projeto | {PROJETO} |
| Última atualização | {DATA} |
| Atualizado por | {AGENTE / USUARIO} |
| Progresso geral | 0 / {N} itens |

---

## 🟦 Fase 0 — Kickoff do Projeto

> Antes de iniciar o pipeline. Feito uma vez por projeto.

- [ ] Definir nome do projeto e criar pasta `docs/output/{projeto}/`
- [ ] Confirmar stakeholders disponíveis para entrevistas
- [ ] Coletar documentos de referência existentes (se houver)
- [ ] Confirmar acesso ao repositório para todos os envolvidos
- [ ] Definir ferramenta de tracking (Jira / Linear / Azure DevOps / GitHub)
- [ ] Agendar kick-off meeting com stakeholders

---

## 🟦 Fase 1 — Discovery

> Responsável: @discovery (Scout)

- [ ] Executar `@discovery *start` para elicitação guiada
- [ ] Validar discovery-report.md com stakeholder principal
- [ ] Confirmar KPIs com sponsor
- [ ] Identificar e entrevistar usuários finais (se não coberto na elicitação)
- [ ] Validar restrições técnicas com equipe de TI existente
- [ ] **Gate:** discovery-report.md aprovado (7/7 checklist)

---

## 🟦 Fase 2 — PRD

> Responsável: @prd (Priya)

- [ ] Executar `@prd *generate` após discovery aprovado
- [ ] Revisar personas com stakeholders
- [ ] Validar priorização MoSCoW com Product Owner
- [ ] Confirmar "Fora de Escopo" com sponsor
- [ ] **Gate:** prd.md aprovado (6/6 checklist + rastreabilidade 100%)

---

## 🟦 Fase 3 — Arquitetura

> Responsável: @architect (Aria)

- [ ] Executar `@architect *design` após PRD aprovado
- [ ] **Phase A:** Validar princípios arquiteturais com CTO/Tech Lead
- [ ] **Phase B:** Revisar fluxos de negócio com Product Owner
- [ ] **Phase C1:** Validar modelo de dados com DBA se existente
- [ ] **Phase C2:** Gerar diagrama de container com ícones Azure
- [ ] **Phase D:** Validar stack com equipe de infraestrutura
- [ ] **Phase E:** Validar plano de equipe com RH/Gestão
- [ ] Revisar ADRs com Tech Lead
- [ ] **Gate:** architecture.md aprovado (9/9 checklist)

---

## 🟦 Fase 4 — Backlog

> Responsável: @backlog (Ben)

- [ ] Executar `@backlog *breakdown` após arquitetura aprovada
- [ ] Validar estimativas com Tech Lead
- [ ] Confirmar composição do Sprint 1 com Product Owner
- [ ] Importar backlog para ferramenta de tracking ({ferramenta})
- [ ] Criar Epic e Stories no {ferramenta}
- [ ] **Gate:** backlog.md aprovado (7/7 checklist)

---

## 🟦 Fase 5 — Business Case

> Responsável: @business-case (Biz)

- [ ] Executar `@business-case *analyze` após backlog aprovado
- [ ] Validar premissas de custo com financeiro
- [ ] Validar benefícios com sponsor
- [ ] Apresentar business case para aprovação
- [ ] Documentar decisão GO/NO-GO no memory.md
- [ ] **Gate:** business-case.md aprovado + recomendação emitida

---

## 🟧 Sprint 0 — Setup (após GO)

> Responsável: DevOps + Tech Lead

- [ ] Criar repositório Git ({org}/{repo})
- [ ] Configurar Azure DevOps / GitHub Actions (CI/CD)
- [ ] Provisionar ambientes: dev / staging / prod
- [ ] Configurar Entra ID / Auth (ADR-003)
- [ ] Provisionar banco de dados + migrations iniciais
- [ ] Configurar Redis Cache
- [ ] Configurar Key Vault + segredos iniciais
- [ ] Configurar Application Insights + alertas
- [ ] Definir branching strategy (GitFlow / Trunk-based)
- [ ] Criar board no {ferramenta} com Sprint 1
- [ ] Realizar Sprint Planning do Sprint 1

---

## 🟧 Contratação e Onboarding de Equipe

> Ver Phase E do architecture.md para plano de ramp-up detalhado

- [ ] Confirmar disponibilidade: Product Owner
- [ ] Confirmar disponibilidade: Tech Lead / Arquiteto
- [ ] Confirmar disponibilidade: Backend Devs ({N})
- [ ] Confirmar disponibilidade: Frontend Devs ({N})
- [ ] Confirmar disponibilidade: DevOps/Infra
- [ ] Confirmar disponibilidade: QA Engineer
- [ ] Contratar/alocar: {papel pendente}
- [ ] Onboarding: acesso a repositório, ferramentas, ambientes
- [ ] Onboarding: apresentação do PRD e arquitetura para todo o time

---

## 🟩 Recorrente — Por Sprint

> Template de checklist a repetir a cada sprint

- [ ] Sprint Planning realizado
- [ ] DoD (Definition of Done) acordada
- [ ] Stories refinadas e estimadas
- [ ] Retro do sprint anterior documentada
- [ ] Velocity atualizada no backlog.md
- [ ] Demos com stakeholders agendadas

---

## 🟪 Framework — Melhorias Pendentes

> Melhorias ao próprio requirements-intel framework

- [ ] Criar integração com Linear para importar backlog automaticamente
- [ ] Criar integração com Azure DevOps para criar work items
- [ ] Adicionar template de release notes
- [ ] Criar skill `/rfp` para análise de RFPs e editais
- [ ] Adicionar suporte a múltiplos idiomas (EN)
- [ ] Criar versão CLI do pipeline (script bash)
- [ ] Adicionar validation automática de Mermaid syntax

---

## Histórico de Conclusões

| Item | Concluído em | Por |
|------|-------------|-----|
| | | |
