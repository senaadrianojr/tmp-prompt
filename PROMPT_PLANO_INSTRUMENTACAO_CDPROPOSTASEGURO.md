# Prompt — Planejar instrumentação de cdPropostaSeguro para observabilidade

Atue como engenheiro de software sênior especializado em Java/Spring Boot, observabilidade, Datadog APM, distributed tracing, logs estruturados, MDC e desenho de telemetria.

## Objetivo

Criar um **plano técnico de instrumentação** para tornar o identificador de negócio `cdPropostaSeguro` observável e reutilizável nos fluxos do Darwin Monitor.

O foco é identificar **onde o `cdPropostaSeguro` já existe no fluxo da aplicação, onde ele se perde e em quais pontos faz sentido expô-lo como atributo estruturado de observabilidade**.

## Requisito técnico alvo

O plano deve avaliar como objetivo preferencial tornar o `cdPropostaSeguro` **recuperável e pesquisável no Datadog**, priorizando uma solução nativa na origem da telemetria.

A direção técnica a ser validada no código/projeto é:

1. **Span/APM:** expor `cdPropostaSeguro` como tag/attribute estruturado no span relevante e, quando a configuração do Datadog exigir, indicar a necessidade de torná-lo pesquisável/facetado/indexado para uso em filtros, investigação e cardinality/count unique.
2. **Logs estruturados:** nos pontos estratégicos em que já existe logging útil, enriquecer o evento com `cdPropostaSeguro` como campo estruturado, evitando depender do texto livre da mensagem.
3. **Micrometer/métricas:** quando o indicador exigir métrica de negócio, avaliar uma métrica Micrometer com **cardinalidade controlada**. O valor bruto de `cdPropostaSeguro` NÃO deve ser usado como tag de métrica de alta cardinalidade. Use dimensões controladas como status, fluxo, meio de pagamento ou resultado.
4. **Grok:** considerar Grok/regex no Datadog **somente como última alternativa**, caso não seja possível intervir nas aplicações envolvidas ou expor o campo de forma estruturada na origem.

A solução preferencial deve ser a menor intervenção possível que torne o dado observável com segurança.

**Não proponha mudança de código por conveniência.** Só recomende alterações quando forem estritamente necessárias para melhorar a observabilidade nos pontos estratégicos do fluxo. Não altere regra de negócio, contrato, DTO, payload ou arquitetura apenas para facilitar dashboard/monitor.

Esta etapa é somente de análise e planejamento.

## Regra principal

**NÃO implemente nenhuma alteração.**

Não:

- edite código Java;
- altere configuração de logging;
- altere `logback.xml`, `logback-spring.xml` ou equivalentes;
- adicione dependências;
- crie classes;
- altere DTOs;
- altere contratos de APIs;
- altere payloads;
- configure Datadog;
- crie pipelines;
- crie Grok parsers;
- crie dashboards;
- crie monitors;
- faça commits de implementação.

Você está autorizado apenas a:

1. investigar o código;
2. ler a documentação existente;
3. localizar os pontos do fluxo;
4. propor a solução;
5. criar um documento Markdown com o plano.

---

# 1. Ler primeiro a documentação Darwin

Leia o conteúdo existente em:

`darwin-monitor/`

Dê prioridade a:

- `discovery/`
- `discovery/services/`
- `indicadores/`
- `fontes/`
- `dashboards/`
- documentos de gaps de instrumentação.

Procure especialmente referências a:

- `cdPropostaSeguro`;
- proposta;
- recorrência;
- Evento 31;
- cartão;
- débito;
- pagamento;
- retries;
- duplicidade;
- cardinality/count unique;
- APM Spans;
- ausência de tags/atributos no Datadog.

A documentação deve servir como contexto, mas confirme no código tudo que for necessário para o plano.

---

# 2. Identificar aplicações e fluxos envolvidos

Localize as aplicações relacionadas ao Darwin Monitor, principalmente:

- gestão de eventos conta/cartão;
- gestão de eventos pagamentos.

Se houver outras aplicações que participem diretamente do fluxo e carreguem `cdPropostaSeguro`, inclua-as somente quando houver evidência.

Reconstrua os fluxos relevantes de recorrência:

## Cartão

Identifique:

- entrada do fluxo;
- validações;
- envio ao motor externo;
- retorno;
- persistência;
- geração de Evento 31;
- sucesso;
- falha;
- retries, quando existirem;
- integrações posteriores relacionadas à recorrência.

## Débito

Identifique:

- entrada/agendamento;
- envio SGAT/SPAG;
- retorno;
- criação/persistência da recorrência;
- Evento 31;
- sucesso;
- falha;
- retry;
- regularização, quando aplicável.

---

# 3. Mapear o ciclo de vida do cdPropostaSeguro

Faça uma investigação específica do campo `cdPropostaSeguro`.

