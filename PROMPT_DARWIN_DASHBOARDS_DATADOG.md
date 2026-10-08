# Prompt — Revalidar Darwin Monitor no Datadog e atualizar documentação de dashboards

Atue como um engenheiro de software sênior especializado em Java/Spring Boot, observabilidade, Datadog APM, distributed tracing, métricas, logs e análise de fluxos de negócio.

O objetivo desta etapa é **revalidar o discovery do Darwin Monitor usando dados reais do Datadog** e atualizar a documentação de dashboards para que ela represente o que é de fato possível montar hoje com a telemetria existente.

## Restrições desta etapa

- NÃO criar, alterar ou excluir dashboards no Datadog.
- NÃO criar, alterar ou excluir monitors.
- NÃO criar alertas ou notificações.
- NÃO alterar código das aplicações.
- NÃO alterar instrumentação nesta etapa.
- NÃO alterar configurações do Datadog.
- Operar no Datadog apenas em modo de leitura/análise.
- NÃO imprimir, registrar, documentar ou expor cookies, tokens, headers de autenticação ou qualquer credencial.
- Se a autenticação falhar, documentar o bloqueio e continuar com análise estática; não inventar resultados.
- Todo resultado técnico desta etapa deve ser registrado dentro de `darwin-monitor/`.

A etapa atual é exclusivamente de **dashboard + validação da telemetria disponível**.

---

# 1. Ler primeiro todo o contexto existente

Antes de consultar o Datadog, localize e leia a documentação já produzida em:

`darwin-monitor/`

Leia principalmente, quando existirem:

- `README.md`
- `01-arquitetura-e-fontes.md`
- `gaps-e-proximos-passos.md`
- `indicadores/`
- `fontes/`
- `dashboards/`

Dê atenção especial aos documentos relacionados a:

- Índice de Sucesso de Recorrência
- Taxa de Falha
- Processamento de Seguros / MOB
- Taxa de Reprocessamento
- Cobrança de Seguros Cancelados
- Taxa de Falha no Cancelamento
- Cobranças em Duplicidade
- Parcela Cobrada x Parcela Lançada
- Data de Vencimento x Data de Postagem
- Ausência de Evento de Pagamento
- Conciliação Financeira
- Estornos Indevidos

Não trate a documentação anterior como verdade absoluta.

Ela é a base de hipóteses que agora deve ser confrontada com:

1. código das aplicações;
2. telemetria real disponível no Datadog.

---

# 2. Ler as aplicações envolvidas

Identifique e leia as duas aplicações responsáveis pelos fluxos já levantados:

- gestão de eventos de conta/cartão;
- gestão de eventos de pagamentos.

Localize os projetos reais no workspace pelo nome e pela documentação existente.

Use o código apenas para confirmar:

- endpoints;
- consumers;
- producers;
- listeners;
- schedulers;
- integrações;
- services;
- métodos;
- status funcionais;
- IDs;
- mensagens de log;
- recursos HTTP;
- propagação de identificadores;
- comportamento de retry;
- eventos de sucesso/falha.

Não faça alterações.

Reconstrua somente os fluxos necessários para validar os indicadores.

---

# 3. Ler o guia do servidor de cookies/autenticação

Localize a instalação atual do projeto de sincronização, preferencialmente em estrutura semelhante a:

