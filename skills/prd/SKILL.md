# Skill: PRD Generation

**Agente:** @prd (Priya)  
**Trigger:** `*generate`  
**Input:** `docs/output/{projeto}/discovery-report.md`  
**Output:** `docs/output/{projeto}/prd.md`

---

## Objetivo

Transformar o discovery report em um PRD estruturado com FRs, NFRs, personas e rastreabilidade completa.

---

## Pré-condições

- [ ] `discovery-report.md` existe e está aprovado
- [ ] Problema central claramente definido no discovery
- [ ] Pelo menos 3 stakeholders identificados

---

## Execução

### Etapa 1 — Leitura e Análise
1. Ler `discovery-report.md` na íntegra
2. Listar todos os requerimentos de alto nível (RQ-XXX)
3. Identificar personas implícitas e explícitas
4. Mapear restrições já identificadas

### Etapa 2 — Personas
Para cada stakeholder do discovery que for usuário do sistema:
- Criar Persona com: perfil, objetivo, dor, nível técnico, frequência de uso
- Nomear a persona (ex: "Ana, Gerente de Vendas")

### Etapa 3 — User Journeys
Para cada persona principal:
- Mapear happy path (fluxo principal)
- Identificar edge cases críticos
- Documentar erros e fallbacks esperados

### Etapa 4 — Requerimentos Funcionais
Para cada RQ do discovery:
- Expandir em 1 ou mais FRs específicos e testáveis
- Atribuir ID sequencial: FR-001, FR-002...
- Definir prioridade MoSCoW (Must/Should/Could/Won't)
- Escrever critério de aceitação (Given/When/Then)

### Etapa 5 — Requerimentos Não-Funcionais
Sempre incluir (mesmo que discovery não cite explicitamente):
- Performance (tempo de resposta, throughput)
- Segurança (autenticação, autorização, criptografia)
- Disponibilidade (SLA, uptime)
- Escalabilidade (carga máxima)
- LGPD (se dados pessoais envolvidos)
- Manutenibilidade (cobertura de testes)

### Etapa 6 — Fora de Escopo
Listar explicitamente funcionalidades que:
- Foram mencionadas mas estão fora desta versão
- Podem ser confundidas com o escopo

### Etapa 7 — Rastreabilidade
Para cada FR/NFR, documentar a origem no discovery (RQ-XXX).

### Etapa 8 — Geração
Preencher template: `templates/prd/prd.md`  
Salvar em: `docs/output/{projeto}/prd.md`

---

## Perguntas de Clarificação (se necessário)

Fazer ao usuário apenas se o discovery for insuficiente:
- "O discovery menciona X como restrição — isso é hard constraint ou preferência?"
- "Para a persona Y, qual é o fluxo mais crítico a suportar no MVP?"
- "Não encontrei SLA definido — qual é o uptime esperado?"

---

## Validação

Executar checklist do agente (`agents/prd.md § Checklist de Qualidade`).  
Verificar rastreabilidade: toda FR/NFR deve ter origem no discovery.

---

## Handoff para @architect

```
PRD concluído.

FRs definidos: {N} ({N} Must Have)
NFRs definidos: {N}
Rastreabilidade: 100%

Decisões técnicas a considerar:
- {NFR crítico 1}
- {NFR crítico 2}

Pronto para @architect *design
```