Considere também aliases ou nomes equivalentes encontrados no código, por exemplo:

- `cdPropostaSeguroByRequest`;
- `codigoPropostaSeguro`;
- `numeroProposta`;
- outros nomes comprovadamente equivalentes.

Para cada ponto do fluxo, documente:

| Etapa | Arquivo | Classe | Método | Nome do campo | Origem | Propagado para | Hoje está em log/span? |
|------|---------|--------|--------|---------------|--------|---------------|------------------------|

Determine:

- onde o ID nasce;
- onde ele é recebido;
- onde ele é transformado;
- onde ele é passado adiante;
- onde é persistido;
- onde deixa de estar disponível;
- onde existe apenas dentro de mensagem textual;
- onde já existe em objeto/DTO mas não é exposto à observabilidade.

Não assuma que todos os campos chamados "proposta" representam o mesmo ID.

---

# 4. Localizar os pontos onde o ID deve ser observável

O objetivo não é "logar em todos os métodos".

Identifique os **pontos mínimos e estratégicos** que permitam acompanhar uma proposta de ponta a ponta.

Considere, quando fizer sentido:

1. entrada do fluxo;
2. chamada para sistema externo;
3. retorno da integração;
4. sucesso funcional;
5. falha funcional;
6. exceção técnica;
7. retry/redelivery;
8. persistência importante;
9. geração de evento;
10. transição para outra aplicação/service.

Para cada ponto candidato responda:

- por que esse ponto é importante;
- o `cdPropostaSeguro` está disponível ali?
- já existe log nesse ponto?
- já existe span?
- é melhor enriquecer algo existente ou criar nova instrumentação?
- qual benefício isso traz para investigação/dashboard/monitor futuro?

Priorize **enriquecer pontos já existentes** em vez de criar logs redundantes.

Antes de sugerir qualquer alteração, aplique esta ordem de decisão:

1. o dado já existe estruturado no span/log e só falta utilizá-lo corretamente no Datadog;
2. o dado já está disponível no ponto de execução e pode enriquecer um span/log existente com alteração mínima;
3. é necessário adicionar um único ponto estratégico de observabilidade;
4. somente depois considerar instrumentação adicional;
5. Grok/regex apenas se não houver possibilidade real de intervenção nas aplicações envolvidas.

Não crie novos logs apenas para repetir informação já presente em um span ou log estruturado equivalente.

---

# 5. Avaliar log estruturado

Analise como o logging da aplicação funciona hoje.

Verifique:

- SLF4J/Logback;
- MDC;
- JSON encoder;
- appenders;
- layout;
- correlation IDs existentes;
- campos já enviados ao Datadog;
- padrões de logging estruturado já usados no projeto.

Determine se a aplicação já possui infraestrutura para algo equivalente a:

`cdPropostaSeguro=<valor>`

como atributo estruturado.

Avalie opções como:

- MDC;
- structured arguments;
- JSON fields;
- mecanismos já padronizados no projeto.

Não escolha uma tecnologia nova se já existir um padrão corporativo no código.

## Importante

O objetivo não é apenas produzir uma mensagem como:

`Proposta: 123456`

O plano deve priorizar que o Datadog receba algo equivalente a um atributo estruturado:

`@cdPropostaSeguro`

ou o nome padronizado que já exista na aplicação.

Quando esse campo estiver em logs, indique se ele precisa ser transformado em atributo/facet pesquisável no Datadog para permitir filtros e cardinalidade.

Não proponha Grok aqui como solução principal. Se o campo puder ser emitido estruturado pela aplicação com uma alteração pequena e segura, essa é a opção preferencial.

---

# 6. Avaliar enriquecimento de spans/APM

Como o APM já é uma fonte importante do Darwin Monitor, analise também como expor o ID nos spans.

Procure:

- Datadog tracer;
- OpenTelemetry;
- Micrometer Tracing;
- Spring instrumentation;
- interceptors;
- filters;
- decorators;
- annotations;
- APIs para adicionar tags/attributes ao span ativo.

Determine se é viável planejar algo equivalente a:

`cdPropostaSeguro=<valor>`

no span relevante.

A recomendação preferencial deve ser que o campo fique disponível como **span tag/attribute pesquisável no Datadog**. Quando necessário, explique também o passo operacional futuro de criar facet/indexação correspondente, sem executar essa configuração agora.

Para cada ponto candidato, informe:

- span/resource relacionado;
- onde o ID está disponível;
- como conceitualmente seria adicionado;
- impacto de propagação;
- se precisa ser colocado somente no span principal ou também em spans filhos.

Não implemente.

---

# 7. Separar logs, spans e métricas

O plano deve distinguir claramente as finalidades.

## Logs estruturados

Uso esperado:

- troubleshooting;
- investigação de uma proposta;
- auditoria técnica;
- correlação textual/funcional.

