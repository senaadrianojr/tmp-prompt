# Prompt 05A — Auditoria e Planejamento do IND-03: Falhas no Cancelamento

> Uso: iniciar uma nova sessão do Gemini CLI no ambiente corporativo autorizado. Este arquivo contém somente instruções de investigação; nomes completos de aplicações, identificadores, contratos, consultas e evidências devem ser descobertos **nos arquivos locais privados** e não registrados neste repositório público.

## 1. Papel, missão e limites

Atue como **auditor técnico e revisor crítico de observabilidade** do projeto Darwin Monitor. Seu objetivo único é investigar a **viabilidade do indicador IND-03 — Falhas no Cancelamento**, sem construir nem publicar widgets, sem modificar aplicações e sem alterar a configuração remota.

A sequência de trabalho é: **investigação fundamentada → classificação da viabilidade → relatório local → revisão humana → parada**. Outras etapas só podem começar após autorização explícita do responsável.

Não confunda uma conclusão de relatório anterior com uma evidência independente. Diferencie sempre: (a) afirmado pelo responsável; (b) reportado anteriormente; (c) observado no código; (d) observado efetivamente no ambiente de observabilidade; (e) hipótese.

## 2. Contexto mínimo que deve ser preservado

O projeto estabelece uma linha de base de observabilidade durante a evolução de uma arquitetura de serviços e sua integração com processadores externos. Nossos serviços podem controlar apenas parte dos fluxos de negócio. Uma resposta HTTP, um ACK, um retorno local de orquestrador, uma tentativa de processamento ou um log **não são necessariamente** desfechos finais de cancelamento.

Estado de continuidade:
- **IND-02:** consolidação operacional concluída, publicada em UAT e validada manualmente pelo responsável. A fórmula percentual anterior não tinha populações compatíveis; foi retirada, mantendo cobertura operacional parcial sem duplicação. **Não reabrir IND-02** nesta tarefa.
- **IND-01:** possui sinais operacionais de processamento, sucesso local e falhas/retries; isso não equivale a taxa integral por adesão/proposta.
- **IND-07:** possui widgets de origem e volume de operações, inclusive cancelamento. Volume de cancelamentos **não** é contagem de falhas.
- A correlação por identificador de proposta foi implantada nos serviços relevantes. Um dos serviços ainda aguarda **evidência de logs reais pós-deploy** em suas rotas, por falta de tráfego representativo. Não confundir implantação com validação em todos os caminhos.
- A estrutura do dashboard, widgets de status, APM e streams deve ser preservada.
- O ambiente de observabilidade pode ter leitura limitada por autenticação, sessão e períodos sem dados; APM pode ter resultados quando a busca de logs não tiver. **Janela vazia não prova inexistência de eventos.**

## 3. Fontes locais e estratégia de leitura

