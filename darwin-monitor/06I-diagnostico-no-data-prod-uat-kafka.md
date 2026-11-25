# PROMPT 06I — Diagnóstico Diferenciado de "No Data" em PROD e Ausência de Sinais Kafka em UAT/PROD

> **Executor:** Gemini CLI, no workspace corporativo autorizado.
>
> **REPOSITÓRIO PÚBLICO — RESTRIÇÃO DE SEGURANÇA:** este arquivo contém somente instruções metodológicas sanitizadas. Não incluir aqui nomes reais de serviços, endpoints, atributos internos, queries literais, IDs/URLs de dashboards/widgets, logs, tópicos, dados, planilhas, relatórios corporativos, snapshots JSON, hashes privados, credenciais nem código. Os detalhes técnicos e resultados serão lidos e registrados **exclusivamente no workspace corporativo privado**.
>
> **AUTORIZAÇÃO:** diagnóstico técnico, leitura seletiva dos artefatos locais e código pertinente, inspeção local de configurações e JSONs, comparação por Python em modo não destrutivo, elaboração de consultas **para execução manual autorizada** no Datadog pelo responsável, e criação de **um relatório privado** de achados e plano de correção. **NÃO** alterar dashboards ou canônicos, não editar queries, não importar/exportar remotamente, não executar PUT/POST, não modificar código Java, instrumentações, pipelines ou infraestrutura, não criar métricas/monitores/facets e não executar deploy. Git é **exclusivamente local** e só pode ser consultado; nenhum commit/push ou comando destrutivo foi autorizado.
>
> **ESTADO ANTERIOR:** a promoção de duas visualizações auxiliares já foi concluída e reconciliada em UAT e PROD. O relatório privado mais recente de encerramento (etapa 06H, último número local conhecido 42) registrou ambos os ambientes com 21 widgets de dados e os 19 anteriores preservados. **Não reabrir nem reeditar essa promoção.**
>
> **NOVO PROBLEMA CONFIRMADO PELO RESPONSÁVEL:** em PROD, **três widgets de fluxos de cancelamento/estorno** mostram `No Data` há cerca de uma semana, enquanto em UAT exibem registros (não presumir mesmos intervalos ou mesma frequência). Adicionalmente, **dois widgets de consumo Kafka** não mostram registros nem em UAT nem em PROD. Os demais widgets com prefixos de alerta exibem dados conforme observação do responsável. Esses cenários constituem **duas frentes diferentes**, que exigem diagnóstico distinto. Não investigar todos os widgets como se estivessem falhando pelo mesmo motivo.
>
> **PRIORIDADE:** descobrir se falta tráfego real, se o sinal/fonte está mal definido, se o filtro de ambiente/serviço/storage está errado, se há diferença de deploy ou emissão de logs/métricas, ou se a evidência ainda é insuficiente. **Não tentar "resolver" removendo filtros à força, substituindo métricas por logs arbitrários ou fabricando zero eventos.**

## 1. Resultado esperado

Entregar diagnóstico **individual por widget** com trilha auditável:

`widget/indicador | UAT (evidência, intervalo) | PROD (evidência, intervalo) | fonte e unidade de medida | query/expressão original | hipóteses testadas | evidências positivas/negativas | causa confirmada ou não comprovada | gravidade/impacto | ação proposta | validação humana mínima necessária`.

Separar explicitamente:
- **Frente A — 3 widgets UAT com registros / PROD `No Data`:** fluxos de solicitação de baixa no cancelamento de cartão, esgotamento de tentativas para DLQ no cancelamento de débito e disparo de solicitação de efetivação de estorno. Os nomes exatos e IDs devem ser recuperados dos JSONs privados. Prioridade investigativa ALTA, mas não declarar incidente ou falha do serviço sem evidência.
- **Frente B — 2 widgets Kafka sem registros em ambos:** consumo de postagem de cartão e consumo de faturas liquidadas. Identificar se são métricas Kafka/APM, logs ou outra fonte, e se representam o que os títulos prometem. Não concluir que não há mensagens no tópico com base no gráfico vazio.
- **Referência de controle (opcional):** outros widgets que apresentam dados, inclusive de conciliação/Kafka, podem ajudar a confirmar ambiente e conectividade de telemetria; **não** inferir que compartilham serviço, tópico, consumo ou fonte só porque estão próximos no dashboard.

