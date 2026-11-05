# Darwin Monitor — Prompt 04A: investigação do IND-02 (Taxa de Falha) e auditoria do widget legado

> **Objetivo desta execução: investigar e propor uma definição tecnicamente defensável para o IND-02, sem alterar código, widgets, JSON, Python, Git ou Datadog.**
>
> Execute a investigação com os arquivos reais do workspace. **Não publicar, não executar scripts de publicação, não criar/substituir widgets e não reabrir auditorias já concluídas do IND-01.**

## 1. Contexto — ponto de partida

Projeto **Darwin Monitor**, Banco BV, linha de base de observabilidade durante desmembramento do monólito `venda-digital` e transição DXC/Bankly/Pismo. Nosso limite é o que é efetivamente observável na fronteira dos serviços sob responsabilidade do time; não confundir logs de integração com conclusão de seguro ou processamento de outro time.

Dashboard: **`[UAT] Darwin Monitor`** na organização BV-DUQ, dashboard ID **`ymk-jgs-3dd`**.

Workspace: a pasta **`darwin-monitor/`**, que contém diretamente `planos/`, `dashboards/`, `uat_darwin_monitor_dashboard.json` e `update_uat_dashboard.py`. Localize a raiz física correta; não recrie `docs/darwin-monitor` dentro de `darwin-monitor`.

**Já realizado:**
- Inventário de indicadores a partir de `Monitoramento Cartões 1.xlsx`, aba `Monitoramento`: **IND-02 = Taxa de Falha**. Os filtros do ciclo são: responsável `Seguros` ou `Seguros - Monitoramento do v6`, observações vazias. A planilha é a fonte para **fórmula e escopo de negócio**; não inferir seu conteúdo a partir do título.
- **IND-07 Origem:** 3 widgets funcionais, preservados.
- **IND-01 Conta Cartão:** 3 widgets operacionais de Logs/Count já implementados: (A) processamentos iniciados Pub/Sub, (B) retornos de sucesso local do orquestrador, (C) falhas de negócio e retentativas em duas séries. Classificação: cobertura operacional parcial. **Não modificá-los**.
- Instrumentação `cdPropostaSeguro` implantada em UAT e **confirmada pelo responsável em logs estruturados de Conta Cartão, sob `mdc.cdPropostaSeguro`**. Foi observada correlação **parcial** de múltiplas etapas de uma proposta: exemplo com cinco logs, dois inícios e duas mensagens de UUID de idempotência. Isso não prova taxa por proposta única, nem propagação a todas as etapas terminais.
- Nas últimas 24h verificadas, após o deploy da instrumentação, **não houve logs de “Chamando orquestrador de recorrência”**; historicamente essa mensagem apareceu raramente (3 registros em período de 2 dias antes e 31 em período próximo de 7 dias atrás). Tráfego irregular em UAT **não prova falha de logging**. Não pedir testes de fluxo completo agora; depende de QA.
- O **Stream de Recorrência** teve a coluna de correlação corrigida manualmente de `@cdPropostaSeguro` para `@mdc.cdPropostaSeguro`. **Em Eventos Pagamento ainda não se observou `cdPropostaSeguro` em logs**; isso é uma **pendência de observabilidade entre serviços**, sem concluir que código/implantação estejam errados. Registrar, mas não iniciar instrumentação de Pagamento nesta tarefa.
- O responsável exportou o JSON remoto após corrigir widgets e executou a sincronização remoto → local, relatada como concluída com **zero divergências de widgets** e baseline Git local. `uat_darwin_monitor_dashboard.json` é o artefato canônico versionado. **A correção manual posterior do Stream pode ainda não estar sincronizada no Git local**; portanto, não fazer qualquer PUT e não presumir equivalência atual.
- Existe um widget **legado** chamado `Taxa de Falha Funcional de Recorrência (Cartão)`, apresentado em UAT com aproximadamente `1,93%` na janela exibida. Foi inspecionado manualmente: visualização **Query Value**, fonte **Logs** e fórmula **`(a/b)*100`**, com `a` buscando mensagens antigas de falha em Conta Cartão e `b` mensagens de início de recorrência. Esse widget **não foi aprovado como indicador percentual confiável** e pode pertencer ao IND-02; não o apagar sem analisar. Não converter automaticamente `[RETRY-PUBSUB]` em falha funcional.

