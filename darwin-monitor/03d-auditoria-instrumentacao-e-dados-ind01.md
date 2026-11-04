# Darwin Monitor — Prompt 03D: Auditoria da instrumentação e suficiência dos dados do IND-01

## PAPEL E OBJETIVO — LEIA ANTES DE ATUAR

Você continuará **exclusivamente o IND-01 — Índice de Sucesso**, na aplicação `sboot-pseg-base-orch-gestao-eventos-conta-cartao`, do projeto Darwin Monitor.

Esta etapa NÃO é apenas descobrir se existe `cdPropostaSeguro`. Sua missão possui **duas perguntas obrigatórias, igualmente importantes**:

**A. Instrumentação:** a telemetria existente (logs, MDC, spans/traces, métricas) está corretamente implementada nos pontos reais do fluxo, inclusive na rota assíncrona Pub/Sub? Ela acompanha os eventos sem perder contexto, criar dados falsos ou gerar riscos técnicos?

**B. Suficiência para o monitoramento:** com essa telemetria, conseguimos montar **dados tecnicamente corretos, com significado de negócio explícito**, para os widgets e indicadores do IND-01? Em cada métrica, o que exatamente contamos, qual é a fonte, o que falta, qual limitação é aceitável e qual alteração mínima seria necessária?

**Não encerre sua auditoria só porque encontrou `MDC.put`, um `ObservabilityProcessor`, logs `INFO` ou um tag no span.** Prove o comportamento pelo caminho de execução e cruze-o com as necessidades dos dados de monitoramento.

O objetivo do Darwin Monitor é fornecer uma **linha de base operacional para detectar regressões enquanto o monólito é desmembrado e o ecossistema migra para Pismo**. Monitoramos a fronteira sob responsabilidade do nosso time; não podemos declarar efetivação final em sistemas de outros times só com HTTP 2xx ou um log local.

**ESCOPO DA EXECUÇÃO: INVESTIGAR, CONCLUIR E PLANEJAR. NÃO IMPLEMENTAR.**

É proibido nesta etapa:
- alterar código Java, testes, instrumentação, configuração de logs, métricas ou pipeline;
- editar dashboard remoto, widgets, monitores, o JSON de publicação ou `update_uat_dashboard.py`;
- executar atualizações HTTP, publicar ou modificar credenciais/cookies;
- investigar outros indicadores ou reabrir o IND-07;
- sugerir desativar/comentar testes para viabilizar instrumentação;
- declarar atendimento integral/percentual confiável sem demonstrar numerador, denominador, população e semântica.

## 0. Regras rigorosas de diretório e de documentação

A raiz do workspace é a pasta **`darwin-monitor/`**. Confirme o caminho real primeiro. O destino do relatório é **`planos/` na raiz desse projeto**.

Leia os relatórios já existentes:
- `planos/07-investigacao-ind01-indice-sucesso-conta-cartao.md`
- `planos/08-validacao-telemetria-ind01-conta-cartao.md`
- `planos/09-consolidacao-ind01-e-widgets-conta-cartao.md`

Se algum estiver em outra posição, localize-o e leia sem movê-lo. A planilha **`Monitoramento Cartões 1.xlsx`**, aba **Monitoramento**, e o inventário do IND-01 são as fontes do requisito original. Histórico é hipótese; **código atual é a fonte para execução, e Datadog UAT é a fonte para telemetria efetivamente disponível**.

**Crie UM arquivo novo** ao final, em:
`planos/10-auditoria-instrumentacao-e-suficiencia-ind01-conta-cartao.md`

É expressamente proibido criar novos relatórios em `docs/darwin-monitor/planos/` ou repetir `darwin-monitor/` dentro de `darwin-monitor/`. Não altere os relatórios 07, 08 ou 09 nesta fase. Caso o destino já exista, escolha um sufixo incremental e informe o caminho.

## 1. Evidência já resolvida — NÃO REPETIR TESTES DESNECESSÁRIOS

