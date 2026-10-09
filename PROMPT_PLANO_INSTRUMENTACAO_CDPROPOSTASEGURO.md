# Prompt — Planejar instrumentação de cdPropostaSeguro para observabilidade

Atue como engenheiro de software sênior especializado em Java/Spring Boot, observabilidade, Datadog APM, distributed tracing, logs estruturados, MDC e desenho de telemetria.

## Objetivo

Criar um **plano técnico de instrumentação** para tornar o identificador de negócio `cdPropostaSeguro` observável e reutilizável nos fluxos do Darwin Monitor.

O foco é identificar **onde o `cdPropostaSeguro` já existe no fluxo da aplicação, onde ele se perde e em quais pontos faz sentido expô-lo como atributo estruturado de observabilidade**.

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

## Métricas customizadas

Avalie separadamente.

**Não recomende usar `cdPropostaSeguro` como tag de métrica customizada** se isso gerar alta cardinalidade.

Se houver proposta de métrica futura, prefira dimensões de baixa cardinalidade, como:

- `status:sucesso|falha`;
- `meio_pagamento:cartao|debito`;
- `fluxo:recorrencia`;
- outras dimensões funcionais estáveis.

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

| Prioridade | Aplicação | Fluxo | Arquivo/Classe | Método | Momento | cdPropostaSeguro disponível? | Log estruturado | Span attribute | Motivo |
|------------|-----------|-------|----------------|--------|---------|-----------------------------|-----------------|----------------|--------|

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

- enriquecer pontos obrigatórios;
- permitir busca de uma proposta;
- permitir correlação básica.

## Etapa 2 — Propagação assíncrona

- garantir ID em consumers/producers;
- revisar MDC/contexto.

## Etapa 3 — APM

- adicionar atributo ao span;
- validar filtros no Datadog.

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
8. estratégia de spans/APM;
9. contexto assíncrono;
10. riscos;
11. padrão de nomenclatura;
12. plano por etapas;
13. arquivos/classes que futuramente seriam alterados;
14. critérios de aceite;
15. impacto esperado nos dashboards Darwin.

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