## Span attributes

Uso esperado:

- investigação APM;
- correlação entre operações;
- filtros;
- dashboards baseados em spans;
- cardinality/count unique, se suportado.

## Métricas customizadas / Micrometer

Avalie separadamente se o projeto já usa Micrometer e se existe um ponto adequado para uma métrica de negócio.

O plano deve considerar, quando fizer sentido para dashboards/monitors, uma métrica Micrometer com **cardinalidade controlada**.

**Não use o valor bruto de `cdPropostaSeguro` como tag de métrica.** Esse identificador tende a gerar alta cardinalidade e deve permanecer em spans/logs para correlação e investigação.

A métrica deve representar o evento/resultado de negócio e usar dimensões controladas, como:

- `status:sucesso|falha`;
- `meio_pagamento:cartao|debito`;
- `fluxo:recorrencia`;
- outras dimensões funcionais estáveis.

---

# 7.1. Hierarquia obrigatória de solução e uso de Grok

O plano deve deixar explícita a ordem de preferência técnica:

## Preferência 1 — Span/APM estruturado

Se o `cdPropostaSeguro` estiver disponível no ponto de execução, priorize adicionar/enriquecer a tag/attribute do span relevante, com alteração mínima e localizada.

Objetivo futuro:

- pesquisar a proposta no APM;
- filtrar spans;
- correlacionar operações;
- usar cardinality/count unique quando suportado;
- apoiar dashboard e monitor.

## Preferência 2 — Log estruturado em ponto estratégico

Se já houver log útil no mesmo ponto, preferir enriquecer esse log com o campo estruturado `cdPropostaSeguro`.

Não adicionar dezenas de logs novos. A mudança deve ser mínima e restrita aos eventos necessários para identificar:

- entrada;
- resultado/sucesso;
- falha;
- transição assíncrona relevante.

## Preferência 3 — Métrica Micrometer de negócio com cardinalidade controlada

Quando houver necessidade de indicador agregado confiável para dashboard/monitor, avaliar métrica Micrometer específica.

A métrica pode contabilizar eventos como:

- recorrência iniciada;
- recorrência concluída;
- recorrência com falha;
- retry/reprocessamento.

Tags permitidas devem ser de baixa cardinalidade.

Exemplo conceitual:

`status:sucesso|falha`

`meio_pagamento:cartao|debito`

`fluxo:recorrencia`

Nunca usar `cdPropostaSeguro=<id>` como tag da métrica.

## Última alternativa — Grok/regex no Datadog

Grok deve ser recomendado somente se:

- não houver autorização para alterar as aplicações;
- a aplicação não puder ser publicada/reimplantada no prazo;
- o dado existir apenas em texto de log e não puder ser estruturado na origem;
- houver limitação técnica/legada que impeça span/log estruturado.

Nesse cenário, Grok é um **fallback operacional**, não a arquitetura alvo.

O plano deve explicar:

- qual mensagem seria parseada;
- qual padrão extrairia o valor;
- qual atributo seria criado;
- riscos de quebra se o texto do log mudar;
- necessidade de testes;
- caráter temporário da solução.

Não criar o parser nesta etapa.


---

# 8. Evitar excesso de logs

O plano deve explicitamente avaliar volume e ruído.

Para cada ponto proposto, classifique:

- OBRIGATÓRIO;
- RECOMENDADO;
- OPCIONAL;
- NÃO RECOMENDADO.

Evite sugerir o mesmo ID em dezenas de logs internos sem benefício.

O objetivo é obter rastreabilidade suficiente com baixo ruído.

---

# 9. Contexto assíncrono e propagação

Analise especialmente fluxos que passam por:

- Pub/Sub;
- Kafka;
- consumers;
- producers;
- listeners;
- retries;
- processamento assíncrono.

Determine:

- se o `cdPropostaSeguro` já está no payload;
- se precisa ser copiado para contexto/MDC ao consumir mensagem;
- se o contexto precisa ser limpo após processamento;
- se o ID continua disponível em retries/redelivery;
- como manter correlação entre producer e consumer.

Inclua riscos de vazamento de contexto entre threads quando aplicável.

---

# 10. Segurança e privacidade

Avalie se `cdPropostaSeguro` pode ser tratado como identificador interno de negócio e se há alguma política existente no código/documentação para não expor valores sensíveis.

Não proponha logar:

- CPF;
- dados pessoais;
- payload completo;
- tokens;
- credenciais;
- dados de cartão;
- informações financeiras sensíveis.

O plano deve trabalhar somente com o identificador mínimo necessário.

---

# 11. Impacto nos dashboards Darwin

Explique como a instrumentação proposta melhoraria o Darwin Monitor.

Exemplos de benefícios a validar:

- contar propostas únicas;
- evitar dupla contagem por retry;
- correlacionar entrada e sucesso;
- localizar uma proposta específica;
- calcular Índice de Sucesso mais confiável;
- investigar falhas;
- correlacionar cartão e pagamento;
- criar monitors futuros com melhor precisão.

Não crie dashboards nesta etapa.

---

# 12. Produzir matriz de pontos de instrumentação

Crie uma tabela consolidada:

| Prioridade | Aplicação | Fluxo | Arquivo/Classe | Método | Momento | cdPropostaSeguro disponível? | Solução preferencial | Log estruturado | Span attribute | Micrometer | Grok fallback? | Motivo |
|------------|-----------|-------|----------------|--------|---------|-----------------------------|---------------------|-----------------|----------------|------------|----------------|--------|

Ordene por prioridade.

---

# 13. Propor padrão de nomenclatura

Verifique se já existe convenção para atributos/tags.

Se houver, use-a.

Se não houver, proponha um nome consistente para análise, por exemplo:

`cdPropostaSeguro`

ou:

`business.cdPropostaSeguro`

mas NÃO assuma uma convenção sem verificar o projeto.

Documente:

- nome recomendado;
- motivo;
- onde deve ser consistente;
- impacto em consultas Datadog.

---

# 14. Plano de implementação em etapas

Crie um plano futuro, sem executar, por exemplo:

## Etapa 1 — Instrumentação mínima

- confirmar se o campo já pode ser exposto sem mudança de código;
- enriquecer somente pontos obrigatórios;
- priorizar tag/attribute de span pesquisável e/ou log estruturado;
- permitir busca de uma proposta;
- permitir correlação básica;
- evitar mudanças de negócio/DTO/contrato por conveniência.

## Etapa 2 — Propagação assíncrona

- garantir ID em consumers/producers;
- revisar MDC/contexto.

## Etapa 3 — APM e métricas de negócio

- adicionar/enriquecer atributo no span somente onde necessário;
- validar filtros/facet/indexação no Datadog;
- avaliar Micrometer com cardinalidade controlada para indicadores agregados;
- nunca usar `cdPropostaSeguro` bruto como tag de métrica.

## Etapa 4 — Validação

- gerar tráfego de teste;
- verificar atributo no Datadog;
- validar cardinalidade;
- validar ausência de duplicidade.

## Etapa 5 — Evolução Darwin

- atualizar dashboards;
- recalcular indicadores;
- somente depois planejar monitors.

Adapte as etapas ao código real encontrado.

---

# 15. Critérios de aceite futuros

Defina critérios claros para uma implementação futura.

Exemplos:

- uma proposta pode ser localizada no Datadog pelo ID;
- o ID aparece no início e no resultado do fluxo;
- o ID aparece em spans relevantes;
- retries mantêm o mesmo ID;
- nenhuma credencial/dado sensível é incluído;
- MDC/contexto não vaza entre requisições;
- o dashboard pode contar propostas únicas;
- código antigo continua funcional.

Não execute os testes agora.

---

# 16. Arquivo de saída

Crie, se necessário, a pasta:

`darwin-monitor/planos/`

Crie o arquivo:

`darwin-monitor/planos/01-instrumentacao-cdpropostaseguro.md`

Esse documento deve conter:

1. objetivo;
2. estado atual;
3. fluxo do `cdPropostaSeguro`;
4. pontos onde o campo já existe;
5. pontos onde a observabilidade é insuficiente;
6. matriz de instrumentação;
7. estratégia de logs estruturados;
8. estratégia de spans/APM, incluindo tag/attribute pesquisável `@cdPropostaSeguro` ou equivalente;
9. estratégia de Micrometer/métrica de negócio com cardinalidade controlada;
10. decisão explícita sobre Grok como fallback de última alternativa;
11. contexto assíncrono;
12. riscos;
13. padrão de nomenclatura;
14. plano por etapas;
15. arquivos/classes que futuramente seriam alterados;
16. critérios de aceite;
17. impacto esperado nos dashboards Darwin.

---

# 17. Não modificar documentação de discovery sem necessidade

Use `darwin-monitor/discovery/` como fonte.

Se encontrar uma informação importante que contradiga diretamente o discovery atual, registre a divergência dentro do plano.

Não faça uma reescrita ampla das demais documentações nesta tarefa.

---

# 18. Resultado final no terminal

Ao terminar, apresente apenas:

1. aplicações analisadas;
2. quantidade de pontos OBRIGATÓRIOS;
3. quantidade de pontos RECOMENDADOS;
4. estratégia recomendada para logs;
5. estratégia recomendada para spans;
6. se há propagação assíncrona que exige cuidado;
7. principais classes/métodos que futuramente seriam alterados;
8. caminho do plano criado.

Reforçando: **não implemente nenhuma alteração nesta etapa.**