O responsável **já pesquisou diretamente no Datadog Log Explorer UAT** e confirmou registros para TODOS estes casos:

1. Serviço `sboot-pseg-base-orch-gestao-eventos-conta-cartao`, ambiente `uat`.
2. Início do fluxo Pub/Sub: `[EVENTO-31-PUBSUB] Iniciando fluxo de recorrência assíncrono para proposta`.
3. Retorno de sucesso local do orquestrador: `[EVENTO-31-PUBSUB] Retorno com sucesso do orquestrador de recorrência` — encontrado em logs **mais antigos**.
4. Falha de negócio: `[EVENTO-31-PUBSUB][FALHA-NEGOCIO]`.
5. Retentativa: `[EVENTO-31-PUBSUB][RETRY-PUBSUB]`.

A busca textual e a existência desses registros **estão confirmadas manualmente pelo usuário**. Não peça para executar novamente os mesmos filtros nem para trocar de List para Timeseries apenas para comprovar que existem dados. A apresentação gráfica com Count é detalhe de implementação dos widgets e poderá ser validada na própria publicação futura.

**O que NÃO foi comprovado:** presença do `cdPropostaSeguro` nos registros reais deste serviço em UAT, cobertura do campo em todos os caminhos, integridade da correlação entre eventos, deduplicação, grupos/facets, fórmula de percentual e equivalência de janelas de eventos. Essas são as novas perguntas da auditoria.

Não reutilize como verdade os caminhos de atributo do `venda-digital` (`@mdc.processadora` etc.). A estrutura deste serviço precisa ser confirmada.

## 2. Defina primeiro o contrato de dados necessário (antes de olhar só para o MDC)

Use o requisito original e os widgets candidatos do relatório 09 para montar uma **MATRIZ DE NECESSIDADE DE DADOS**, cobrindo pelo menos:

- **IND-01 original:** "Índice de Sucesso", objetivo de integridade BV–Bankly, fórmula original **`Recorrências recebidas ÷ Recorrências enviadas (%)`**, preservada literalmente. Há ambiguidade de interpretação: não inverta a fórmula nem a descarte por intuição.
- **Widget A — Volume de processamento de recorrências:** qual acontecimento observável, qual unidade (log, mensagem, tentativa ou proposta), quais filtros, exclusões e risco de NACK/reentrega.
- **Widget B — Volume de encaminhamentos bem-sucedidos:** qual sucesso comprovado pelo código (resposta local do orquestrador, e não adesão final), qual unidade, exceções e perdas de sinal.
- **Widget C — Ocorrências de falha:** separar falhas de negócio e retentativas; contagem por tipo, condição de emissão e risco de sobreposição. NÃO agrupar por `message` completo se ele contém identificadores variáveis.
- **Correlação adicional desejável:** acompanhar uma mesma proposta entre recebimento, retorno, falha e retries usando `cdPropostaSeguro`, **se estiver presente e estável**.
- **Taxa operacional alternativa (condicional):** avaliar se há condições de definir numerador/denominador comparáveis; se não houver, **não produzir percentual artificial**.

Para cada linha da matriz, exija:
**evento de negócio → fronteira observável → ponto/condição no código → sinal exigido → campos mínimos → regra de contagem/agregação → tratamento de retries/duplicidade → telemetria existente → lacuna → status**.

A matriz deve responder claramente **se os dados necessários já estão satisfeitos**, e não apenas listar possibilidades.

Categorias de status:
- **SATISFEITO EM UAT:** evidência concreta na aplicação E no Datadog para o dado exigido;
- **SATISFEITO NO CÓDIGO / UAT NÃO CONFIRMADO:** implementação verificada, sem evidência do dado correspondente em UAT;
- **PARCIAL:** dá para entregar uma observabilidade fiel, mas não a métrica integral;
- **INSUFICIENTE:** falta dado indispensável para o monitoramento proposto;
- **DEPENDÊNCIA EXTERNA:** dado de efetivação/negócio não está sob nossa fronteira e não pode ser inferido.

