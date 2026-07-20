# PRD — Intelligent Operations Document Platform

**Versão:** 1.0
**Data:** 2026-07-19
**Autor:** @prd (Priya)
**Discovery:** [discovery-report.md](discovery-report.md)
**Status:** Draft

---

## 1. Visão Geral

**Tagline:** "Intelligent Operations Document Platform é uma plataforma de intake e processamento documental governado que permite a analistas de Finance e Customer Operations classificar, extrair, validar e rotear documentos com rastreabilidade completa, sem re-digitação manual e sem tratar output de IA não aprovado como dado confiável."

---

## 2. Problema e Oportunidade

Ver `discovery-report.md § 4`. Síntese: processo hoje fragmentado entre mailbox, SharePoint, sistemas de negócio e planilhas gera classificação inconsistente, re-digitação, baixa visibilidade de status, rastreabilidade fraca e risco de decisões materiais baseadas em IA não revisada. Oportunidade: pipeline único de intake→classificação→extração→confiança→validação→roteamento com auditoria completa.

---

## 3. Personas

### Persona 1: Finance Reviewer (Analista de Finance Operations)
| Campo | Detalhe |
|-------|---------|
| Perfil | Analista que processa faturas de fornecedor e evidências de pagamento |
| Objetivo principal | Validar rapidamente exceções e aprovar resultados extraídos com confiança |
| Dor atual | Re-digitação manual de valores, dificuldade de rastrear origem do dado |
| Nível técnico | Intermediário |
| Frequência de uso | Diária, contínua durante o expediente |

### Persona 2: Customer Service Analyst
| Campo | Detalhe |
|-------|---------|
| Perfil | Analista que trata anexos de solicitações de clientes |
| Objetivo principal | Classificar e rotear solicitações rapidamente para o CRM correto |
| Dor atual | Identificação manual de tipo de documento, atraso na fila |
| Nível técnico | Básico/Intermediário |
| Frequência de uso | Diária, alto volume |

### Persona 3: Platform Operations Owner (Platform Engineering)
| Campo | Detalhe |
|-------|---------|
| Perfil | Time de engenharia responsável por operar a plataforma pós-handover |
| Objetivo principal | Confiabilidade, observabilidade, suportabilidade |
| Dor atual | Falta de visibilidade operacional consolidada hoje |

### Persona 4: Compliance / DPO
| Campo | Detalhe |
|-------|---------|
| Perfil | Autoridade de privacidade e proteção de dados |
| Objetivo principal | Minimização de dados, retenção, auditabilidade, base legal |
| Dor atual | Falta de proveniência e controle de retenção no processo manual |

---

## 4. User Journeys

### Journey 1: Validação de Fatura de Fornecedor (Persona: Finance Reviewer)
```
[E-mail com fatura chega] → [Intake gera ID único] → [Classificação + extração automática]
→ [Confiança calculada] → [Baixa confiança → fila de revisão] → [Reviewer aprova/corrige]
→ [Roteamento para sistema financeiro] → [Registro de auditoria criado]
```
**Happy Path:** documento classificado e extraído com alta confiança, aprovação automática por regra de negócio, roteamento direto.
**Edge Cases:** documento duplicado, campo crítico ausente, múltiplos idiomas no mesmo anexo.
**Erros e Fallbacks:** falha de roteamento vai para fila de exceção recuperável (FR-015); intake falho é reprocessado por política de retry (FR-004).

### Journey 2: Triagem de Solicitação de Cliente (Persona: Customer Service Analyst)
```
[Anexo de solicitação recebido] → [Classificação por taxonomia] → [Extração de campos]
→ [Confiança média → regra de negócio decide] → [Roteamento para CRM] → [Status visível na fila]
```
**Happy Path:** classificação correta, roteamento automático ao CRM.
**Edge Cases:** documento fora da taxonomia configurada (cai em exceção manual).
**Erros e Fallbacks:** reviewer pode escalar (FR-013).

---

## 5. Requerimentos Funcionais

