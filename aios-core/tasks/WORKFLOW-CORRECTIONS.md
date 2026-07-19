# Correções Críticas do Workflow DPABB Framework

**Data:** 18 de julho de 2026  
**Status:** Pronto para implementação  
**Impacto:** Crítico — Valida 75% dos riscos de qualidade  

---

## 🔴 CORREÇÃO 1: Azure CAF Validation na Fase Architect

**Arquivo:** `skills/architect/SKILL.md` → Etapa 9 (antes de SAVE)

**Adicionar:**
```markdown
## Etapa 8b — Azure CAF Validation (obrigatório)

Validar arquitetura contra **Azure Well-Architected Framework** — 5 pilares:

### Security Pillar
- [ ] Autenticação: Entra ID (OAuth2/OIDC) obrigatório
- [ ] Autorização: RBAC com granularidade mínima necessária
- [ ] Managed Identity: Zero hardcoded credentials (aplicações usam MSI)
- [ ] Criptografia em trânsito: TLS 1.3 obrigatório
- [ ] Criptografia em repouso: AES-256 (Transparent Data Encryption para SQL)
- [ ] Segredos: Azure Key Vault obrigatório
- [ ] Network: VNET + NSGs + Private Endpoints

**Artefato:** Tabela Security Strategy no architecture.md

### Reliability Pillar
- [ ] SLA Target: Documentar (99.9%, 99.95%, etc.)
- [ ] Backup Strategy: Frequência e retenção
- [ ] RTO/RPO: Recovery Time Objective e Recovery Point Objective definidos
- [ ] Disaster Recovery: Multi-region? Failover automático?
- [ ] Health Checks: Liveness + Readiness probes

**Artefato:** Seção D.4 Estratégia de Observabilidade (3 pilares)

### Cost Optimization Pillar
- [ ] Right-sizing: Recursos não devem ser over-provisioned
- [ ] Reserved Instances: Para workloads previsíveis
- [ ] Auto-scaling: Configured para picos
- [ ] Unused resources: Nenhum recurso sem propósito
- [ ] Monitoring: Alertas de custo ativados

**Artefato:** Tabela E.3 Build vs Buy vs Integrate com custos estimados

### Operational Excellence Pillar
- [ ] Monitoring: Azure Monitor + Application Insights
- [ ] Logging: Estruturado, 90-365 dias retenção
- [ ] Alerting: SLA-based alerts definidos
- [ ] Runbooks: Procedimentos para operações críticas
- [ ] Documentation: Architecture.md + README + runbooks

**Artefato:** Phase D seção Observabilidade + Phase E riscos

### Performance Efficiency Pillar
- [ ] Scalability: Horizontal scaling configured
- [ ] Caching: Redis / CDN estratégia definida
- [ ] Connection Pooling: DB + Redis
- [ ] Latency SLA: P99 < {target}ms
- [ ] Throughput: {N} req/s target com headroom

**Artefato:** Phase D diagrama tecnologia + NFR Coverage

**Validação:** Gerar relatório JSON:
```json
{
  "azure_caf_validation": {
    "security": { "compliant": true, "gaps": [], "review_date": "2026-07-18" },
    "reliability": { "compliant": true, "gaps": [], "review_date": "2026-07-18" },
    "cost_optimization": { "compliant": true, "gaps": [], "review_date": "2026-07-18" },
    "operational_excellence": { "compliant": true, "gaps": [], "review_date": "2026-07-18" },
    "performance_efficiency": { "compliant": true, "gaps": [], "review_date": "2026-07-18" },
    "overall_status": "PASS|PASS_WITH_RISKS|FAIL"
  }
}
```

---

## 🔴 CORREÇÃO 2: Handoff Structure com JSON Validável

**Arquivo:** `framework/pipeline-state.json` → Adicionar campo "handoff"

**Estrutura JSON (novo schema):**

```json
{
  "phases": {
    "architect": {
      "status": "completed",
      "handoff": {
        "from_previous_phase": {
          "summary": "PRD com 12 FRs, 8 NFRs, 4 personas",
          "decisions_applied": ["FR-001 requer low latency", "Dados sensíveis LGPD"],
          "constraints": ["Equipe 8 pessoas", "Budget R$ 500K"],
          "quality_score": "Good|Fair|Poor"
        },
        "to_next_phase": {
          "summary": "Monolito Modular com Azure services",
          "critical_decisions": [
            "ADR-001: Monolito Modular (não microservices)",
            "ADR-002: Azure SQL + Redis Cache",
            "ADR-003: Entra ID OAuth2/OIDC"
          ],
          "team_estimated": 8,
          "cost_monthly_peak_brl": 166600,
          "cost_total_capex_brl": 1900600,
          "risks_identified": 5,
          "nfr_coverage_pct": 100,
          "azure_caf_status": "PASS",
          "stories_technical_from_nfrs": [
            "TECH-PERF-001: Redis cache setup",
            "TECH-SEC-001: Entra ID integration",
            "TECH-OPS-001: App Insights monitoring"
          ],
          "spike_stories_from_risks": [
            "SPIKE-PERF-001: Load testing 1000 req/s (8 pts)",
            "SPIKE-SEC-001: Penetration testing (13 pts)"
          ]
        }
      }
    }
  }
}
```

**Validação:** Script verifica que:
- ✅ `from_previous_phase` está preenchido antes de marcar phase "completed"
- ✅ `to_next_phase` tem mínimo 3 campos críticos
- ✅ `stories_technical_from_nfrs` tem entrada para cada NFR Must/Should
- ✅ `spike_stories_from_risks` tem entrada para cada risco com score > 0.3

---

## 🔴 CORREÇÃO 3: NFR → Story Técnica Rastreabilidade

**Arquivo:** `templates/architect/architecture.md` → Seção NFR Coverage

**Adicionar coluna "Story Técnica":**

```markdown
## NFR Coverage (Cobertura 100%)