## 2. Sua tarefa — investigar somente IND-02

### Etapa A — recuperar requisito real

1. Localize a planilha **`Monitoramento Cartões 1.xlsx`**, aba `Monitoramento`, e os relatórios `planos/04-inventario-filtrado-indicadores.md`, `planos/07-...`, `planos/08-...`, `planos/09-...`, `planos/10-...`, `planos/12-...` e `planos/14-baseline-json-remoto-para-local.md` **apenas quando existirem e forem relevantes**. Não inventar caminhos, fórmulas ou conclusões documentais.
2. Transcreva **fielmente** objetivo, formulação, numerador, denominador, frequência, escopo, aplicação e ressalvas **efetivamente presentes** na linha elegível de **Taxa de Falha / IND-02**. Diferencie o que vem da planilha do que é interpretação sua ou texto do widget.
3. Verifique se `Taxa de Falha Funcional de Recorrência (Cartão)` mede de fato esse mesmo requisito ou se é somente um KPI legado aproximado. **Associação por título não é prova.**

### Etapa B — auditar widget existente, somente leitura

1. Leia `uat_darwin_monitor_dashboard.json` e encontre **a definição completa** do widget legado de taxa de falha, inclusive dentro de grupos aninhados. Identifique título, tipo, queries A/B, `data_source`, `compute`, filtros, fórmula, agregação, intervalos, tipo de log e demais definições relevantes.
2. Não presuma que o JSON local já contém a última correção manual do Stream; **não sincronize nem edite o JSON nesta execução**.
3. Para cada query da taxa, identifique **exatamente o que conta**: ocorrências de logger, processamento/tentativa, proposta única, condição de negócio, falha técnica, retry/NACK, erro HTTP, ou outra coisa. Relacione denominador e numerador com a mesma população e janela.
4. Determine se o número exibido como `%` é semanticamente defendível; considere eventos repetidos, reentrega Pub/Sub, etapas ausentes, conclusões não observadas e eventual divisão por zero. Uma fórmula matemática calculável não é automaticamente uma métrica correta. Não alterar fórmulas ainda.

### Etapa C — evidência no código real

Inspecione na branch/código **efetivamente presente** de `sboot-pseg-base-orch-gestao-eventos-conta-cartao` as rotas, condições e loggers que podem embasar o IND-02, com prioridade para `SolicitacaoRecorrenciaViaPubSub.java`, e arquivos/processors/rotas relacionados **se necessários**. Identifique classe/método/condição e trecho literal do log; não se basear em relatos antigos sem confrontar o código.

Já foram verificados manualmente em UAT os seguintes tipos de evento:
- `"Iniciando fluxo de recorrência assíncrono para proposta"`;
- `"Retorno com sucesso do orquestrador de recorrência"` (aparece em janelas antigas);
- `"[EVENTO-31-PUBSUB][FALHA-NEGOCIO]"`;
- `"[EVENTO-31-PUBSUB][RETRY-PUBSUB]"`.

Evidências visuais do código:
- A rota adiciona o identificador de correlação após validação via `ObservabilityProcessor`.
- Uma chamada ao orquestrador antecede o log de sucesso local.
- Há ramificações para falha funcional e para retry (incluindo alguns casos HTTP/rede), mas **não presumir equivalência entre todas as exceções, falhas de negócio e indisponibilidade definitiva**.
- A presença de mesmo `cdPropostaSeguro` em logs de etapas intermediárias **não comprova** que sucesso e erro de todo fluxo também o carreguem.

**Evite varrer todo o monólito, Eventos Pagamento ou logs de outros indicadores** salvo se a planilha ou o caminho técnico do IND-02 exigir e houver justificativa explícita.

### Etapa D — matriz de suficiência e proposta de monitoria

Entregue uma matriz com, para cada sinal possível:
- requisito de negócio a que atende;
- evento observável e condição que o emite;
- classe/método real;
- consulta textual ou atributo **já comprovado** em UAT, com fonte Logs/APM correta;
- unidade de contagem e possibilidade de múltiplas ocorrências por proposta;
- efeitos de retries e lacunas na observação;
- status: **COMPROVADO / PARCIAL / NÃO VALIDADO / INSUFICIENTE / FORA DA FRONTEIRA**.