Você pode ter sinais confirmados mas agregações/correlações ainda pendentes. Registre-os separadamente; **não confunda log encontrado com métrica calculável ou com proposta única**.

## 3. Auditoria dirigida do código real e da instrumentação

Investigue o repositório `repos/sboot-pseg-base-orch-gestao-eventos-conta-cartao` (localize caminho real). **Comece por `SolicitacaoRecorrenciaViaPubSub.java`**, e siga somente as dependências necessárias. Inclua, se relacionados ao fluxo atual, `SolicitacaoRecorrenciaRouter.java`, `ValidaEventoGeradoProcessor`, `ObservabilityProcessor`, configuração de log/JSON, consumidores, propagação Pub/Sub, cliente HTTP e tratamento de erros.

Verifique ponto a ponto, com trechos ou referências verificáveis de **classe/método/condição** (não use números fixos de linha):

### 3.1. Existência e significado de `cdPropostaSeguro`
- Onde o código extrai a proposta? De qual payload, header, `ExchangeProperty` ou objeto validado?
- Onde é aplicado `MDC.put` (e com que nome exato)? Há divergência entre `cdPropostaSeguro`, `cdPropostaSeguroByRequest` ou outros nomes?
- Onde ocorre a leitura do identificador e **em qual ordem** em relação à validação, roteamento e logs de recebimento/sucesso/falha?
- Há proteção contra `null`, branco, valor stale e valores de mensagens anteriores?
- A configuração de logging realmente serializa o MDC no formato enviado ao Datadog, ou isso é só uma hipótese baseada no Java?
- Como a instrumentação interage com `Tracer`, `currentSpan()`, tags APM, Micrometer e tracing? Distinguir MDC/log, trace tag e tag de métrica. Verificar tratamento seguro de span nulo e cardinalidade.

### 3.2. Abrangência e ciclo de vida
- Inspecione **rota Pub/Sub assíncrona**, rota síncrona e caminhos de sucesso, erro, retry, NACK, rejeição e interrupção precoce.
- Verifique **qual evento cria o contexto**, quando ele é limpo (`MDC.remove/clear` e tratamento `finally`, `onCompletion` ou equivalente), e se callbacks, threads, executores ou consumidores mudam o contexto entre logs.
- Prove pelo fluxo se o identificador estará disponível **em cada log usado pelos widgets**, não apenas em algum log isolado.
- Verifique se o campo se mantém **idêntico em reentregas/retries da mesma proposta** e se uma proposta pode ter múltiplos eventos de negócio legitimamente distintos.
- Identifique riscos de contexto ausente, propagação interrompida, limpeza antecipada, vazamento entre mensagens e alto volume de tags.

### 3.3. Gaps históricos (apenas como pistas)
Relatórios anteriores indicaram, em uma branch `feature/SEGNP2-5340`, que:
- `ObservabilityProcessor` teria sido colocado **antes** de `ValidaEventoGeradoProcessor` na rota síncrona, quando o identificador ainda não existiria;
- a rota `SolicitacaoRecorrenciaViaPubSub.java` poderia não invocar o processador;
- tags `cdPropostaSeguro` dependem da ordem de inicialização.

**Esses são achados históricos, NÃO conclusões sobre o código atual.** Verifique se foram corrigidos ou continuam presentes. Registre branch/commit quando acessíveis e diferencie código local, alterações não commitadas e versão efetivamente implantada em UAT. **Não recomende reaplicar correção já presente.**

### 3.4. Evidência real e cobertura dos eventos
Para os logs B–E já confirmados em UAT, valide:
- a emissão do log antes/depois da operação que ele pretende comprovar;
- se log de sucesso só ocorre após resultado elegível (ex.: HTTP 2xx) ou se há outros caminhos;
- a possibilidade de **múltiplos logs para uma tentativa**;
- falhas que não geram os marcadores analisados;
- se o evento contém identificador estruturado, texto concatenado, ou apenas correlação por trace;
- se o `cdPropostaSeguro` no contexto realmente alcança esse logger.