| NFR | Categoria | Target | Solução | Fase | ADR | Story Técnica Sugerida |
|-----|-----------|--------|---------|------|-----|----------------------|
| NFR-PERF-001 | Performance | P99 < 500ms | Redis Cache | D | — | TECH-PERF-001 |
| NFR-PERF-002 | Performance | 1000 req/s | Load balancer + connection pooling | D | — | TECH-PERF-002 |
| NFR-SEC-001 | Segurança | OAuth2/OIDC + MFA | Entra ID | D | ADR-003 | TECH-SEC-001 |
| NFR-SEC-002 | Segurança | TLS 1.3 em trânsito | mTLS entre serviços | D | — | TECH-SEC-002 |
| NFR-COMP-001 | Compliance | LGPD (Brasil) | Hard delete 12 meses | C1 | — | TECH-COMP-001 |
```

**No handoff do memory.md, adicionar seção:**

```markdown
### Architect → Backlog: NFR Rastreability

Stories técnicas obrigatórias (geradas dos NFRs):

- TECH-PERF-001: Implementar cache Redis com TTL 5min, hit rate >80%
- TECH-PERF-002: Load testing: 1000 req/s, latência P99 < 500ms
- TECH-SEC-001: Integrar Entra ID OAuth2/OIDC com Conditional Access
- TECH-SEC-002: Validar mTLS entre APIM → Backend
- TECH-COMP-001: Implementar data retention policy LGPD (12m hard delete)
- TECH-OPS-001: Setup Application Insights + alertas
```

**@backlog valida:**
- ✅ Todas as stories TECH-* estão presentes no Sprint 1
- ✅ Nenhum NFR Must/Should fica sem story técnica
- ✅ Points estimados correspondem a esforço de implementação

---

## 🔴 CORREÇÃO 4: Team Plan → CapEx Automático

**Arquivo:** `skills/architect/SKILL.md` → Etapa 7b Phase E Team Plan

**Adicionar ao handoff memory.md:**

```json
{
  "team_plan": {
    "composition": [
      {
        "role": "Product Owner",
        "seniority": "Lead",
        "dedication_pct": 100,
        "cost_per_month_brl": 15000,
        "start_week": 1,
        "duration_weeks": 18,
        "total_cost_brl": 270000
      },
      {
        "role": "Tech Lead / Arquiteto",
        "seniority": "Senior",
        "dedication_pct": 80,
        "cost_per_month_brl": 18000,
        "start_week": 1,
        "duration_weeks": 18,
        "total_cost_brl": 388800
      }
    ],
    "metadata": {
      "total_people": 8,
      "peak_monthly_cost_brl": 166600,
      "total_capex_brl": 1900600,
      "overhead_multiplier_clt": 1.7,
      "calculation_date": "2026-07-18"
    }
  }
}
```

**@business-case lê e usa:**
```
CapEx Calculation:
  Team Cost: R$ 1.900.600 (do handoff acima)
  Infrastructure: R$ X (do Phase D)
  + Software licenses: R$ Y
  + Contingency (15%): R$ Z
  = Total CapEx: R$ {soma}
```

---

## 🔴 CORREÇÃO 5: Approval Gates com Proprietários Explícitos

**Arquivo:** `framework/pipeline-state.json` → Campo quality_gate

```json
{
  "phases": {
    "architect": {
      "quality_gate": {
        "required_approvals": 2,
        "approvers": [
          {
            "role": "Tech Lead",
            "status": "pending|approved|rejected",
            "approved_at": null,
            "comments": null
          },
          {
            "role": "Product Owner",
            "status": "pending|approved|rejected",
            "approved_at": null,
            "comments": null
          }
        ],
        "gate_status": "pending",
        "gate_completion_pct": 0
      }
    }
  }
}
```

**Regra:** Fase só passa para "completed" se `gate_status = "approved"` (2/2 aprovadores)

**Comando novo:**
```bash
*approve architect "Tech Lead" "Architecture is solid, ready to proceed"
  ├── Valida que "Tech Lead" é aprovador válido
  ├── Marca como "approved" + timestamp
  ├── Calcula gate_completion_pct = 1/2
  └── Se 2/2: marca phase "completed", avança para próxima