Distinga três coisas, sem misturá-las:
1. **falha funcional de negócio** (rejeição);
2. **falha técnica que aciona retry/NACK** (tentativa reexecutável);
3. **falha definitiva por proposta**, se e somente se houver sinal explícito comprovado.

Compare pelo menos estas alternativas **se fizerem sentido ao requisito real da planilha**:
- manter um **widget de volumes** de falhas funcionais e retentativas (séries distintas);
- calcular **taxa por tentativa** com numerador/denominador homogêneos e limites expressos;
- calcular **taxa por proposta única** somente se a implementação real de agregação/deduplicação e a cobertura de resultados for comprovada.

Aproveite os widgets IND-01 sem criar gráficos duplicados por reflexo. **Não sugerir uma fórmula percentual artificial** apenas para preencher um KPI da planilha. Se taxa não for viável ainda, indicar o menor recorte operacional útil e o que falta para uma taxa válida.

### Etapa E — conclusão executiva e próximo passo mínimo

Conclua:
- **Widget legado**: `MANTER COMO ESTÁ / RENOMEAR FUTURAMENTE / CORRIGIR EM ETAPA SEPARADA / SUBSTITUIR APÓS APROVAÇÃO`, com justificativa; **nenhuma alteração agora**.
- **IND-02**: `VIÁVEL INTEGRALMENTE / VIÁVEL PARCIALMENTE / NÃO COMPROVADO`, com fundamento e sem atrelar “viável” a dados recentes de UAT que não existem.
- Se algo crucial depender de Datadog UAT, peça no máximo **um teste pontual não redundante** (consulta exata, período, resultado esperado e como interpretar). **Não solicitar novamente as pesquisas A–E** usadas no IND-01 para mera existência dos eventos. Se já houver evidência suficiente para propor um widget parcial, não bloquear essa proposta por falta de logs recentes.
- Observe como pendência separada a ausência de `mdc.cdPropostaSeguro` em logs de Eventos Pagamento **até agora**, sem abrir novo escopo.

## 3. Restrições obrigatórias

- **NÃO** executar PUT, publicar, criar, editar ou excluir widgets no Datadog.
- **NÃO** modificar `uat_darwin_monitor_dashboard.json`, `update_uat_dashboard.py`, Java, configurações de APM, pipeline, monitores ou alertas.
- **NÃO** disparar eventos, fazer deploy ou tentar contornar a API de Logs bloqueada.
- **NÃO** criar queries inventadas, tags de métricas de alta cardinalidade, `sum:log.count`/ `sum:trace.log_count` sem métrica real, `distinct_count` sem suporte comprovado, percentuais baseados em populações diferentes ou marcadores inexistentes.
- **NÃO** criar duplicação do diretório do projeto.
- **NÃO** enviar documentos internos do BV ou JSON corporativo para `senaadrianojr/tmp-prompt`; esse repositório contém apenas prompts.
- **NÃO** fazer `git push` ou commit de código/JSON. Esta etapa autoriza apenas salvar o relatório local, sem manipular o baseline versionado do dashboard.

## 4. Entregável

Crie **`darwin-monitor/planos/15-investigacao-ind02-taxa-de-falha.md`** no diretório correto, incluindo:

1. requisitos literais da planilha e campos não disponíveis;
2. inventário completo da configuração do widget legado (com queries e fórmula exatamente como estão);
3. tabela de evidência por tipo de evento, apontando código atual vs UAT;
4. análise de numerador, denominador, retries, correlação e deduplicação;
5. comparação objetiva das alternativas de monitoria;
6. parecer de suficiência/viabilidade e limitações;
7. **próximo passo mínimo** para construção posterior, sem executá-lo;
8. registro das pendências: fluxo terminal de `cdPropostaSeguro` em Conta Cartão e ausência de observação em Eventos Pagamento.

**Ao finalizar, responda objetivamente com:** caminho do relatório, requisito literal encontrado, diagnóstico do widget legado, recomendação IND-02, teste pontual indispensável (se houver) e confirmação explícita de que **dashboard, JSON, Python, Java e Git permaneceram intocados**. Uma única resposta de encerramento, sem looping.

**O objetivo é viabilizar a decisão técnica sobre o IND-02 sem criar trabalho repetitivo ou comprometer os indicadores que já funcionam.**