| ID | Descrição | Critério de Aceitação | Prioridade | Origem |
|----|-----------|------------------------|------------|--------|
| FR-001 | Ingerir documentos e anexos de e-mail das fontes acordadas do piloto (mailbox, SharePoint) | Given um documento na fonte configurada, When o intake roda, Then um evento de ingestão é criado | Must | RQ-001 |
| FR-002 | Atribuir ID de processamento único a cada evento de intake | Given qualquer ingestão, When processada, Then um ID único e imutável é gravado | Must | RQ-001 |
| FR-003 | Detectar submissões duplicadas por lógica de matching configurável | Given dois documentos equivalentes, When ambos ingeridos, Then o segundo é sinalizado como duplicado | Must | RQ-001 |
| FR-004 | Reprocessar eventos de intake falhos segundo política documentada de retry | Given falha transitória de ingestão, When a política de retry aciona, Then o evento é reprocessado até o limite definido | Must | RQ-001 |
| FR-005 | Expor fila de exceção para falhas permanentes de intake | Given falha permanente, When o limite de retry é atingido, Then o evento aparece na fila de exceção | Must | RQ-001 |
| FR-006 | Classificar cada documento contra taxonomia configurável | Given um documento ingerido, When classificado, Then recebe um tipo da taxonomia com score de confiança | Must | RQ-002 |
| FR-007 | Extrair o conjunto de campos configurado por tipo de documento do piloto | Given um documento classificado, When extraído, Then os campos configurados são populados | Must | RQ-003 |
| FR-008 | Reter confiança, localização de origem e timestamp por campo extraído | Given qualquer campo extraído, When persistido, Then confiança + fonte + timestamp são armazenados | Must | RQ-003 |
| FR-009 | Suportar correção humana de valores extraídos | Given um campo extraído incorreto, When o reviewer corrige, Then o novo valor e o autor são registrados | Must | RQ-003, RQ-005 |
| FR-010 | Identificar versão de modelo, prompt, regra e configuração usada em cada resultado | Given qualquer resultado gerado, When consultado, Then a versão de modelo/prompt/regra é rastreável | Must | RQ-003 |
| FR-011 | Aplicar thresholds de confiança configuráveis | Given um resultado com score, When comparado ao threshold, Then a rota (auto/revisão) é definida | Must | RQ-004 |
| FR-012 | Exigir validação humana para campos críticos abaixo do threshold | Given um campo crítico com baixa confiança, When avaliado, Then é bloqueado até aprovação humana | Must | RQ-004, RQ-005 |
| FR-013 | Permitir que reviewers aprovem, rejeitem, corrijam ou escalem um item de trabalho | Given um item na fila, When o reviewer age, Then o status muda para aprovado/rejeitado/corrigido/escalado | Must | RQ-004 |
| FR-014 | Rotear resultados aprovados para o sistema alvo (financeiro ou CRM) | Given um resultado aprovado, When roteado, Then o sistema alvo recebe o dado | Must | RQ-006 |
| FR-015 | Tornar falhas de roteamento recuperáveis e auditáveis | Given uma falha de roteamento, When detectada, Then é registrada e reprocessável | Must | RQ-006 |
| FR-016 | Permitir busca de registros de processamento por data, fonte, tipo, status e identificador de negócio | Given critérios de busca, When aplicados, Then registros correspondentes são retornados | Must | RQ-007 |
| FR-017 | Exibir fonte original, valores extraídos, correções, aprovações e status downstream em um único registro | Given um registro selecionado, When aberto, Then todo o histórico é visível | Must | RQ-007, RQ-008 |
| FR-018 | Fornecer métricas operacionais de volume, latência, acurácia, exceções e ageing | Given o dashboard, When acessado por usuário autorizado, Then as métricas atuais são exibidas | Should | RQ-011 |
| FR-019 | Registrar em auditoria ator, ação, timestamp, valor anterior e novo valor | Given qualquer ação material, When executada, Then um registro de auditoria imutável é criado | Must | RQ-008 |
| FR-020 | Suportar classificação/extração em inglês, português e italiano no piloto | Given um documento em EN/PT/IT, When processado, Then o resultado inclui idioma detectado e acurácia correspondente | Should | RQ-009 |
| FR-021 | Permitir configuração de novo tipo de documento sem redeploy completo | Given um novo tipo de documento, When configurado via taxonomia/regras, Then o pipeline o processa sem alteração de código core | Should | RQ-010 |

