# Darwin Monitor — Prompt 03B: Verificação da telemetria do IND-01 no Datadog UAT

## Missão única

Continue a investigação **IND-01 — Índice de Sucesso / Eventos Conta Cartão**, **sem reiniciar a análise de negócio**. O objetivo agora é determinar se os eventos encontrados no código realmente existem nos **logs do Datadog UAT**, descobrir **os filtros reais que funcionam**, e preparar a futura configuração de widgets **sem inventar consultas ou fórmulas**.

**Nesta execução, não publique nem modifique widgets, dashboard, JSON de publicação, scripts Python, instrumentação Java ou monitores.** Este é um trabalho de leitura/validação. Não avance para IND-02.

O projeto Darwin monitora a **fronteira das aplicações sob responsabilidade do time** durante a migração. Um sucesso HTTP local de encaminhamento não comprova efetivação de adesão em sistemas de outros times. **Um widget parcial é uma entrega válida se for nomeado conforme o evento que realmente mede.**

## REGRA DE CAMINHOS — LEIA PRIMEIRO

**A raiz de trabalho é a pasta `darwin-monitor/`**. Antes de qualquer arquivo, identifique o diretório atual (`pwd`/equivalente) e encontre a raiz real pelos artefatos já existentes, por exemplo `uat_darwin_monitor_dashboard.json` e `planos/`.

**TODOS OS NOVOS RELATÓRIOS DEVEM FICAR EM `darwin-monitor/planos/`.** Quando estiver dentro da raiz, use caminhos relativos como `planos/08-validacao-telemetria-ind01-conta-cartao.md`.

**PROIBIDO criar `darwin-monitor/docs/darwin-monitor/planos/` ou `docs/darwin-monitor/planos/` como destino a partir da raiz.** Essa duplicação foi causada por execuções anteriores.

O relatório anterior pode estar no local antigo `docs/darwin-monitor/planos/07-investigacao-ind01-indice-sucesso-conta-cartao.md` **dentro** da raiz `darwin-monitor`, ou já em `planos/07-...`. **Localize e LEIA o arquivo existente; não assuma sua localização.** Não o mova, apague nem sobrescreva nesta execução. Crie o novo relatório somente em `planos/08-validacao-telemetria-ind01-conta-cartao.md` na raiz real. Se o caminho novo já estiver ocupado, use um sufixo incremental sem sobrescrever.

Caso não seja possível identificar a raiz de forma inequívoca, **pare e relate os caminhos encontrados**. Não crie pastas paralelas como tentativa de corrigir o problema.

## 1. Estado inicial já conhecido — hipóteses a validar

A investigação anterior identificou `SolicitacaoRecorrenciaViaPubSub.java` na aplicação `sboot-pseg-base-orch-gestao-eventos-conta-cartao` e apontou eventos candidatos para:

1. Recebimento/início de fluxo assíncrono: trecho de log com `[EVENTO-31-PUBSUB] Iniciando fluxo de recorrência assíncrono para proposta:`.
2. Chamada/encaminhamento ao orquestrador: trecho com `[EVENTO-31-PUBSUB] Chamando orquestrador de recorrência`.
3. Retorno HTTP de sucesso local: trecho com `[EVENTO-31-PUBSUB] Retorno com sucesso do orquestrador de recorrência`.
4. Falha de negócio: evento `[EVENTO-31-PUBSUB][FALHA-NEGOCIO]`.
5. Falha sujeita a retentativa: evento `[EVENTO-31-PUBSUB][RETRY-PUBSUB]`.

**Atenção:** esses textos foram **relatados pelo Gemini, não validados no Datadog**. Consulte brevemente a classe atual para confirmar a string literal de cada log, o nível `INFO/ERROR`, o caminho de execução e sua condição. Não assuma que todos os logs aparecem numa mesma tentativa ou que existem na versão implantada em UAT.

