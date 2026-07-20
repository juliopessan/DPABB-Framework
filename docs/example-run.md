# Exemplo de Execução — Sistema de Agendamento Médico

> Demonstra como usar o pipeline DPABB Framework em um projeto real.

---

## Iniciando o Pipeline

```
@discovery *start
```

Scout: "Vamos começar. Qual é o nome do projeto e em qual contexto ele será desenvolvido?"

Usuário: "MedSchedule — sistema de agendamento de consultas para uma clínica médica de médio porte."

Scout: "Perfeito. Descreva o problema principal em 2-3 frases."

Usuário: "Hoje os pacientes agendam consultas por telefone, causando filas de espera e erros de agendamento. A recepção perde 3h/dia com agendamentos e remarcações. Os médicos não têm visibilidade da agenda em tempo real."

...

---

## Outputs Gerados

Após completar o pipeline, em `docs/output/medschedule/`:

### discovery-report.md
- Problema: Agendamento manual ineficiente (3h/dia perdidas, 15% de no-show)
- Stakeholders: Pacientes, Médicos, Recepcionistas, Diretoria
- Restrições: LGPD, integração com sistema de prontuário atual, orçamento R$ 150k
- KPIs: Redução de no-show para <5%, tempo de agendamento <2min

### prd.md
- 12 FRs (8 Must, 3 Should, 1 Could)
- 6 NFRs (performance, segurança, LGPD, disponibilidade, usabilidade, mobile)
- Personas: Paciente (digital e não-digital), Médico, Recepcionista
- Fora de escopo: Telemedicina, prontuário eletrônico completo

### architecture.md
- Estilo: Monolito Modular (equipe pequena, prazo 4 meses)
- Stack: Azure App Service + ASP.NET Core + Azure SQL + Microsoft Entra ID + Azure Front Door
- 4 ADRs documentados
- Cobertura 100% de NFRs

### backlog.md
- 4 Epics, 18 Stories, 89 story points
- Sprint 1 (MVP): 32pts — Cadastro + Agendamento básico + Auth
- Rastreabilidade 100%

### business-case.md
- Investimento: R$ 95k (desenvolvimento) + R$ 2k/mês (infra)
- Benefícios: R$ 78k/ano (3h/dia × 12 meses × R$ 21/h) + R$ 45k/ano (redução no-show)
- ROI 24 meses: 67%
- Payback: 14 meses
- **Recomendação: GO ✅**