*gate-status architect
  ├── Mostra quem aprovou, quem falta, comentários
```

---

## 🔴 CORREÇÃO 6: Riscos Técnicos → Spike Stories

**Arquivo:** `templates/architect/architecture.md` → Phase E Riscos

**Estrutura de riscos (já existe, mas agregar ao handoff):**

```json
{
  "technical_risks": [
    {
      "id": "RT-01",
      "title": "Database performance degradation under high concurrency",
      "probability": 0.6,
      "impact": 0.9,
      "score": 0.54,
      "severity": "HIGH",
      "mitigation": "Load testing early + connection pooling + read replicas",
      "mitigation_cost_brl": 5000,
      "suggested_spike": {
        "id": "SPIKE-PERF-001",
        "title": "Load testing: validate 1000 req/s with <500ms P99 latency",
        "estimated_points": 8,
        "suggested_sprint": 1,
        "acceptance_criteria": [
          "Teste com K6 simulando 1000 req/s concorrentes",
          "Latência P99 < 500ms confirmada",
          "CPU/Memory < 70% durante teste",
          "Relatório gerado com gráficos"
        ]
      }
    }
  ]
}
```

**No handoff memory.md, adicionar:**

```markdown
### Architect → Backlog: Technical Risks

Riscos técnicos mapeados para stories de spike:

- SPIKE-PERF-001 (8 pts): Load testing database (RT-01 mitigation) — Sprint 1
- SPIKE-SEC-001 (13 pts): Penetration testing + security audit (RT-03 mitigation) — Sprint 2
```

**@backlog valida:**
- ✅ Cada risco com score > 0.3 tem spike story correspondente
- ✅ Spike stories estão presentes no backlog
- ✅ Mitigation costs são incorporados no CapEx

---

## 📋 Implementação Roadmap

| # | Correção | Arquivo | Prioridade | Esforço | Bloqueador |
|---|----------|---------|-----------|---------|-----------|
| 1 | Azure CAF Validation | skills/architect/SKILL.md | 🔴 P0 | 2h | SIM |
| 2 | Handoff JSON Structure | framework/pipeline-state.json | 🔴 P0 | 1h | SIM |
| 3 | NFR → Story Rastreability | templates/architect/architecture.md | 🔴 P0 | 2h | SIM |
| 4 | Team Plan → CapEx | skills/architect/SKILL.md | 🔴 P0 | 1h | SIM |
| 5 | Approval Gates | framework/pipeline-state.json + CLAUDE.md | 🔴 P0 | 1h | SIM |
| 6 | Riscos → Spike Stories | templates/architect/architecture.md | 🔴 P0 | 1h | SIM |
| 7 | Icon Validation | skills/architect/SKILL.md | 🟡 P1 | 1h | NÃO |
| 8 | *resume Command | CLAUDE.md + skills/*/SKILL.md | 🟡 P1 | 1h | NÃO |
| 9 | HTML Export (*html) | templates/html/sme-view.html | 🟡 P1 | 2h | NÃO |

**Total Crítico:** ~9 horas  
**Total Alto:** ~4 horas  
**Tempo Total:** ~13 horas

---

## ✅ Validação Após Implementação

Executar em ordem:

```bash
# 1. Validar schema JSON
npm test -- framework/pipeline-state.json

# 2. Testar handoff com projeto real
@discovery *start -- ip-word-addin
@prd *generate
@architect *design
  # Verificar: architecture.md tem Azure CAF validation
  # Verificar: memory.md tem handoff JSON estruturado
  # Verificar: NFR Coverage tem coluna Story Técnica
  # Verificar: Team Plan tem custo CapEx

@backlog *breakdown
  # Verificar: TECH-* stories criadas do NFRs
  # Verificar: SPIKE-* stories criadas dos riscos

@business-case *analyze
  # Verificar: CapEx = team_plan.total_capex_brl + infra + licenses

# 3. Validar aprovações
*approve architect "Tech Lead" "OK"
*approve architect "Product Owner" "OK"
*gate-status architect
  # Esperar: gate_status = "approved", phase = "completed"
```

---

## 🎯 Outcome Esperado

Após implementação das 6 correções CRÍTICAS:

- ✅ Arquitetura é validada contra Azure CAF (5 pilares)
- ✅ Handoff entre fases é estruturado e rastreável (JSON)
- ✅ 100% dos NFRs conectam a stories técnicas
- ✅ Custos de equipe fluem automaticamente para Business Case
- ✅ Gates de qualidade têm aprovadores explícitos
- ✅ Riscos técnicos geram stories de mitigação no backlog
- ✅ Rastreabilidade completa: Discovery → PRD → Architecture → Backlog → Business Case

**Resultado:** Pipeline seguro, validável e auditável ✅