Se houver eventos/atributos já estruturados mais adequados do que o texto literal, apresente-os, mas **não presuma sua existência em UAT sem prova**.

## 4. Verificar viabilidade técnica dos cálculos e das visualizações

Sua obrigação é aferir a **suficiência dos dados para montar o monitoramento**, não só a correção do código.

Para cada widget candidato, responda explicitamente:
1. **O que exatamente será contado?** Logs, tentativas, mensagens ou propostas distintas? Qual a unidade correta para este widget?
2. **Fonte exata:** Logs disponíveis em UAT, APM Traces ou métricas efetivamente existentes (não inventar `trace.log_count` ou `log.count`).
3. **Eventos de inclusão/exclusão:** condições necessárias; necessidade de ausência de duplicidade.
4. **Campos de seleção/correlação:** `service`, `env`, marcador, `cdPropostaSeguro` ou outro **somente se verificado**; nome e caminho real do atributo se confirmado no Datadog.
5. **Cálculo/agrupamento:** Count é viável a partir dos logs? Distinct Count é necessário? Há campo indexável/facet? Quais premissas ainda não foram testadas?
6. **Impacto de retries:** como a contagem representa fluxo e por que não mede necessariamente propostas únicas; se houver identificação estável, qual deduplicação é **tecnicamente suportada**, sem assumir que o Datadog faz join de logs/transações.
7. **Janelas temporais e população:** logs de recebimento e de retorno podem estar em janelas diferentes; quais limites há para qualquer razão/taxa?
8. **Completude e fidelidade:** o dado é suficiente para o propósito operacional de monitorar regressões durante o Darwin? Se não, **qual sinal está faltando e em qual ponto do código seria produzido?**
9. **Segurança e operação:** evite alta cardinalidade em métricas e exposição de identificadores em dashboards públicos; use correlação de logs/traces onde autorizado.

**Priorize resultado utilizável:** se os três widgets de volume já forem satisfeitos por logs de UAT, recomende implementá-los mesmo que a taxa percentual continue inadequada. Não bloqueie visualizações parciais apenas por falta de deduplicação que elas não exigem.

## 5. Verificação UAT adicional APENAS para a lacuna nova

Não repita os testes A–E e não peça nova validação genérica de Timeseries.

A questão ainda sem resposta é: **`cdPropostaSeguro` chega como atributo estruturado nos logs de Eventos Conta Cartão em UAT e está presente nos eventos necessários?**

Se houver consulta de **leitura autorizada, já disponível** ao Datadog, use-a somente para esta verificação, sem modificar ferramenta ou credenciais. Se a API legada não permitir ler logs, não force endpoints nem contorne bloqueios.

Caso dependa do responsável, proponha **um teste manual pontual**, na UI do Log Explorer UAT, partindo de um dos eventos B–E já encontrados:
- abrir um registro e verificar se há atributo de correlação estruturado; registrar **o nome/caminho exato e se está preenchido**;
- se ausente, verificar **um segundo evento distinto** para diferenciar uma lacuna geral de um ponto específico de perda de contexto;
- se disponível, indicar um **único teste adicional realmente necessário** para verificar continuidade entre início, sucesso e retry (sem expor IDs reais no relatório ou chat).

**Sem registro real inspecionado, use UAT NÃO CONFIRMADO, não EXISTENTE.** Um valor presente em um único log não comprova presença em todos os tipos de evento.

Não recuse a entrega de um plano técnico por causa da limitação de acesso à API de Logs.

## 6. Planejamento de instrumentação — somente onde houver deficiência comprovada