O operador já confirmou as observações visuais. Não exigir novo print da mesma tela nem nova exportação por hábito. O objetivo não é validar se o dashboard tem 21 widgets novamente, mas descobrir **por que determinadas visualizações não demonstram o sinal esperado**.

## 2. Fontes obrigatórias — sem redescoberta geral

Ler somente o necessário, nesta ordem:

1. Relatório privado de reconciliação final da etapa 06H e o **canônico UAT pós-P1**, além do snapshot/export PROD pós-P1 já reconciliado.
2. Relatórios anteriores pertinentes às definições funcionais de cancelamento, estorno, DLQ, Kafka/saúde operacional, instrumentação e limites de responsabilidade; planilha mestre apenas nas linhas dos indicadores relacionados.
3. Os **cinco nós de widget específicos**, incluindo source, query/expressão, compute/aggregation, index/storage, filtros, template variables, group, timeseries, intervalos, fórmulas e descrições. Inspecionar widgets aninhados e variáveis de template de ambos os ambientes.
4. Código de emissão/consumo **apenas quando necessário** para fundamentar nome e condição de produção do sinal: rota, processor, logger, status, eventos de início/sucesso/erro, assinaturas Kafka, tags APM/Micrometer e, quando houver, configuração de índices/monitoramento. Distinguir código na branch do que foi realmente implantado.
5. Evidências históricas privadas de consultas e logs, destacando **ambiente, data e janela**; verificar se a evidência UAT é contemporânea ou histórica. Não assumir que a ausência de registros em uma janela recente invalida logs históricos.

O workspace é versionado com **Git local**. Ler `git status --short` e usar diff/histórico **somente para esclarecer origens**; nunca dar `git add`, `commit`, `push`, `reset`, `restore`, `checkout`, `stash` nem reformatar o projeto.

## 3. Metodologia comparativa, sequencial e econômica

Começar por um inventário de cinco linhas com informação **literal** extraída dos JSONs e uma tabela de diferenças UAT × PROD:

- tipo de widget e fonte (logs, APM, métricas, Kafka, traces, etc.);
- query original e versão PROD;
- filtros `env`, serviço, hostname, tópico, evento/status, origem, storage/index e eventuais variáveis;
- intervalo de tempo da captura versus janela sugerida; timezone e disponibilidade/retention;
- agregação e fórmula;
- significado de ausência: `No Data`, resultado vazio, série zerada ou zero hits — não tratar como equivalentes.

Primeiro avaliar inconsistências concretas e baratas. Se houver diferença de query/serviço/ambiente, demonstrar seu impacto tecnicamente, mas **não alterar o widget**.

Quando precisar reduzir uma consulta até localizar o filtro que elimina resultados, **projetar uma sequência de testes de leitura manual**: query original → filtro ambiental/fonte confirmado → serviço real → marcador de evento/estado → índice/storage e período, relaxando **um critério por vez**, com limites seguros. O Gemini não deve alegar que executou consultas reais só por ter produzido esse roteiro.

**Guardrails de diagnóstico:** preservar critérios semânticos que distinguem evento de cancelamento, erro de negócio, esgotamento de retries, NACK ou disparo de estorno. Jamais substituir uma consulta por `service:*` sem escopo em PROD; toda expansão de pesquisa deve respeitar serviço, janela, volume, privilégios e custo. Não inferir `ausência do evento` de `No Data`.

## 4. Frente A — três widgets UAT com sinal / PROD sem dados

Para CADA widget, responder:

1. **Qual pergunta operacional ele deveria responder?** Início/baixa efetivamente solicitada, esgotamento de retries ou solicitação de estorno? Distinguir tentativa/encaminhamento local de cancelamento ou liquidação realmente concluídos.
2. **Qual é o evento exato consultado?** Recuperar a expressão literal e a sua condição real de emissão no código privado. Se usar texto de log, verificar se as mensagens mudaram entre versões.
3. **O que diferencia UAT e PROD?** Comparar query, filtro de serviço, `env`, index/storage, intervalo, tags e versão implantada (quando há evidência); procurar parâmetros, operadores e namespaces incorretos. A presença de uma instrumentação geral não prova que essa rota específica foi executada.
4. **O evento é raro ou condicionado a erro?** Especialmente DLQ/esgotamento de retentativas: não aparecer pode significar ausência real do evento. Confirmar existência de tráfego relevante e de sinais correlatos de entrada/retentativa **sem exigir que erros reais ocorram**.
5. **Existe tráfego base da rota em PROD?** Se não for observável, apontar qual evidência/consulta controlada poderia separar `sem requisições` de `requisições sem log/sem indexação`. Verificar se é outro serviço/time que emite o sinal.
6. **Indexação e tempo:** conferir Logs Explorer vs fonte real do widget, status dos índices e retenção/arquivamento acessíveis, event timestamp vs ingestion timestamp, limites de datas, storage tier e permissões. Diferenciar logs de métricas e traces amostrados.
7. **Deploy e instrumentação:** inspeção de código apenas como suporte; não afirmar que algo está em PROD por estar em `main`, nem que falta por causa da ausência de tráfego; não solicitar deploy preventivo sem prova.
8. **Comportamento e títulos:** se a query estiver tecnicamente certa mas o evento for excepcional, propor apenas melhoria futura de descrição/legenda que evite interpretar `No Data` como sucesso ou zero falhas. Não transformar indicador parcial em taxa completa.

Classificar individualmente: `ERRO DE QUERY/FILTRO DEMONSTRADO`, `SINAL NÃO EMITIDO/INSTRUMENTAÇÃO INSUFICIENTE DEMONSTRADA`, `FONTE/INDEX/STORAGE INCOMPATÍVEL DEMONSTRADO`, `EVENTO CONDICIONAL SEM OCORRÊNCIA COMPROVADA`, `TRÁFEGO NÃO COMPROVADO`, `EVIDÊNCIAS INSUFICIENTES` ou outra categoria explicada. **Não declarar causa confirmada sem evidências positivas adequadas.**

## 5. Frente B — dois widgets de consumo Kafka sem sinais em ambos os ambientes

Investigar cada um dos dois widgets de forma própria, sem afirmar que compartilham o mesmo tópico ou mecanismo:

- Determinar se o widget consulta **métrica nativa de Kafka**, série APM, span, log de consumer, métrica customizada ou sinal de infraestrutura. Recuperar nome exato, tags, granularidade, recursos monitorados e semântica de "eventos consumidos".
- Analisar se a instrumentação/integração realmente oferece a métrica ou evento correspondente nos dois ambientes e se as tags pesquisadas existem; distinguir `consumer lag`, `messages in`, `messages consumed`, `commits`, `poll`, `processing success` e tráfego de tópico.
- Checar se o processo/consumer responsável está no perímetro do time; se tópico ou integração são geridos por outro time, registrar dependência e evidência necessária em vez de fingir acesso.
- Diferenciar **sem tráfego**, consumer não implantado/sem inscrição, ausência de instrumentação, erro de tag, índice/retention, métrica não habilitada ou problema de definição do widget. Observar que um gráfico próximo com hits não comprova esse mesmo fluxo.
- Verificar se UAT e PROD têm realmente o fluxo habilitado e onde seria esperado haver consumo. Não criar nova instrumentação ou duplicar métricas como correção automática.
- Quando não houver fonte que permita responder à pergunta do título, registrar explicitamente `COBERTURA NÃO DEMONSTRADA` e propor correção de escopo ou novo discovery direcionado.

**Resultado obrigatório:** separar o que é falha de visualização, lacuna de telemetria, ausência de consumo real ou falta de provas.

## 6. Consultas e testes manuais: entregar só os necessários

Como o Gemini não deve usar a sessão web por meios alternativos nem possui credenciais corporativas autorizadas para API, ele deverá preparar um bloco de **validação manual mínima** no relatório privado, adequado ao que faltar após análise local:

- Para cada um dos três widgets da Frente A, no máximo **duas consultas diagnósticas por ambiente**, derivadas das queries reais e do código, com `env`, serviço/index, intervalo temporal e finalidade explicitados. Agrupar verificações comuns para reduzir passos.
- Para os dois Kafka, usar **Metrics Explorer/APM/Log Explorer**, conforme a **fonte real** de cada widget. Entregar consultas ou filtros exatos da ferramenta correta, não forçar Logs Explorer quando a fonte é métrica.
- Antes de sugerir novos testes, recuperar evidências já existentes; não solicitar novamente validações dos P1 nem exportações já disponibilizadas.
- Se o operador confirmar os resultados, orientar **quais valores observar** (hits, timestamps, tags, série, índices, resultado esperado/ausente), sem pedir dados sensíveis de propostas ou logs completos.
- Classificar todos os passos: `EXECUTADO E COM EVIDÊNCIA`, `PREPARADO, NÃO EXECUTADO`, `SEM DADOS NA JANELA`, `BLOQUEADO POR PERMISSÃO`.
- **Nenhum teste de produção deve gerar erro artificial, tráfego sintético, NACK, reprocessamento, cancelamento ou estorno reais.** Somente leitura; respeitar controles de acesso e retenção.
- O que depende de teste manual deve ficar destacado como pendência; não fabricar resultados nem conclusões automáticas.

Se já for possível descartar uma hipótese por comparação estática confiável, não pedir testes redundantes só para preencher checklist.

## 7. Plano de correção, sem execução

Para cada widget:
`prioridade | causa demonstrada ou hipótese | impacto | correção candidata | tipo (query/configuração/instrumentação/discovery/tráfego) | dependência | risco de regressão | evidência de viabilidade | teste de aceite em UAT | teste seguro em PROD | necessidade de aprovação`.

Dar prioridade às divergências reais UAT × PROD e a falhas que possam gerar cegueira operacional; **não ordenar apenas pelo tamanho do texto "No Data"**.

Manter:
- veto aos filtros que esconderiam falhas sem chave;
- preservação dos 21 widgets atuais e seus indicadores;
- P1 recém-entregues fora do escopo;
- nenhum percentual, taxa ou desfecho de negócio fabricado;
- saneamento visual de avisos separado, salvo se uma descrição incorreta for parte direta da causa diagnosticada.

Se uma correção de query for comprovadamente viável, descrever a **mudança exata como proposta**, com diff conceitual (antes/depois) no relatório privado, **sem editar JSON**. Se exigir código ou infraestrutura, definir descoberta restrita e aprovação separada.

## 8. Entregável e parada

Criar **UM relatório Markdown privado**, número sequencial livre em `darwin-monitor/planos/`, sugestão `43-diagnostico-no-data-prod-e-sinais-kafka-uat-prod.md`, sem sobrescrever artefatos.

Deve conter:
1. resumo executivo com prioridade e riscos;
2. fontes privadas verificadas e estado Git somente leitura;
3. inventário exato dos **cinco widgets** e suas queries por ambiente;
4. **Frente A**: três diagnósticos individuais UAT com dados / PROD sem dados;
5. **Frente B**: dois diagnósticos individuais Kafka sem registros em ambos;
6. tabela de hipóteses testadas vs ainda não testadas, com evidências/citações internas;
7. distinção entre resultados do JSON/código e resultados de Datadog efetivamente observados;
8. consultas mínimas para validação manual por ferramenta e ambiente, com ordem e interpretação;
9. matriz de causa, urgência, plano de correção e critérios de aceite;
10. backlog separado de eventuais melhorias futuras, sem execução;
11. integridade: **zero alterações** em canônicos/JSONs/código/dashboards UAT/PROD e **zero commits/push**;
12. decisão do estado final: `CAUSA COMPROVADA`, `CAUSA PARCIALMENTE DEMONSTRADA` ou `DIAGNÓSTICO DEPENDE DE VALIDAÇÃO MANUAL`, individual por widget.

Na resposta final ao operador, mostrar **uma tabela de cinco widgets** com situação e causa (ou lacuna real), as **duas ou três verificações manuais mais úteis**, caminho do relatório e recomendação objetiva do próximo passo. Não afirmar que PROD está quebrado só porque não houve log de erro, nem que está saudável só porque outros widgets têm dados.

**PARADA OBRIGATÓRIA:** após gerar o diagnóstico e o plano, **NÃO implementar nem publicar qualquer correção**. Aguardar revisão humana e autorização expressa para uma etapa futura.
