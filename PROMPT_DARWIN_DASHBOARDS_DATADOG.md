# Prompt — Darwin Monitor: validação OBRIGATÓRIA no Datadog real + documentação de dashboards

Atue como engenheiro de software sênior especializado em Java/Spring Boot, observabilidade, Datadog APM, distributed tracing, métricas, logs e análise de fluxos de negócio.

## REGRA PRINCIPAL DESTA TAREFA

**NÃO produza a documentação final de dashboards apenas a partir das documentações existentes ou do código.**

Esta tarefa só é considerada executada corretamente se você:

1. ler o runbook do servidor de cookies/autenticação;
2. usar a sessão/cookies atuais para acessar o **Datadog real** em modo somente leitura;
3. executar consultas reais no Datadog para confirmar services, operations, resources, spans, tags/atributos, métricas e disponibilidade de logs;
4. confrontar essas evidências reais com o código e com a documentação já existente em `darwin-monitor/`;
5. somente depois atualizar a documentação de dashboards.

As documentações existentes são **hipóteses e contexto**, não fonte de verdade suficiente para esta etapa.

A fonte de verdade desta etapa deve ser a combinação:

**DATADOG REAL + CÓDIGO + DOCS EXISTENTES**

Se você não conseguir acessar o Datadog real, **PARE a tarefa** e informe o bloqueio.  
Não continue produzindo uma nova versão baseada somente em análise estática.

---

# 1. Escopo e restrições

Esta etapa é exclusivamente de **análise e documentação de dashboards**.

NÃO:

- crie, altere ou exclua dashboards no Datadog;
- crie monitors;
- crie alertas;
- configure Teams;
- altere código;
- altere telemetria;
- altere instrumentação;
- altere configurações do Datadog;
- exponha cookies, tokens, headers de autenticação ou credenciais.

O acesso ao Datadog deve ser somente leitura.

Todo resultado deve ser gravado dentro de:

`darwin-monitor/`

---

# 2. ETAPA ZERO OBRIGATÓRIA — preparar acesso ao Datadog real

Antes de analisar indicadores ou editar documentação, localize o projeto atual de sincronização de cookies/autenticação, preferencialmente em estrutura semelhante a:

`%USERPROFILE%\Tools\cookie-sync\gemini-auth-sync\`

Leia obrigatoriamente:

- `GEMINI_PLATFORM_ACCESS_RUNBOOK.md`
- `README.md`
- demais documentos operacionais relevantes;
- o código do servidor somente quando necessário para compreender o procedimento documentado.

Siga o runbook para:

1. localizar a credencial atual do Datadog;
2. validar `metadata.json` e freshness;
3. iniciar o servidor local, se necessário;
4. validar a sessão autenticada no Datadog com uma chamada somente leitura;
5. confirmar que a sessão realmente permite consultar APM/Traces/Spans/Metrics/Logs.

Nunca imprima os valores das credenciais.

## GATE obrigatório

Antes de prosseguir, você deve conseguir registrar:

`DATADOG_REAL_ACCESS=CONFIRMADO`

Isso deve significar que pelo menos uma consulta real autenticada ao Datadog retornou dados do ambiente do usuário.

Se não conseguir:

- não reescreva os documentos de dashboard;
- não repita conclusões antigas;
- não produza queries especulativas;
- pare e informe exatamente o bloqueio técnico encontrado.

---

# 3. Prova mínima obrigatória de acesso real

Antes de usar qualquer conclusão anterior, faça consultas reais no Datadog e confirme pelo menos:

1. existência do service de gestão de eventos conta/cartão;
2. existência do service de gestão de eventos pagamentos;
3. pelo menos um resource real de recorrência no APM/Spans;
4. operations associadas a esse resource;
5. se APM Metrics possui dados para esse fluxo;
6. se APM Spans possui dados para esse fluxo;
7. se Log Explorer possui ou não logs dessas aplicações;
8. pelo menos algumas tags/atributos reais observáveis em um span do fluxo.

Você já possui como candidatos de investigação, mas deve confirmar no Datadog, não assumir:

- `POST /v1/recorrencias`
- `POST /solicitacao-recorrencia`
- `POST /v1/schedule-management/schedule`
- resources/consumers do Evento 31;
- consumers/listeners de retorno SGAT/SPAG;
- operations como `spring.handle`, `http.client.request`, `servlet.request`, `camel.exchange`.

Se algum candidato não existir no Datadog, marque como refutado ou não localizado.

---

# 4. Somente depois, ler e confrontar a documentação Darwin

Após confirmar acesso ao Datadog real, leia:

`darwin-monitor/`

Incluindo, quando existirem:

- `README.md`
- `01-arquitetura-e-fontes.md`
- `gaps-e-proximos-passos.md`
- `indicadores/`
- `fontes/`
- `dashboards/`

A documentação anterior deve ser tratada como:

- descoberta anterior;
- hipótese;
- evidência de código;
- possível direção de investigação.

Para cada afirmação relevante, classifique-a após consultar o Datadog:

- CONFIRMADO NO DATADOG
- CONFIRMADO NO CÓDIGO, MAS NÃO NO DATADOG
- REFUTADO PELO DATADOG
- AINDA NÃO COMPROVADO

Não copie conclusões antigas sem essa revalidação.

---

# 5. Ler o código das duas aplicações

Localize no workspace as aplicações de:

- gestão de eventos conta/cartão;
- gestão de eventos pagamentos.

Use o código para explicar e complementar o que foi observado no Datadog.

Confirme:

- endpoints;
- consumers;
- producers;
- listeners;
- schedulers;
- integrações;
- métodos;
- status funcionais;
- IDs;
- mensagens de log;
- propagação de identificadores;
- retries/redelivery;
- sinais de sucesso e falha.

O código não substitui a validação no Datadog.

---

# 6. Inventário REAL de telemetria

Para cada uma das duas aplicações, produza um inventário baseado em observação real:

| Item | Valor observado no Datadog | Evidência/consulta | Status |
|------|-----------------------------|-------------------|--------|
| Service | ... | ... | CONFIRMADO |
| Environment | ... | ... | CONFIRMADO |
| Operation | ... | ... | CONFIRMADO |
| Resource | ... | ... | CONFIRMADO |
| Span tags | ... | ... | CONFIRMADO |
| APM Metrics | ... | ... | DISPONÍVEL/INDISPONÍVEL |
| APM Spans | ... | ... | DISPONÍVEL/INDISPONÍVEL |
| Logs | ... | ... | DISPONÍVEL/INDISPONÍVEL |

Não registre cookies, trace IDs sensíveis, proposal IDs reais ou payloads pessoais.

---

# 7. Tirar a prova dos gaps de IDs

Um ponto crítico anterior foi a possível existência de identificadores de proposta/recorrência no código, mas não necessariamente como atributos pesquisáveis no Datadog.

Agora comprove isso no Datadog real.

Procure, inclusive com variações já vistas anteriormente:

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
- outros IDs encontrados no código.

Para cada ID, informe com base em observação real:

- aparece no Datadog?
- em que fonte: span, trace, log ou metric tag?
- qual nome exato possui?
- é pesquisável?
- pode ser usado em filtro?
- pode ser usado em group by?
- pode ser usado para cardinality/count unique?
- aparece nos dois lados do fluxo?
- ajuda a evitar dupla contagem?

Se existir no código mas não no Datadog, classifique como gap de instrumentação.

---

# 8. Revalidar o Índice de Sucesso de Recorrência com dados reais

Este é o indicador prioritário.

## Cartão

Tente identificar no Datadog real sinais para:

1. entrada/início;
2. envio;
3. processamento;
4. sucesso técnico;
5. sucesso funcional;
6. falha técnica;
7. falha funcional.

## Débito

Faça o mesmo para:

1. início/agendamento;
2. envio;
3. processamento;
4. sucesso técnico;
5. sucesso funcional;
6. falha técnica;
7. falha funcional;
8. esgotamento de retries, se observável.

Não considere automaticamente HTTP 2xx como sucesso funcional.

Para cada sinal, registre:

- source;
- service;
- operation;
- resource;
- tags/atributos;
- agregação possível;
- risco de duplicidade;
- confiança.

---

# 9. Decidir a fonte do dashboard com base no Datadog real

Não escolha Logs, APM Metrics ou APM Spans por preferência teórica.

Escolha pelo que realmente estiver disponível.

## APM Metrics

Use somente quando os dados reais permitirem uma métrica defensável.

Exemplos possíveis:

- Requests
- Errors
- Error Rate
- Latency

Filtrando por service/operation/resource reais.

Não chame requests técnicos de "propostas" se isso não estiver comprovado.

## APM Spans

Use quando precisar:

- resource específico;
- tags/atributos do span;
- cardinalidade;
- filtros funcionais/técnicos;
- investigar IDs.

## Logs

Use somente se o Log Explorer realmente retornar dados das aplicações.

Se o Log Explorer estiver vazio para esses services, registre isso como descoberta real e remova queries de log da versão executável do dashboard.

## Métricas customizadas

Somente documente como existentes se forem observadas de fato.

Caso contrário, ficam como recomendação futura.

---

# 10. Queries reais, não especulativas

Toda query ou configuração recomendada como "PRONTO PARA MONTAR" deve ter sido testada contra o Datadog real nesta execução.

Para cada widget registre:

- consulta/configuração testada;
- se retornou dados;
- período usado no teste;
- source;
- service;
- operation;
- resource;
- filtros;
- group by;
- agregação;
- resultado qualitativo observado.

Não é necessário documentar números de produção exatos.

Pode registrar:

- retornou dados;
- retornou zero;
- não disponível;
- campo não pesquisável;
- facet ausente.

Uma query não testada deve ser classificada como:

`HIPÓTESE — NÃO VALIDADA NO DATADOG`

e não pode entrar na lista de widgets prontos.

---

# 11. Dashboard MVP para demonstrar progresso

Existe necessidade de apresentar algo funcional agora.

Construa uma proposta de MVP baseada somente no que o Datadog real permite hoje.

Separe claramente:

## PRONTO PARA MONTAR AGORA

Widgets sustentados por query/configuração testada no Datadog.

## MVP PRELIMINAR

Widgets tecnicamente úteis, mas que medem volume técnico e não necessariamente o indicador funcional definitivo.

## BLOQUEADO POR GAP

Widgets que exigem:

- tag adicional;
- ID de proposta;
- métrica customizada;
- log estruturado;
- correlação;
- instrumentação.

Se, por exemplo, hoje o Datadog permite contar Requests de `POST /v1/recorrencias`, o nome do widget deve ser algo como:

`Chamadas de Recorrência`

e NÃO:

`Propostas Processadas`

a menos que essa equivalência tenha sido comprovada.

---

# 12. Reavaliar os dashboards da história Darwin

Com base em DATADOG REAL + código + documentação, determine quais agrupamentos fazem sentido.

Indicadores esperados incluem:

## Recorrência

- Índice de Sucesso
- Taxa de Falha
- Processamento de Seguros / MOB
- Taxa de Reprocessamento

## Cancelamento

- Cobrança de Seguros Cancelados
- Taxa de Falha no Cancelamento

## Postagem/Cobrança

- Cobranças em Duplicidade
- Parcela Cobrada x Parcela Lançada
- Data de Vencimento x Data de Postagem

## Pagamento/Conciliação

- Ausência de Evento de Pagamento
- Conciliação Financeira
- Estornos Indevidos

Não force um widget quando a telemetria não sustentar o indicador.

---

# 13. Documentar exatamente como montar cada widget

Para cada widget recomendado informe:

- dashboard/seção;
- nome;
- objetivo;
- tipo de widget;
- data source;
- Request, se APM Metrics;
- service;
- environment;
- operation;
- resource;
- filter by tags;
- group by;
- aggregation;
- formula;
- unidade;
- janela temporal;
- configuração/query exata;
- resultado da validação no Datadog real;
- evidência no código;
- limitação;
- status.

Status permitidos:

- PRONTO PARA MONTAR — TESTADO NO DATADOG
- MVP PRELIMINAR — TESTADO NO DATADOG
- DEPENDE DE GAP
- HIPÓTESE — NÃO VALIDADA
- NÃO RECOMENDADO

---

# 14. Índice de Sucesso: preliminar x definitivo

O índice definitivo deve, idealmente, representar:

`sucessos funcionais únicos / recorrências únicas iniciadas * 100`

Só classifique isso como implementável agora se tiver comprovado no Datadog:

- unidade de negócio;
- ID confiável;
- sinal funcional de sucesso;
- sinal de entrada;
- possibilidade de evitar retry/redelivery/duplicidade.

Caso o Datadog atual só permita:

- request count;
- span count;
- HTTP errors;

documente uma visão técnica preliminar com nome correto e explique por que ainda não equivale ao índice funcional definitivo.

---

# 15. Evidência real obrigatória por widget

Cada widget PRONTO ou MVP PRELIMINAR deve conter duas evidências:

## Evidência Datadog

Formato:

`service -> operation -> resource -> tag/atributo -> comportamento observado`

## Evidência código

Formato:

`arquivo -> classe -> método -> papel no fluxo`

Sem evidência Datadog real, o widget não pode ser classificado como pronto.

---

# 16. Documentação final

Atualize/crie:

`darwin-monitor/dashboards/README.md`

`darwin-monitor/dashboards/01-indice-sucesso-recorrencia.md`

`darwin-monitor/dashboards/02-dashboard-mvp.md`

`darwin-monitor/dashboards/03-dashboard-alvo.md`

`darwin-monitor/dashboards/04-gaps-de-instrumentacao.md`

Além disso, crie:

`darwin-monitor/dashboards/05-evidencias-datadog.md`

Este último arquivo deve registrar, sem dados sensíveis:

- data source consultada;
- service;
- operation;
- resource;
- atributos/tags observados;
- consultas/configurações testadas;
- se retornaram dados;
- conclusões que confirmaram/refutaram a documentação anterior.

Esse arquivo serve para impedir que futuras análises voltem a tratar hipóteses antigas como fatos.

---

# 17. Corrigir documentos antigos

Se a consulta real ao Datadog refutar ou confirmar informações presentes em:

- `darwin-monitor/indicadores/`
- `darwin-monitor/fontes/`
- `darwin-monitor/gaps-e-proximos-passos.md`

atualize apenas os pontos afetados.

Marque claramente:

- CONFIRMADO NO DATADOG
- NÃO LOCALIZADO NO DATADOG
- REFUTADO
- PENDENTE

---

# 18. Segurança

Nunca grave ou mostre:

- cookies;
- tokens;
- headers;
- secrets;
- proposal IDs reais;
- CPFs;
- payloads sensíveis;
- valores de sessão.

Use somente nomes de campos e exemplos mascarados quando necessário.

---

# 19. Critério de conclusão

A tarefa só pode ser considerada concluída se:

- `DATADOG_REAL_ACCESS=CONFIRMADO`;
- houver evidência de consultas reais ao APM/Spans/Metrics/Logs;
- pelo menos os fluxos de recorrência de cartão e débito tiverem sido confrontados com o Datadog;
- as recomendações de widgets distinguirem o que foi testado do que ainda é hipótese;
- a documentação anterior tiver sido revalidada, e não simplesmente repetida.

Se qualquer um desses itens falhar, não declare a tarefa concluída.

---

# 20. Resumo final no terminal

Ao final, responda somente:

1. `DATADOG_REAL_ACCESS=CONFIRMADO` ou o bloqueio;
2. services reais confirmados;
3. sources reais confirmadas: APM Metrics / APM Spans / Logs;
4. resources reais confirmados;
5. IDs/tags confirmados;
6. quantidade de widgets PRONTO PARA MONTAR — TESTADO NO DATADOG;
7. quantidade de widgets MVP PRELIMINAR — TESTADO NO DATADOG;
8. principais gaps;
9. primeiro dashboard recomendado;
10. arquivos criados/atualizados.

**Não crie dashboards nem monitors nesta etapa.**
