# Discovery Report — Intelligent Operations Document Platform

**Versão:** 1.0
**Data:** 2026-07-19
**Conduzido por:** @discovery (Scout)
**Status:** Draft
**Fonte:** RFP-001 — Northstar Operations Group *(fictício, teste de framework)*

---

## 1. Resumo Executivo

A Northstar Operations Group processa ~180.000 páginas, 65.000 e-mails e 12.000 documentos estruturados/semiestruturados por mês através de processos manuais fragmentados (caixas compartilhadas, pastas de rede, SharePoint, planilhas). Isso gera classificação inconsistente, re-digitação de dados, baixa rastreabilidade e risco de uso de resultados de IA não aprovados como se fossem fato de negócio. O programa busca uma **Plataforma Inteligente de Documentos de Operações** governada, com piloto de produção nas áreas de Finance Operations e Customer Operations, preservando revisão humana obrigatória para qualquer resultado crítico.

---

## 2. Contexto

| Campo | Valor |
|-------|-------|
| Empresa / Área | Northstar Operations Group — Finance Operations e Customer Operations (piloto) |
| Produto / Sistema | Intelligent Operations Document Platform (intake, classificação, extração, validação, busca, roteamento) |
| Gatilho do Projeto | Fragmentação operacional, re-digitação manual, falta de rastreabilidade fonte→ação, risco de uso indevido de output de IA |
| Prazo Indicativo | 25 semanas (mobilização → go-live), ver §18 do RFP |
| Orçamento Indicativo | Não informado no RFP — a definir na resposta comercial (Seção 21); custo será modelado no Business Case |

---

## 3. Stakeholders

| Stakeholder | Papel | Objetivo Principal | Nível de Influência |
|-------------|-------|---------------------|----------------------|
| Chief Operating Officer | Sponsor executivo | Valor de negócio e adoção | Alto |
| Finance Operations Director | Business owner | Precisão, throughput, controles | Alto |
| Customer Operations Director | Business owner | Tempo de resposta e qualidade de roteamento | Alto |
| Enterprise Architect | Autoridade de arquitetura | Padrões, integração, reuso | Alto |
| Data Protection Officer | Autoridade de privacidade | Minimização de dados, retenção, base legal | Alto |
| Information Security Lead | Autoridade de segurança | Identidade, secrets, rede, monitoramento | Alto |
| Finance reviewer | SME | Qualidade de validação e tratamento de exceções | Médio |
| Customer service analyst | Usuário final | Usabilidade e produtividade de fila | Médio |
| Platform engineering team | Owner de operação | Confiabilidade, deploy, suportabilidade | Alto |
| Procurement | Owner comercial | Preço, contrato, risco de fornecedor | Médio |

---

## 4. Problema Central

### 4.1 Declaração do Problema
> "Como analista de Finance Operations ou Customer Operations, enfrento classificação manual e re-digitação de dados de documentos operacionais, o que causa atrasos de fila, erros de retrabalho e perda de rastreabilidade, porque a ingestão hoje é fragmentada entre mailboxes, pastas de rede, SharePoint e sistemas de linha de negócio sem um pipeline de governança único."

### 4.2 Situação Atual (As-Is)
Analistas abrem manualmente cada e-mail/anexo, identificam o tipo de documento, copiam valores para planilha ou sistema de negócio, verificam completude e, se faltar dado, buscam a informação por e-mail/telefone antes de rotear ou submeter o registro. O status é atualizado manualmente e o reporting é periódico e não em tempo real (ver fluxograma §7 do RFP).

### 4.3 Impacto do Problema Não Resolvido
- **Financeiro:** custo de re-digitação e retrabalho, atraso em processamento de pagamentos/faturas.
- **Operacional:** filas com atraso, ageing de exceções sem dono claro, tempo de recuperação de documentos lento.
- **Reputacional:** tempo de resposta ao cliente degradado em Customer Operations.
- **Regulatório:** dificuldade de auditoria e resolução de disputas por ausência de proveniência fonte→campo→ação; risco de decisão material tomada a partir de output de IA não revisado.