`%USERPROFILE%\Tools\cookie-sync\gemini-auth-sync\`

Leia antes de usar qualquer credencial:

- `GEMINI_PLATFORM_ACCESS_RUNBOOK.md`
- `README.md`
- demais documentos operacionais relevantes do servidor.

Se o caminho atual estiver diferente, localize o diretório real e siga a documentação existente.

O código/documentação atual do servidor é a fonte de verdade para:

- como localizar a credencial do Datadog;
- como validar freshness;
- como iniciar o servidor quando necessário;
- como validar a sessão;
- como detectar expiração;
- como tratar 401/403;
- como usar os cookies sem expô-los.

Nunca exiba valores reais de credenciais.

---

# 4. Tentar validar acesso ao Datadog

Usando somente o procedimento documentado no runbook:

1. verifique se existe credencial válida para Datadog;
2. valide freshness;
3. valide a sessão com uma chamada somente leitura;
4. confirme se é possível consultar o Datadog autenticado.

Se houver necessidade de sincronização manual/automática, siga apenas o mecanismo já implementado/documentado.

Não tente contornar controles de acesso.

Não altere nada no Datadog.

Registre no relatório apenas:

- acesso confirmado;
- acesso não confirmado;
- sessão expirada;
- bloqueio técnico.

Nunca registre o cookie/token.

---

# 5. Revalidar as fontes reais disponíveis no Datadog

Existe uma hipótese anterior de que:

- os services das aplicações aparecem no APM;
- APM Traces/Spans possuem dados;
- APM Metrics possui alguns dados derivados;
- o Log Explorer pode não estar recebendo logs dessas aplicações.

NÃO assuma que isso continua correto.

Valide no Datadog.

Para cada aplicação, confirme:

- service real;
- environments disponíveis;
- operações;
- resources;
- endpoints;
- spans;
- tags/atributos;
- APM Metrics disponíveis;
- traces disponíveis;
- logs disponíveis ou ausentes;
- período de retenção/consulta relevante, quando visível.

Classifique cada fonte:

- CONFIRMADO NO DATADOG
- CONFIRMADO NO CÓDIGO, NÃO LOCALIZADO NO DATADOG
- HIPÓTESE
- INDISPONÍVEL

---

# 6. Revalidar resources e operações já descobertos

Procure especificamente os recursos já levantados, quando aplicáveis.

Exemplos a validar, não assumir:

## Conta/cartão

- `POST /v1/recorrencias`
- `POST /solicitacao-recorrencia`
- resources/consumers relacionados ao Evento 31
- operações `spring.handle`, `http.client.request`, `servlet.request`, `camel.exchange` ou equivalentes realmente observados.

## Pagamentos/débito

- `POST /v1/schedule-management/schedule`
- consumers/listeners de retorno SGAT/SPAG
- resources de processamento/agendamento.

Para cada resource realmente encontrado, registre:

- service;
- operation;
- resource_name;
- method;
- status code quando relevante;
- tags úteis;
- relação com o fluxo de negócio;
- se é entrada, processamento intermediário, saída ou sucesso técnico.

---

# 7. Tirar a prova dos gaps já identificados

Um dos principais gaps anteriores foi o identificador de proposta, como `cdPropostaSeguro`, que poderia aparecer no código/log, mas não estava confirmado como atributo pesquisável no Datadog.

Agora, com acesso ao Datadog, tente confirmar de fato.

Procure por atributos/tags equivalentes a:

- `cdPropostaSeguro`
- `proposta`
- `proposta_id`
- `numeroProposta`
- `mdc.numeroProposta`
- `nuProposta`
- `codigoProposta`
- `recurrencyId`
- `cdRecorrenciaPagamento`
- `uidSchedule`
- `uidRecurrence`
- `correlationId`
- `requestId`
- outros IDs reais encontrados no código.

Para cada identificador responda:

- existe no código?
- aparece no Datadog?
- aparece em span?
- aparece em trace?
- aparece em log?
- é tag/facet pesquisável?
- o valor é estável ao longo do fluxo?
- pode ser usado em group by?
- pode ser usado em cardinality/count unique?
- permite correlacionar entrada e resultado?

Se o valor existir somente no payload/mensagem, mas não como atributo estruturado, documente isso como gap de instrumentação.

---

# 8. Revalidar o Índice de Sucesso de Recorrência

Este é o indicador prioritário.

Reavalie com dados reais do Datadog:

## Cartão

Descubra qual sinal existente representa melhor:

1. recorrência iniciada;
2. recorrência enviada;
3. recorrência processada;
4. sucesso funcional;
5. falha funcional;
6. falha técnica.

## Débito

Faça a mesma análise:

1. início/agendamento;
2. envio ao sistema externo;
3. processamento;
4. sucesso funcional;
5. falha funcional;
6. falha técnica;
7. esgotamento de retries, se observável.

Não transforme HTTP 2xx automaticamente em sucesso de negócio.

Diferencie:

- sucesso técnico da chamada;
- sucesso funcional da recorrência.

Verifique se o Datadog contém sinal suficiente para essa distinção.

---

# 9. Reavaliar se o dashboard deve usar Logs, APM Spans ou APM Metrics

Para cada indicador, escolha a fonte mais adequada baseada no que realmente estiver disponível.

Considere:

## APM Metrics

Usar quando houver métricas confiáveis como:

- requests/hits;
- errors;
- error rate;
- latency;

filtradas corretamente por:

- service;
- operation;
- resource;
- tags.

Não tratar requests técnicos automaticamente como recorrências de negócio.

## APM Spans

Usar quando for necessário:

- filtrar resource específico;
- usar tags/atributos do span;
- separar operações;
- pesquisar IDs;
- fazer agregações ou cardinalidade suportadas pela interface.

## Logs

Usar apenas se os logs estiverem de fato disponíveis/indexados no Datadog.

Se Log Explorer não possuir os logs dessas aplicações, remover das recomendações qualquer query que dependa de logs como se fosse executável hoje.

## Métricas customizadas

Se não existirem hoje, não inventar.

Podem ser documentadas como evolução futura.

---

# 10. Construir uma matriz Indicador x Fonte real

Atualize a documentação com uma tabela semelhante a:

| Indicador | Fonte atual | Service | Operation | Resource | Tags/IDs | Query possível hoje | Confiabilidade | Gap |
|-----------|-------------|---------|-----------|----------|----------|---------------------|---------------|-----|

Classifique a confiabilidade:

- ALTA
- MÉDIA
- BAIXA
- NÃO IMPLEMENTÁVEL COM CONFIANÇA HOJE

Explique o motivo.

---

# 11. Priorizar um dashboard MVP que possa ser apresentado agora

Existe necessidade de apresentar progresso da atividade.

Portanto, para cada conjunto de indicadores, separe:

## MVP IMPLEMENTÁVEL AGORA

Widgets que podem ser montados com a telemetria atual, mesmo que representem uma visão técnica/operacional preliminar.

Os nomes dos widgets devem ser honestos.

Exemplo:

Se a fonte é `requests` do resource `POST /v1/recorrencias`, não chamar automaticamente de "Propostas únicas processadas".

Prefira um nome como:

- Chamadas de Recorrência
- Requests de Recorrência
- Volume técnico do endpoint de Recorrência

até existir evidência suficiente para um nome funcional.

## VERSÃO ALVO

Widgets que fariam sentido após:

- instrumentação de ID de proposta;
- span tags adicionais;
- log estruturado;
- métrica customizada;
- correlação entre sistemas;
- outra melhoria necessária.

O objetivo é permitir apresentar:

1. o que já está pronto/viável;
2. o que ainda precisa ser evoluído;
3. por que a evolução é necessária.

---

# 12. Propor dashboards que façam sentido para a história Darwin

Com base nos indicadores esperados na atividade, reavalie quais dashboards são realmente úteis.

Não crie um widget por obrigação.

Agrupe por contexto funcional quando fizer sentido.

Exemplo conceitual, somente se validado:

## Dashboard 1 — Recorrência

- volume de entrada;
- volume de processamento;
- sucessos;
- falhas;
- taxa de sucesso;
- evolução temporal;
- cartão x débito.

## Dashboard 2 — Cancelamento

- solicitações de cancelamento;
- cancelamentos processados;
- falhas;
- recorrências ainda ativas após cancelamento;
- cobranças após cancelamento.

## Dashboard 3 — Postagem/Cobrança

- postagens;
- duplicidades;
- parcela cobrada x lançada;
- divergências;
- data esperada x data de postagem.

## Dashboard 4 — Pagamento/Conciliação

- eventos de pagamento;
- ausência de evento;
- conciliação;
- estornos indevidos.

Essa estrutura é apenas uma hipótese.

A documentação final deve refletir o que o código e o Datadog sustentam.

---

# 13. Para cada widget, documentar exatamente como montar no Datadog

Para todo widget recomendado como MVP, informe:

- Dashboard/seção
- Nome do widget
- Objetivo
- Tipo de widget:
  - Query Value
  - Timeseries
  - Table
  - Top List
  - outro, se necessário
- Data source:
  - APM Metrics
  - APM Spans
  - Logs
  - Metrics
- Request:
  - Requests
  - Errors
  - Error Rate
  - Latency
  - outro realmente disponível
- Service
- Environment, se necessário
- Operation
- Resource
- Filter by tags
- Group by
- Aggregation
- Formula
- Unidade
- Janela temporal recomendada
- Query/filtro exato quando aplicável
- Evidência no Datadog
- Evidência no código
- Limitação
- Status:
  - PRONTO PARA MONTAR
  - MVP PRELIMINAR
  - DEPENDE DE GAP
  - NÃO RECOMENDADO

Escreva de maneira que um desenvolvedor consiga abrir o Datadog e reproduzir o widget seguindo o documento.

---

# 14. Query Value e Timeseries

Quando fizer sentido, forneça instruções concretas de interface.

Exemplo conceitual:

Data source:
APM Metrics

Request:
Requests

Service:
<service real>

Operation:
<operation real>

Resource:
<resource real>

Filter by:
<tag real>

Group by:
<campo real>

Não use placeholders no documento final quando o valor tiver sido confirmado no Datadog.

Se não tiver sido confirmado, marque explicitamente:

`VALIDAR NO DATADOG`

---

# 15. Índice de Sucesso: distinguir versão preliminar da versão definitiva

Se hoje for possível apenas medir:

- quantidade de requests;
- spans;
- erros técnicos;

documente um indicador preliminar com nome tecnicamente correto.

Não apresente isso como "Índice de Sucesso de Recorrência" definitivo se não houver como identificar sucesso de negócio.

Para o índice definitivo, tente chegar a algo equivalente a:

`sucessos funcionais únicos / recorrências únicas iniciadas * 100`

Mas somente recomende essa fórmula como executável se houver:

- unidade de negócio identificável;
- ID confiável;
- sinal funcional de sucesso;
- proteção contra retry/redelivery/duplicidade.

Caso contrário, documente exatamente o gap necessário.

---

# 16. Validar retries e duplicidade

Para fluxos assíncronos, Pub/Sub, consumers ou chamadas com retry:

- verificar risco de múltiplos spans para a mesma proposta;
- verificar redelivery;
- verificar novas tentativas;
- verificar se request count superestima recorrências;
- verificar se cardinality por ID resolve o problema.

Se não houver ID disponível como tag/facet, isso deve aparecer explicitamente na documentação como necessidade de instrumentação.

---

# 17. Produzir documentação final atualizada

Crie ou atualize a pasta:

`darwin-monitor/dashboards/`

Preserve o histórico útil da documentação anterior, mas corrija hipóteses que tenham sido refutadas pelo Datadog.

Crie/atualize pelo menos:

## `darwin-monitor/dashboards/README.md`

Índice dos dashboards e estado atual da solução.

## `darwin-monitor/dashboards/01-indice-sucesso-recorrencia.md`

Revalidar completamente com dados reais do Datadog.

## `darwin-monitor/dashboards/02-dashboard-mvp.md`

Documento curto e objetivo contendo somente o que pode ser montado agora para demonstrar progresso.

Deve separar:

- cartão;
- débito;
- widgets disponíveis agora;
- limitações conhecidas.

## `darwin-monitor/dashboards/03-dashboard-alvo.md`

Visão do dashboard ideal para atender a história completa, indicando dependências de instrumentação.

## `darwin-monitor/dashboards/04-gaps-de-instrumentacao.md`

Consolidar somente os gaps que bloqueiam ou reduzem a confiabilidade dos dashboards.

Para cada gap informe:

- indicador afetado;
- dado ausente;
- onde o dado existe hoje;
- por que não é utilizável no Datadog;
- melhoria necessária;
- benefício esperado.

Não implemente a melhoria.

---

# 18. Atualizar documentos antigos relacionados

Se arquivos anteriores em:

- `darwin-monitor/indicadores/`
- `darwin-monitor/fontes/`
- `darwin-monitor/gaps-e-proximos-passos.md`

contiverem afirmações que a consulta real ao Datadog comprovou ou refutou, atualize essas afirmações.

Não reescreva tudo desnecessariamente.

Inclua evidência clara de que o ponto foi:

- confirmado no código;
- confirmado no Datadog;
- não localizado no Datadog;
- ainda pendente.

---

# 19. Evidências obrigatórias

Toda recomendação de widget deve possuir evidência.

Sempre que possível documente:

## Evidência no código

`arquivo -> classe -> método -> elemento`

## Evidência no Datadog

`service -> operation -> resource -> tag/atributo -> comportamento observado`

Não inclua trace IDs, proposal IDs ou dados reais desnecessários no relatório.

Se precisar usar exemplos, masque os valores.

---

# 20. Segurança

Nunca documente:

- cookies;
- tokens;
- headers de autenticação;
- senhas;
- secrets;
- valores reais de sessão;
- dados pessoais;
- propostas reais;
- CPFs;
- payloads sensíveis.

Se encontrar dado sensível no Datadog, use apenas o nome do atributo e descreva seu papel.

---

# 21. Resultado esperado

Ao terminar, quero ter uma documentação que responda claramente:

1. Quais dashboards fazem sentido para a história Darwin?
2. Quais widgets consigo montar hoje?
3. Quais são apenas MVP técnico/preliminar?
4. Quais indicadores já têm telemetria suficiente?
5. Quais dependem de instrumentação?
6. O Log Explorer realmente possui ou não os dados necessários?
7. APM Spans ou APM Metrics são hoje a melhor fonte?
8. Quais services, operations e resources devo selecionar na interface?
9. Quais tags/IDs estão realmente disponíveis?
10. Existe hoje um ID de proposta utilizável para cardinality/count unique?
11. Como evitar dupla contagem por retry/redelivery?
12. O que posso apresentar imediatamente como progresso da atividade?
13. O que precisa ser feito para chegar ao dashboard executivo definitivo?

---

# 22. Resumo final no terminal

Depois de gravar a documentação, responda no terminal apenas com um resumo curto:

1. acesso ao Datadog: confirmado ou não;
2. fontes confirmadas: APM Metrics / APM Spans / Logs;
3. quantidade de widgets PRONTOS PARA MONTAR;
4. quantidade de widgets MVP PRELIMINAR;
5. principais gaps;
6. se o código de proposta/ID equivalente foi confirmado no Datadog;
7. primeiro dashboard recomendado para montar;
8. caminho dos arquivos criados/atualizados.

Não crie dashboards nem monitors no Datadog nesta etapa.