Se identificar lacunas de sinal, MDC, logs, APM ou contagem:
- proponha uma alteração **mínima e localizada**, com **classe/método/ponto no fluxo/condição**, campo(s) necessários, regra de preenchimento e limpeza, proteção `null` e contexto assíncrono;
- identifique **qual widget/necessidade de dado** ela habilita e por que a instrumentação existente é insuficiente;
- diferencie **correção de instrumentação atual** de **instrumentação nova**;
- proponha testes unitários/integrados específicos, inclusive sucesso, erros, reentrega Pub/Sub e limpeza do MDC entre eventos;
- não mude contratos de APIs, regras de negócio nem instale nova dependência sem justificativa;
- não adicione logs redundantes em pontos que já são observáveis;
- **não use `cdPropostaSeguro` como dimensão/tag de métrica de alta cardinalidade**; se for necessário deduplicar/correlacionar propostas, proponha mecanismo tecnicamente viável e autorizado, sem prometer que simples `count_distinct` resolve conclusão de negócio ou junção entre fluxos;
- se o dado necessário estiver em sistemas de terceiros, assinale **dependência externa**, não simule sucesso.

Apresente uma pequena ordem de execução com **pré-condição → alteração mínima → testes → comprovação no UAT → uso no widget**. **Não implemente nenhuma dessas alterações.**

## 7. Entregável obrigatório: relatório decisório e objetivo

Crie somente `planos/10-auditoria-instrumentacao-e-suficiencia-ind01-conta-cartao.md` com as seções:

1. **Escopo, versão/branch avaliada e evidências anteriores aproveitadas.**
2. **Contrato dos dados necessários** (requisito original e widgets A/B/C), na matriz especificada na seção 2.
3. **Auditoria `cdPropostaSeguro`**: extração, ordem, rotas síncrona/assíncrona, sucesso/falhas/retry, contexto MDC, propagação e limpeza, trace/APM, serialização e evidência UAT.
4. **Suficiência por widget**: o que já é atendido, o que não é, se Count/Distinct/percentual são aplicáveis, com justificativa.
5. **Taxa original versus métrica alternativa**: se é calculável e semanticamente válida; se não, preservar cobertura parcial.
6. **Gaps comprovados versus hipóteses**, com local exato no fluxo e impacto na monitoria. Não declarar ausência de algo sem evidência de inspeção suficiente.
7. **Plano de implementação mínima** onde necessário, com testes; nenhuma edição de código.
8. **Decisão final operacional por item**, usando uma tabela compacta:
   - Widget A: PRONTO COM LOGS / REQUER AJUSTE / DEPENDE DE VALIDAÇÃO UAT;
   - Widget B: idem;
   - Widget C: idem;
   - Correlação por `cdPropostaSeguro`: VALIDADA / PARCIAL / NÃO CONFIRMADA / QUEBRADA;
   - Percentual do IND-01: VALIDÁVEL / NÃO VALIDÁVEL AINDA;
   - Cobertura do requisito: INTEGRAL / PARCIAL / BLOQUEADA, com justificativa.
9. **Uma única próxima ação recomendada:** se o único bloqueio relevante for verificar `cdPropostaSeguro` em UAT, peça esse teste específico. Se houver falha comprovada no código, indique qual correção deve ser planejada primeiro. Se todos os dados exigidos pelos widgets operacionais estiverem satisfeitos, recomende a etapa de montagem no dashboard sem repetir buscas de logs.

**Não crie novas "queries de métrica" especulativas, não inclua IDs de cliente/propostas nos documentos e não use trechos históricos como prova atual.** Conclusões devem ser rastreáveis ao código inspecionado, ao requisito e/ou à evidência manual de UAT.

## 8. Resposta final (curta e acionável)

Informe:
1. Arquivo criado em `planos/`;
2. `cdPropostaSeguro`: existe? Em quais rotas e em quais eventos? Está no ponto correto? Separe constatação de hipótese;
3. Matriz-resumo dos **três widgets**: dados suficientes ou lacuna concreta;
4. Percentual do IND-01: comprovável agora ou não, e por quê;
5. Correções mínimas recomendadas (se houver), **sem implementar**;
6. **Uma única próxima ação**, sem repetir validações de existência de logs.

**PARE após entregar a auditoria e o plano.**