---

## 5. Oportunidades

| # | Oportunidade | Valor Estimado | Urgência |
|---|--------------|-----------------|----------|
| 1 | Intake governado único (mailbox + SharePoint) eliminando triagem manual | Redução de tempo de classificação | Alta |
| 2 | Extração automática com confiança calibrada + fila de validação humana | Redução de re-digitação e erro | Alta |
| 3 | Roteamento automático para sistema financeiro e CRM | Redução de manuseio manual | Alta |
| 4 | Proveniência completa fonte→campo→ação para auditoria | Evidência para disputas e compliance | Média |
| 5 | Arquitetura reutilizável para novos domínios/tipos de documento pós-piloto | Escala futura sem redesenho | Média |

---

## 6. Requerimentos de Alto Nível

| ID | Requerimento | Tipo | Prioridade |
|----|--------------|------|------------|
| RQ-001 | Centralizar intake de documentos do piloto (mailbox + SharePoint) | Funcional | Must |
| RQ-002 | Classificar documentos com taxonomia configurável (regras + IA) | Funcional | Must |
| RQ-003 | Extrair campos configurados retendo proveniência de fonte | Funcional | Must |
| RQ-004 | Calcular confiança e rotear baixa confiança para revisão humana | Funcional | Must |
| RQ-005 | Impedir que resultado crítico não aprovado seja tratado como dado confiável | Não-Funcional (Governança) | Must |
| RQ-006 | Rotear resultados aprovados para 1 sistema financeiro e 1 CRM | Funcional | Must |
| RQ-007 | Fornecer filas de trabalho e visões de status por papel | Funcional | Must |
| RQ-008 | Registrar histórico de auditoria imutável/protegido | Não-Funcional (Compliance) | Must |
| RQ-009 | Suportar inglês, português e italiano no piloto | Funcional | Should |
| RQ-010 | Permitir configuração de novos tipos de documento sem redesenvolvimento completo | Não-Funcional (Manutenibilidade) | Should |
| RQ-011 | Fornecer dashboards operacionais e métricas exportáveis | Funcional | Should |
| RQ-012 | Fornecer roadmap de scale-up pós-piloto | Não-Funcional (Estratégico) | Must |

---

## 7. Restrições

### 7.1 Técnicas
- Ambiente preferencial Azure (alternativas aceitas com justificativa técnica/negócio).
- Ambientes separados: desenvolvimento, teste e produção.
- SSO corporativo + MFA, least privilege.
- Nenhuma decisão financeira, legal ou operacionalmente material pode ser tomada exclusivamente por output de IA generativa não revisado.

### 7.2 Regulatórias / Compliance
- Minimização de dados, retenção e exclusão configuráveis.
- Proibição de gravar conteúdo sensível em logs de diagnóstico por padrão.
- Residência de dados para UK e UE/EEE.
- Não usar serviços públicos de IA não aprovados com dados confidenciais.
- WCAG 2.1 AA (acessibilidade) como meta de piloto.

### 7.3 Organizacionais
- Piloto restrito a Finance Operations (faturas de fornecedor, evidência de pagamento) e Customer Operations (anexos de solicitação de serviço).
- Fora de escopo: substituição de ERP/CRM, aprovação financeira autônoma, interpretação jurídica autônoma, redesenho de todos os processos, migração de todo histórico, data warehouse de propósito geral.

### 7.4 Integrações Obrigatórias
| Sistema | Tipo de Integração | SLA Atual |
|---------|---------------------|-----------|
| Shared mailbox | Ingestão de e-mail/anexo | A confirmar em Discovery detalhado |
| SharePoint | Retrieval de documento + metadata update | A confirmar |
| Sistema financeiro (1) | Submissão + status feedback | A confirmar — depende de qual sistema (questão aberta) |
| CRM (1) | Criação/atualização de caso | A confirmar — depende de qual CRM (questão aberta) |
| Identity Provider corporativo | SSO/MFA | A confirmar |