### Detalhamento dos FRs Críticos

#### FR-005: Fila de Exceção de Intake
**Descrição:** Toda falha permanente de ingestão (após esgotar retries) deve ficar visível e acionável em uma fila de exceção dedicada, nunca silenciosamente descartada.
**Critérios de Aceitação:**
- Given um evento de intake que falhou N vezes (N configurável), When o limite é atingido, Then o evento aparece na fila de exceção com motivo da falha
- Given um item na fila de exceção, When um operador o reprocessa manualmente, Then o resultado é registrado em auditoria
**Prioridade:** Must Have
**Origem:** RQ-001

#### FR-012: Gate de Aprovação para Campos Críticos
**Descrição:** Nenhum campo classificado como crítico pode ser tratado como dado confiável ou disparar ação downstream sem aprovação humana quando a confiança estiver abaixo do threshold configurado. Este é o controle-chave de governança de IA do RFP (BR-005).
**Critérios de Aceitação:**
- Given um campo crítico com confiança < threshold, When o pipeline avalia, Then o item é bloqueado em "pending_approval" e não é roteado
- Given um campo crítico aprovado, When roteado, Then o registro de auditoria referencia o aprovador e o timestamp
**Prioridade:** Must Have
**Origem:** RQ-004, RQ-005

#### FR-019: Auditoria Imutável
**Descrição:** Toda ação material (classificação, extração, correção, aprovação, roteamento) gera um registro de auditoria com ator, ação, timestamp e diff de valor, protegido contra alteração.
**Critérios de Aceitação:**
- Given qualquer ação material, When executada, Then o registro de auditoria é gravado antes da confirmação da ação ao usuário
- Given um registro de auditoria, When uma tentativa de alteração ocorre, Then ela é rejeitada ou versionada de forma append-only
**Prioridade:** Must Have
**Origem:** RQ-008

---

## 6. Requerimentos Não-Funcionais

| ID | Categoria | Requerimento | Métrica | Origem |
|----|-----------|--------------|---------|--------|
| NFR-001 | Disponibilidade | Disponibilidade do serviço de produção do piloto | ≥ 99,5% mensal, excluindo manutenção aprovada | RQ-005 |
| NFR-002 | Performance | Carregamento de página da fila interativa | ≤ 3s p95 | RQ-007 |
| NFR-003 | Performance | Conclusão de processamento de documento padrão | ≤ 60s p95, excluindo atrasos externos | RQ-002/003 |
| NFR-004 | Escalabilidade | Capacidade mensal de processamento | ≥ 250.000 páginas sem redesenho | RQ-001 |
| NFR-005 | Confiabilidade | RPO (Recovery Point Objective) | ≤ 15 minutos para estado da plataforma | — |
| NFR-006 | Confiabilidade | RTO (Recovery Time Objective) | ≤ 4 horas para serviço crítico | — |
| NFR-007 | Segurança | Identidade | SSO corporativo + MFA; least privilege | RQ-005 |
| NFR-008 | Segurança | Proteção de dados | Criptografia em trânsito e em repouso | — |
| NFR-009 | Privacidade | Dados pessoais | Minimização, retenção e exclusão configuráveis | — |
| NFR-010 | Auditabilidade | Lineage de processamento | 100% dos registros aprovados rastreáveis à fonte | RQ-008 |
| NFR-011 | Acessibilidade | Interface de usuário | WCAG 2.1 AA (meta de piloto) | — |
| NFR-012 | Manutenibilidade | Testes automatizados | Cobertura proposta pelo fornecedor; caminhos críticos obrigatórios | — |
| NFR-013 | Observabilidade | Monitoramento | Logs, métricas, traces e alertas acionáveis | — |
| NFR-014 | Portabilidade | Capacidade de saída (exit) | Exportação de configuração, auditoria e dados de negócio | — |
| NFR-015 | Sustentabilidade | Eficiência de nuvem | Fornecedor descreve abordagem de otimização de custo/recurso | — |