Localize o workspace privado do projeto, preferencialmente em **darwin-monitor/**, a planilha original de requisitos de monitoramento, os relatórios de auditoria/instrumentação anteriores, os códigos-fonte dos **dois orquestradores envolvidos em conta/cartão e eventos de pagamento** e o JSON local da estrutura do dashboard.

Consulte as partes relevantes, sobretudo:
1. Definição original do IND-03 na planilha, com aba, célula/linha, texto e fórmula **exatos**, quando existentes.
2. Conclusões úteis dos relatórios anteriores sobre identificação de proposta, propagação de MDC/span, isolamento, testes e limites de integração.
3. Topologia local das aplicações, contratos, rotas, exceções e mensagens.
4. Inventário atual de widgets, sem presumir que o JSON histórico representa o estado remoto mais recente.

**Não leia indiscriminadamente todos os documentos ou diretórios de dependências/build.** Use buscas direcionadas e registre precisamente o que foi inspecionado. Se algo não estiver disponível, declare a lacuna. O workspace de relatórios não precisa ser um repositório Git.

**Não transcreva código-fonte, dados de clientes, contratos completos, credenciais, URLs internos, cookies, tokens ou relatórios corporativos para qualquer destino público.** O relatório técnico pedido abaixo será criado **somente no workspace privado local**.

## 4. Permissões

**Permitido nesta execução:** leitura de arquivos e código; buscas estáticas direcionadas; consultas **somente leitura** a logs/APM/metrics já acessíveis por autenticação corporativa autorizada; criação/edição apenas do novo relatório Markdown local especificado abaixo.

**Proibido:** editar Java, testes, scripts, dependências, configurações, instrumentação, JSON do dashboard e publicador; alterar Datadog ou qualquer observabilidade remota; criar/alterar widgets, alertas ou monitores; commits, pushes, PRs, pipelines, deploys, reconfigurações de autenticação ou ações destrutivas. Não criar mensagens artificiais ou simular tráfego. Não silenciar/comentar testes. Não fazer publicação alguma.

Se o arquivo de relatório de destino já existir, **não sobrescrever cegamente**: inspecione e escolha um nome alternativo sem perda de conteúdo. Para consultas, não contorne restrições de acesso.

## 5. Passo A — Validar a definição real do IND-03

Na planilha original, localize a linha/célula correspondente a **Falhas no Cancelamento**. Reproduza fielmente o enunciado e qualquer fórmula, identificando:
- nome, escopo de negócio, unidade esperada e eventuais observações;
- se há numerador, denominador e período;
- quais populações e sistemas estão definidos pelo requisito;
- ambiguidades reais que impeçam uma interpretação única.

**Não presuma percentual, denominador, status final nem população de cancelamentos únicos.** Se a planilha não estiver acessível, registre que o requisito não foi verificado e prossiga somente com investigação técnica de alcance limitado.

## 6. Passo B — Mapear o ciclo do cancelamento nas duas aplicações

Examine em ambos os serviços, sem assumir direções de chamadas:
- pontos de entrada/saída: consumidores, produtores, rotas Camel, handlers, processors, serviços, endpoints e mensageria;
- contratos e tipos de mensagens/eventos; status, respostas, exceções e integrações com terceiros;
- iniciação, processamento local, confirmação de terceiros, falha, retentativa, reentrega, compensação, cancelamento efetivo e estorno;
- limites de responsabilidade: o que nossos serviços podem provar e o que depende de outro time.

Para cada caminho identificado, documente **serviço → operação → condição → sinal → significado → dependência**. Aponte classes, métodos e linhas verificáveis. **Cancelamento e estorno devem ser separados sempre que tiverem semânticas distintas.**

Não aceite como “cancelamento concluído” o simples envio de uma solicitação nem como “falha final” uma tentativa que pode ser reprocessada.

## 7. Passo C — Catálogo de sinais reais

Para cada sinal relacionado a cancelamento, registre:
- arquivo/classe, método/rota e intervalo de linhas;
- condição que dispara o sinal, mensagem/marker e nível do log;
- status/código, evento, métricas/traces e campos de correlação efetivamente disponíveis;
- tipo: falha técnica, falha de negócio, rejeição esperada, timeout, retry, reentrega, sucesso local, desfecho final, estorno ou estado desconhecido;
- observabilidade estática versus observação real em UAT;
- riscos de múltipla emissão e duplicação.

Investigue o **caminho concreto do identificador de proposta** pelos contextos de log e trace, inclusive nos limites async. Descubra o nome e o formato reais dos campos no código e nos dados observados; **não invente nem simplifique prefixos dos campos**. Não presuma cobertura universal só porque há instrumentação em alguns caminhos.

## 8. Passo D — Observabilidade UAT sem inferências indevidas

Se o acesso somente leitura estiver disponível, consulte os sinais descobertos usando Logs, APM Traces e/ou Metrics. Para cada tentativa registre:
- fonte, serviço/operação, query **exatamente utilizada**, filtros, período e resultado;
- campos realmente presentes e evidências representativas;
- se a consulta foi validada com dados, executada sem resultados, apenas proposta ou impossível de executar.

**Jamais produza query fictícia como se tivesse sido executada.** Uma query candidata deve usar apenas campos e semânticas já demonstrados e receber classificação explícita de **não validada**.

Se não houver logs novos pós-deploy ou o acesso remoto for insuficiente, avance com análise estática e contratual; documente as pendências sem interromper a investigação.

## 9. Passo E — Definir unidade contável e avaliar viabilidade

Diferencie **linha de log**, **tentativa**, **mensagem**, **reentrega**, **proposta**, **solicitação de cancelamento única**, **falha terminal** e **cancelamento concluído**.

Responda objetivamente:
1. Que condição prova uma falha? É local, técnica, de negócio ou terminal?
2. Pode haver várias falhas/retries para o mesmo cancelamento?
3. Existe chave confiável de deduplicação? Uma proposta pode ter mais de um cancelamento?
4. O mesmo fluxo é emitido nos dois serviços? Há risco de dupla contagem?
5. Há evidência positiva de conclusão? Quem a possui?
6. A população de falhas e a população total são comparáveis se o requisito pedir taxa?
7. O Datadog consegue deduplicar/agregar essas unidades sem pressupostos falsos?
8. Qual trecho depende de instrumentação ou de outro time?

Se houver evidência suficiente, defina a unidade contável e a regra de agregação. Se não houver, **não invente numeradores, denominadores, percentuais nem métricas de sucesso terminal**. Proponha cobertura operacional parcial fiel ao sinal — por exemplo, falhas locais por operação ou volume de tentativas de retry — somente se realmente suportada por eventos existentes.

## 10. Passo F — Verificar duplicação no dashboard

Inspecione em modo leitura o inventário dos widgets existentes: indicadores de origem/volume, recorrência/falhas/retries, status, APM e streams.

Antes de sugerir qualquer novo widget, explique:
- que pergunta operacional responderia;
- que evento real e população contaria;
- se algum widget já responde à mesma pergunta;
- quais limites e possíveis interpretações enganosas existem.

Não construir widgets nesta fase e não tocar em export, JSON canônico, scripts ou dashboard remoto.

## 11. Passo G — Matriz de evidências e decisão

Monte tabela por **serviço, fluxo, sinal, evidência concreta, unidade contável, risco de duplicação, estado de validação em UAT e limitação**. Use exclusivamente estas classificações:
- **DISPONÍVEL E COMPROVADO**
- **DISPONÍVEL MAS NÃO VALIDADO EM UAT**
- **PRECISA DE INSTRUMENTAÇÃO**
- **DEPENDE DE OUTRO TIME**
- **INVIÁVEL COM OS DADOS ATUAIS**

Separe evidência de código, relatórios anteriores e dados de observabilidade; não chame de comprovado em UAT um sinal somente existente no código.

Apresente a conclusão por fluxo, podendo coexistir:
- cobertura integral viável;
- cobertura operacional parcial viável;
- dependência de instrumentação;
- dependência de outro time/fonte;
- inviabilidade atual.

Se recomendar instrumentação, explique **o que falta e por quê**, sem alterá-la.

## 12. Único entregável gravável

Criar somente no workspace privado local:

**darwin-monitor/planos/22-auditoria-viabilidade-ind03-falhas-cancelamento.md**

Estrutura mínima do relatório:
1. Resumo executivo e nível de confiança.
2. Requisito original, fonte (aba/célula), texto e fórmula — ou lacuna.
3. Fontes e arquivos efetivamente consultados.
4. Fluxos, contratos e limites de responsabilidade por aplicação.
5. Catálogo de eventos, logs, status, falhas, retries, cancelamento e estorno.
6. Correlação, identificadores, idempotência, reentrega e dupla contagem.
7. Evidência estática com classes/métodos/linhas.
8. Evidência UAT com consultas exatas, janelas, resultados e limitações.
9. Análise de unidade contável, numerador/denominador (quando cabível).
10. Inventário de widgets existentes e risco de duplicação.
11. Matriz de evidências e classificação por sinal.
12. Decisão de viabilidade, proposta proporcional de cobertura.
13. Pendências, bloqueios e critérios concretos para a etapa 05B.
14. Controle de alterações (somente relatório local).

## 13. Critérios objetivos de aceite

A conclusão deve deixar inequívoco:
- definição original conferida ou indisponibilidade registrada;
- duas aplicações investigadas, com fronteiras entre serviços e terceiros;
- cada evento relevante respaldado por evidência verificável;
- erros técnicos, negócio, rejeições, retries, cancelamento e estorno distinguidos;
- unidade contável e possibilidade real de deduplicação examinadas;
- taxas consideradas **somente** quando as populações forem compatíveis;
- evidência em UAT separada de código/hipótese;
- widgets existentes comparados antes de propor outros;
- matriz, decisão e pendências registradas;
- relatório local criado e relido para checagem;
- nenhuma outra alteração local ou remota realizada.

Itens inconclusos devem permanecer **pendentes**; não marcar tudo como aprovado por conveniência.

## 14. Resposta final e parada

Após gerar e reler o relatório, resuma no terminal:

**A.** Requisito original e fonte.
**B.** Achados por serviço e fronteira de responsabilidade.
**C.** Sinais existentes versus efetivamente validados em UAT.
**D.** Viabilidade integral/parcial, riscos de duplicação e justificativa.
**E.** Opções para 05B, ainda não implementadas.
**F.** Pendências e verificações futuras.
**G.** Caminho do relatório local.
**H.** Arquivos criados/alterados e confirmação explícita de ausência de mudanças remotas.

**PARE E AGUARDE REVISÃO HUMANA.** Não execute 05B, não publique nada, não edite dashboard ou código e não faça commit/push/deploy.

**Critério central:** produzir conhecimento auditável e decisão fundamentada — nunca métricas artificiais ou mudanças remotas não autorizadas.