---

## 8. Premissas

1. A organização detém ou está autorizada a processar os dados do piloto.
2. Serviços de identidade corporativa estão disponíveis para integração.
3. O sistema financeiro e o CRM expõem interfaces de integração suportadas.
4. SMEs de negócio podem fornecer amostras rotuladas e tempo de validação.
5. O piloto usará dados anonimizados/sintéticos quando dado de produção não for necessário.
6. Volumes indicativos (180k páginas/mês, 65k e-mails/mês, 12k documentos/mês) são estimativas e serão validados em Discovery aprofundado.

---

## 9. Riscos Identificados

| Risco | Probabilidade | Impacto | Mitigação Inicial |
|-------|----------------|---------|--------------------|
| Sistema financeiro/CRM alvo não definidos até o fim da Discovery | Alta | Alto | Priorizar decisão no Kick-off; bloquear integrações até confirmação |
| Baixa qualidade de dados de treino/rotulagem (SMEs sem tempo) | Média | Alto | Plano de amostragem mínima + sintético desde o início |
| Uso indevido de output de IA não revisado como fato de negócio | Média | Crítico | Gate de aprovação obrigatório por campo crítico (BR-005) |
| Dados sensíveis (PII/financeiro) vazando para logs de diagnóstico | Média | Alto | Redação de log por padrão + revisão de segurança antes do go-live |
| Volumes reais divergentes das estimativas indicativas | Média | Médio | Validar baseline real na Discovery antes de dimensionar capacidade (NFR-004) |
| Multilinguismo (EN/PT/IT) com acurácia desigual entre idiomas | Média | Médio | Medir acurácia por idioma separadamente nos critérios de sucesso |

---

## 10. Métricas de Sucesso

| Métrica | Baseline Atual | Meta | Prazo |
|---------|-----------------|------|-------|
| Documentos com status de processamento | Não medido (processo manual) | ≥ 95% | Fim do piloto |
| Documentos classificados corretamente na taxonomia | Não medido | ≥ 90% | Fim do piloto |
| Acurácia de extração de campos prioritários | Não medido | ≥ 85% | Fim do piloto |
| Registros aprovados com lineage completo | Não medido | 100% | Fim do piloto |
| Campos críticos de baixa confiança roteados para revisão humana | Não medido | 100% | Fim do piloto |
| Achados críticos de segurança/privacidade em aberto no go-live | N/A | 0 | Go-live |
| Reviewers capazes de concluir o workflow após treinamento | N/A | ≥ 80% | Fim do treinamento |

---

## 11. Fora de Escopo (Inicial)

- Substituição do ERP ou CRM existentes.
- Aprovação autônoma completa de transações financeiras.
- Interpretação jurídica ou aconselhamento legal autônomo.
- Redesenho de todos os processos de negócio existentes.
- Migração de todo o histórico documental.
- Construção de data warehouse corporativo de propósito geral.
- Uso de serviços públicos de IA não aprovados com dados confidenciais.
- Contratos e documentos legais, idiomas além de EN/PT/IT, novos ERPs/CRMs, reconciliação automatizada, canais self-service, analytics preditivo avançado (todos: escopo futuro pós-piloto, §5.2 do RFP).

---

## 12. Próximos Passos

- [x] @prd — Gerar PRD a partir deste discovery
- [ ] Validar premissas com stakeholders (sistema financeiro/CRM alvo, volumes reais, campos críticos)
- [ ] Confirmar dados de categoria especial/sensível presentes na amostra de documentos

---

## Histórico de Versões

| Versão | Data | Autor | Mudança |
|--------|------|-------|---------|
| 1.0 | 2026-07-19 | @discovery | Versão inicial a partir do RFP-001 |