Os resultados anteriores descrevem a cobertura como **PARCIAL** e sugerem dois widgets: volume de recorrências e taxa de sucesso de encaminhamento. Eles ainda **não estão validados em UAT**.

**Não reutilize `sum:trace.log_count{...}` como consulta pronta**: a existência da métrica e sua adequação para Logs não foram comprovadas. A fonte Logs exige a própria estrutura de filtros e agregação.

## 2. Corrigir a compreensão de "Índice de Sucesso"

O requisito original da planilha foi registrado como `Recorrências recebidas ÷ Recorrências enviadas (%)`. **Preserve essa fórmula literalmente como requisito**, mesmo que pareça conceitualmente invertida. Não a substitua automaticamente.

Se desejar propor **taxa operacional de sucesso de encaminhamento**, documente-a **separadamente**, como métrica alternativa, por exemplo `respostas locais de sucesso / tentativas elegíveis`, **somente após validar que o numerador e o denominador têm a mesma unidade e que retries não introduzem distorções inaceitáveis**.

- **Recebida**: comprove o ponto do código (mensagem consumida, validada ou fluxo iniciado?).
- **Encaminhada**: confirme se é início de chamada, retorno 2xx ou outro acontecimento.
- **Confirmada externamente**: não infira efetivação apenas por HTTP 2xx.
- **Duplicidade**: mensagens reenviadas por NACK podem gerar múltiplas entradas; documente o efeito.
- **Denominador zero**: a futura fórmula não deve gerar porcentagem fictícia.
- **Respostas fora da janela**: início e conclusão podem ocorrer em intervalos distintos; não interprete a razão como taxa por proposta sem correlação.

Se os pontos de contagem não forem comparáveis, **não proponha porcentagem nesta etapa**. Volumes separados ou erros locais podem ser a melhor entrega parcial.

## 3. Acesso ao Datadog — sem especulação

A experiência anterior com IND-07 mostrou que a API legada autenticada por cookies permitiu operações de dashboard, mas **não garantiu leitura programática da API de Logs**. O usuário comprovou eventos diretamente no Log Explorer e revelou que atributos daquela aplicação estavam sob `mdc`.

**NÃO presuma que Eventos Conta Cartão tenha o mesmo esquema ou os mesmos campos de venda-digital.**

- Caso exista um meio **já configurado e autorizado** de leitura de logs UAT, pode utilizá-lo em modo estritamente leitura; não mude autenticação nem tente endpoints arbitrários.
- Se o acesso de logs estiver bloqueado, **não insista em novas tentativas**. Prepare testes manuais exatos baseados nas strings do código e deixe os resultados como pendentes.
- Não acesse produção como substituta de UAT, não colete credenciais, não inclua cookies, CSRF, URLs sensíveis ou identificadores de propostas/clientes em relatórios.
- A consulta por `service` e `env` só pode usar nomes confirmados na interface. `service:<nome>` e `env:uat` são exemplos de filtros reservados a testar; **não copie `@env:uat` de documentos antigos sem verificar o esquema real**.
- Confirme se **Include Flex Logs** precisa estar ligado e a janela de consulta utilizada.

## 4. Roteiro de validação manual — curto, um evento de cada vez

Monte **uma sequência com no máximo cinco testes**, nessa ordem, utilizando texto literal verificado no Java:

**Teste A — serviço e tráfego:** pesquisar o serviço/ambiente reais com janela explícita; confirmar que aparecem registros de Eventos Conta Cartão. Se zero resultados, checar serviço, ambiente, armazenamento e janela antes de supor falta de log no código.

**Teste B — recebimento:** adicionar **somente** o trecho literal estável da mensagem de início da rota Pub/Sub. Registrar contagem e um exemplo sanitizado do campo `message` (sem proposta/ID).

**Teste C — retorno local de sucesso:** no mesmo serviço, ambiente e período, buscar o trecho literal da mensagem de retorno com sucesso. Não confundir com o log que apenas anuncia que chamará o orquestrador.