---

## 7. Restrições

### 7.1 Técnicas
- Ambiente preferencial Microsoft Azure (alternativa aceita com justificativa).
- Ambientes segregados: dev, test, produção.
- Nenhuma decisão financeira, legal ou operacionalmente material tomada apenas por IA generativa não revisada (regra transversal a FR-012).

### 7.2 Regulatórias
- Residência de dados UK e UE/EEE.
- Minimização de dados pessoais, retenção e exclusão configuráveis (NFR-009).
- Proibição de PII/dado sensível em logs de diagnóstico por padrão.

### 7.3 Prazo e Orçamento
- Timeline indicativa de 25 semanas (mobilização → go-live), RFP §18.
- Orçamento não fixado no RFP; custos a estruturar na resposta comercial (RFP §21) e no Business Case.

---

## 8. Premissas

1. Sistema financeiro e CRM alvo do piloto serão confirmados no início da Discovery detalhada (questão aberta #1 do RFP).
2. Volumes indicativos (180k páginas/mês) serão validados antes do dimensionamento final de capacidade.
3. Dados de amostra rotulados estarão disponíveis via SMEs de Finance e Customer Operations.
4. Dados de produção não serão necessários no piloto onde sintético/anonimizado for suficiente.

---

## 9. Fora de Escopo (Explícito)

- Substituição de ERP/CRM existentes.
- Aprovação financeira totalmente autônoma.
- Interpretação/aconselhamento jurídico autônomo.
- Redesenho de todos os processos de negócio.
- Migração de todo o histórico documental.
- Data warehouse corporativo de propósito geral.
- Contratos/documentos legais, idiomas além de EN/PT/IT, novos ERPs/CRMs, reconciliação automatizada, canais self-service, analytics preditivo avançado — tudo escopo futuro pós-piloto.
- Uso de serviços públicos de IA não aprovados com dados confidenciais.

---

## 10. Critérios de Sucesso

| Métrica | Meta | Prazo de Medição |
|---------|------|--------------------|
| Documentos com status de processamento | ≥ 95% | Fim do piloto |
| Documentos classificados corretamente | ≥ 90% | Fim do piloto |
| Acurácia de extração de campos prioritários | ≥ 85% | Fim do piloto, sobre conjunto de teste acordado |
| Registros aprovados com lineage completo | 100% | Fim do piloto |
| Campos críticos de baixa confiança roteados para revisão | 100% | Fim do piloto |
| Achados críticos de segurança/privacidade abertos no go-live | 0 | Go-live |
| Reviewers aptos após treinamento | ≥ 80% | Fim do treinamento |

---

## 11. Rastreabilidade

| FR/NFR | Origem Discovery | Rastreado? |
|--------|--------------------|------------|
| FR-001 a FR-005 | RQ-001 | ✅ |
| FR-006 a FR-010 | RQ-002, RQ-003 | ✅ |
| FR-011 a FR-013 | RQ-004, RQ-005 | ✅ |
| FR-014, FR-015 | RQ-006 | ✅ |
| FR-016 a FR-019 | RQ-007, RQ-008 | ✅ |
| FR-020 | RQ-009 | ✅ |
| FR-021 | RQ-010 | ✅ |
| NFR-001 a NFR-015 | RQ-005, RQ-011, RQ-012 | ✅ |

---

## Histórico de Versões

| Versão | Data | Autor | Mudança |
|--------|------|-------|---------|
| 1.0 | 2026-07-19 | @prd | Versão inicial derivada do discovery-report.md e do RFP-001 |
