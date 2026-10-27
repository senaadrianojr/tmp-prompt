# Proposta de histórias — Darwin Monitor

**Épico:** Darwin Monitor — Monitoria da esteira de seguros pagos via cartão  
**História original:** SEGNP2-5340

**Objetivo:** Organizar as atividades necessárias para identificar, viabilizar e implementar os indicadores de monitoramento da esteira de seguros, permitindo entregas incrementais e estimativas de esforço mais confiáveis.

---

## História 01 — Definir e validar os requisitos funcionais dos indicadores do Darwin Monitor

**Responsável:** Negócio / Product Owner

### Descrição

Realizar o levantamento, refinamento e validação das definições funcionais dos indicadores previstos para o Darwin Monitor, utilizando como referência a planilha de indicadores e os critérios de aceite da história original.

Para cada indicador, deverão ser definidos ou validados:

- Objetivo e regra de negócio.
- Fórmula de cálculo e condições que caracterizam sucesso, falha ou inconsistência.
- Periodicidade e janela de apuração.
- Prioridade e criticidade operacional.
- Thresholds para alertas, quando aplicáveis.
- Necessidade de identificar contratos ou transações impactadas.
- Critérios de aceite e referências para validação dos resultados.

**Entregável:** Catálogo funcional de indicadores priorizados, com regras aprovadas pelo Negócio e eventuais pendências documentadas.

---

## História 02 — Realizar discovery técnico dos fluxos, integrações e fontes de dados dos indicadores

**Responsável:** Desenvolvimento

### Descrição

Realizar o levantamento técnico das aplicações e integrações envolvidas na esteira de seguros pagos via cartão, identificando onde são geradas, processadas, transmitidas e persistidas as informações necessárias aos indicadores do Darwin Monitor.

O levantamento deverá considerar:

- Aplicações e serviços envolvidos nos fluxos de negócio.
- Integrações entre sistemas, APIs e mensageria.
- Bancos de dados e outras fontes disponíveis.
- Logs, traces, métricas e eventos existentes no Datadog.
- Identificadores que permitam correlacionar operações entre aplicações.
- Disponibilidade, confiabilidade e limitações das informações.
- Lacunas de telemetria e dependências técnicas.

A investigação deverá considerar os indicadores priorizados pelo Negócio, utilizando as aplicações como referência principal para confirmação dos comportamentos técnicos.

**Entregável:** Mapeamento técnico das aplicações e integrações, acompanhado de uma matriz de indicadores × fontes de dados × disponibilidade, com lacunas e dependências identificadas.

---

## História 03 — Avaliar a viabilidade técnica de coleta e monitoramento dos indicadores

**Responsável:** Desenvolvimento  
**Tipo sugerido:** Spike técnico

### Descrição

Investigar e validar as alternativas técnicas para obtenção, cálculo e monitoramento dos indicadores do Darwin Monitor, considerando os resultados do discovery e as limitações identificadas na telemetria atual.

A avaliação deverá considerar, conforme a necessidade de cada indicador:

- Utilização de logs, traces e métricas existentes no Datadog.
- Ajustes de instrumentação nas aplicações.
- Consultas a bancos de dados ou APIs internas.
- Integrações com fontes externas de informação.
- Coleta e envio de métricas customizadas ao Datadog.
- Possibilidade de utilização de uma solução híbrida, caso as alternativas existentes não atendam aos requisitos.

Para os indicadores selecionados, deverão ser realizadas validações técnicas suficientes para demonstrar a viabilidade ou identificar os impedimentos da solução.

A análise também deverá considerar restrições de acesso, desempenho, segurança, custo e manutenção.

**Entregável:** Relatório de viabilidade técnica, indicando as alternativas recomendadas, evidências das validações, limitações, dependências e necessidades de desenvolvimento adicional.

---

## Histórias 04 — Implementar indicadores do Darwin Monitor

**Responsável:** Desenvolvimento

**Observação:** Criar uma história independente para cada indicador, conforme prioridade e viabilidade identificadas.

### Modelo de título

Implementar indicador [Nome do Indicador] no Darwin Monitor

### Descrição

Implementar o indicador [Nome do Indicador], conforme definição funcional aprovada e alternativa técnica validada durante o discovery.

A implementação deverá contemplar, quando aplicável:

- Validar a regra de cálculo e as fontes de dados.
- Implementar ou configurar os mecanismos de coleta necessários.
- Disponibilizar a métrica calculada de forma confiável e reproduzível.
- Construir o widget correspondente no dashboard.
- Configurar monitor e alerta conforme thresholds definidos pelo Negócio.
- Validar os resultados com as fontes de referência e os responsáveis de negócio.

**Entregável:** Indicador implementado, validado e disponibilizado para monitoramento, com documentação e evidências dos testes.

---

## Histórias 05 — Implementar habilitadores técnicos necessários aos indicadores

**Responsável:** Desenvolvimento / Equipes técnicas envolvidas

**Observação:** Criar histórias específicas somente quando forem identificadas dependências técnicas comprovadas.

### Modelo de título

Implementar [Habilitador Técnico] para viabilizar o indicador [Nome do Indicador]

### Descrição

Implementar as adequações técnicas identificadas durante o discovery e a análise de viabilidade, necessárias à obtenção ou ao monitoramento dos dados do indicador [Nome do Indicador].

As atividades poderão envolver:

- Instrumentação de aplicações para disponibilizar informações não observáveis atualmente.
- Criação ou adequação de métricas e eventos.
- Implementação de coletores ou integrações.
- Disponibilização de acessos e mecanismos seguros de consulta.
- Adequações para correlação de informações entre sistemas.

O escopo deverá ser definido a partir da dependência identificada, evitando alterações desnecessárias nas aplicações.

**Entregável:** Habilitador técnico implementado e validado, disponibilizando os dados ou recursos necessários à implementação do indicador associado.

---

## Organização sugerida para o backlog

**Criação inicial:**
- História 01 — Definição funcional dos indicadores.
- História 02 — Discovery técnico dos fluxos e fontes.
- História 03 — Spike de viabilidade técnica.

**Criação progressiva:**
- Uma história de implementação para cada indicador aprovado.
- Histórias de habilitadores técnicos quando houver necessidade comprovada.

As histórias de discovery e viabilidade podem avançar por conjuntos de indicadores priorizados, sem exigir o levantamento completo de toda a esteira antes das primeiras implementações.

A proposta permite separar definições de negócio, investigação técnica e implementação, tornando o esforço mais previsível e as entregas incrementais.

## Orientação para criação e refinamento

| Item | Criar agora? | Responsabilidade |
|---|---|---|
| Épico SEGNP2-5340 | Sim, se o Jira permitir conversão | PO |
| História 01 | Sim | Negócio / PO |
| História 02 | Sim | Desenvolvimento |
| História 03 | Sim | Desenvolvimento |
| Histórias 04 | Conforme indicadores forem aprovados | Desenvolvimento |
| Histórias 05 | Conforme dependências forem identificadas | Desenvolvimento / equipes técnicas |

As histórias 02 e 03 precisam ter um escopo limitado por indicadores prioritários para serem estimáveis. A PO pode criar as três histórias iniciais, com refinamento e estimativa das histórias 02 e 03 pelo time antes do início do trabalho. A decisão sobre arquitetura (Datadog, coleta externa ou solução híbrida) deve decorrer da investigação técnica, e não ser assumida antecipadamente.