**Teste D — falha de negócio:** buscar marcador de falha se confirmado no Java.

**Teste E — retentativas:** buscar marcador de retry se confirmado no Java.

**Construção da busca:** demonstre a lógica e um **filtro candidato** por teste, mas **não declare sintaxe ou resultados validados se não foram executados**. Marcadores com colchetes, hífens e aspas devem ser testados no Log Explorer, inclusive com frases literais entre aspas quando adequado; não confunda uma pesquisa textual com uma facet estruturada.

**Registre por teste:** texto/atributo exato consultado, fonte, serviço, ambiente, janela, Flex Logs, contagem ou erro, **se executado pelo agente, confirmado pelo usuário ou ainda pendente**.

Ao localizar um registro, inspecione quais atributos realmente aparecem (por exemplo, `message`, `service`, `env`, `mdc` ou outro objeto) e só então proponha filtros por atributos. **Não invente `@mdc.*` sem evidência nessa aplicação.**

**Limite anti-loop:** somente consulta ampla + refinamentos justificados pelos eventos do código. Se o usuário precisar executar manualmente, **pare e peça retorno dos testes em vez de gerar versões sucessivas de query**.

## 5. Conclusão permitida com as evidências atuais

Antes dos testes manuais, pode concluir somente:
- Fluxos e logs candidatos identificados no código (**sujeitos à versão UAT**);
- Telemetria UAT **PENDENTE**, se não houve consulta real;
- Cobertura de negócio **PARCIAL POTENCIAL**, não widget pronto;
- Filtros/consultas agregadas **NÃO VALIDADOS**.

Depois de consultas reais, classifique separadamente:
1. **Log encontrado** e sua semântica no código;
2. **Filtro do Log Explorer validado**;
3. **Contagem/agrupamento da fonte Logs validado**, quando aplicável;
4. **Taxa de sucesso semanticamente válida**, somente com denominador e população comparáveis.

**Uma contagem retornada não prova que o índice original de negócio foi atendido.** Nunca force um KPI integral.

## 6. Saída — UM novo relatório na pasta correta

Crie **somente** `planos/08-validacao-telemetria-ind01-conta-cartao.md` **na raiz `darwin-monitor`**, contendo:

1. **Raiz do workspace confirmada** e caminho do relatório anterior efetivamente lido.
2. **Fonte de cada evento**: tabela curta `classe/método → condição → string literal no logger → significado e limitações`.
3. **Consultas UAT**: no máximo cinco testes, separados em `EXECUTADO PELO AGENTE`, `CONFIRMADO MANUALMENTE PELO USUÁRIO` ou `PENDENTE DE TESTE MANUAL`, sem resultados fictícios.
4. **Campos observados e estrutura real**, somente se confirmados na fonte.
5. **Quadro de cobertura**: requisito original, métrica parcial operacional, risco de retries/duplicidade e status separado de observabilidade/viabilidade de taxa.
6. **Widgets futuros**: no máximo dois candidatos, descritos sem JSON e **sem query agregada inventada**. Incluir apenas query de Logs realmente validada ou etiqueta `PENDENTE`.
7. **Próxima ação única**: pedir ao responsável o menor conjunto de resultados/prints necessários para continuar, quando os testes forem manuais.

**Proibido escrever em `docs/darwin-monitor/planos/`; proibido alterar `uat_darwin_monitor_dashboard.json`, `update_uat_dashboard.py` ou widgets reais.**

## Encerramento no chat

Informe somente:
- Onde encontrou o relatório anterior;
- Caminho correto do novo arquivo na raiz;
- Quais dos testes A–E foram executados e quais dependem do usuário;
- Se há logs comprovados no Datadog UAT;
- Se já existe query de Logs validada para o IND-01;
- **A primeira consulta manual que o usuário deve executar**, sem pedir uma série longa de testes de uma vez.

**Encerre após a entrega.**
