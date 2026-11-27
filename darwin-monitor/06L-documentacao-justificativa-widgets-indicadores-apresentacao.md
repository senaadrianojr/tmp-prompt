# PROMPT 06L — Fundamentação dos Widgets, Aderência aos Indicadores e Guia de Apresentação do Monitor

> **Executor:** Gemini CLI no workspace corporativo autorizado. **Tarefa exclusivamente DOCUMENTAL.** O objetivo é explicar e defender tecnicamente o desenho do monitor existente diante de uma apresentação, não construir mais widgets nem iniciar a etapa de monitores/alertas automatizados.
>
> **SEGURANÇA:** este arquivo está em repositório PÚBLICO e contém apenas instruções sanitizadas. Não enviar para o repositório público nomes internos, queries reais, logs, IDs de dashboards/widgets, URLs privadas, código-fonte, capturas, métricas reais, documentos corporativos ou dados de clientes. Todos os novos documentos solicitados devem ser gravados **somente no workspace privado/local**.
>
> **CONTEXTO COMUNICADO PELO RESPONSÁVEL:** os indicadores originalmente recebidos no levantamento foram formulados pela área de **Produto**, mas a **operação cotidiana do dashboard caberá principalmente aos desenvolvedores**. O responsável precisa **APRESENTAR** a solução e explicar de forma convincente, inteligível e tecnicamente honesta como as escolhas de widgets atendem, complementam ou apenas aproximam as necessidades executivas e o que a equipe de desenvolvimento poderá acompanhar e fazer com esses sinais. Isso muda a abordagem da documentação: distinguir claramente a pergunta de Produto, o instrumento de observabilidade e a ação operacional possível. A origem da demanda é informação prestada pelo responsável; **não supor que uma atribuição formal de ownership/plantão já foi acordada**.
>
> **ESTADO INFORMADO:** houve discovery inicial e decisões sucessivas, construção e promoção do dashboard, instrumentações, duas visualizações auxiliares de correlação, correções de consultas Kafka e revisão editorial dos avisos; o responsável informou atualização/validação de exports e estado pronto para continuidade. Confirmar a versão atual nos arquivos locais antes de citar valores, títulos ou quantidade. O último inventário conhecido tinha quatro grupos e 21 widgets de dados por ambiente; não tomar essa contagem como fato novo sem conferir os JSONs.
>
> **AUTORIZAÇÃO:** ler seletivamente documentos e fontes locais; gerar **documentação explicativa completa** e **roteiro de apresentação** em arquivos privados Markdown; conferir integridade e referências dos documentos. **NÃO** modificar dashboards/JSONs, títulos/descrições de widgets, canônicos, código, queries, monitores, métricas, instrumentações, pipelines ou configurações. **NÃO** publicar/importar/efetuar deploy. Git exclusivamente local, somente leitura de `git status`, `git log`, `git show` e `git diff`: nenhum commit, add, push, reset, checkout, restore, stash ou ação destrutiva.

## 1. Missão e pergunta principal

Produza uma explicação sólida que responda à pergunta:

**"Os indicadores foram propostos por Produto, mas o acompanhamento será feito pelos desenvolvedores. Por que estes widgets foram escolhidos, o que cada um efetivamente monitora, como se relacionam aos indicadores originais, quais limitações possuem e como apoiam uma operação e investigação técnica responsável?"**

O resultado deve servir para:
1. **apresentar** o monitor a pessoas de Produto, engenharia e liderança;
2. **orientar devs** na leitura dos sinais e na decisão sobre o que investigar;
3. **justificar decisões arquiteturais**: substituição de KPI impraticável por evidência operacional honesta; uso de telemetria de etapas locais; fragmentação de fluxos; novos widgets de diagnóstico; limites de atribuição de responsabilidade;
4. **mostrar lacunas sem desvalorizar o trabalho**: cobertura parcial, necessidades externas, ausência de denominadores, sinais condicionais, etc.;
5. **evitar interpretações erradas**: contar hits de APM como transações de negócio, somar retentativas como propostas, confundir encaminhamento com liquidação externa, tratar `No Data` como zero ou saúde plena.

Não é um trabalho de marketing nem uma justificativa pós-fato. **Se um widget não tem aderência clara à pergunta original ou o título exagera o que ele mede, explicite a lacuna com evidência.** Não invente benefícios estratégicos.

## 2. Fontes obrigatórias e hierarquia de evidências

Comece **lendo integralmente o handoff privado mais recente** produzido pela etapa anterior. Em seguida, inspecione **diretamente**, de forma seletiva, as FONTES PRIMÁRIAS indispensáveis:
- planilha original de indicadores e definições do levantamento, incluindo colunas de objetivo, recorte, origem e comentários;
- relatórios e decisões do **discovery inicial** e especificações do dashboard ideal;
- decisões posteriores que mudaram ou restringiram o escopo, especialmente fórmulas rejeitadas, limites do handoff local e indicadores classificados como parciais;
- matriz de rastreabilidade do projeto, se existir;
- JSON canônico UAT atual, último export/snapshot PROD realmente publicado e seus títulos/descrições/queries finais;
- relatórios de instrumentação e de aderência do identificador de proposta;
- relatórios de correções de telemetria, publicação/validação, saneamento editorial e handoff;
- código somente quando imprescindível para provar o evento emitido e a fronteira funcional — **não reabrir discovery de código inteiro**;
- histórico Git local apenas para esclarecer versões divergentes.

**Fonte da verdade:**
- intenção do indicador: planilha e decisões aprovadas;
- configuração do widget: JSON REAL atual do ambiente, não texto de um candidato antigo;
- semântica da fonte: documentação técnica, evidências de código e validações confirmadas;
- status de funcionamento: evidência datada (teste do operador, export, registros), jamais mera existência de uma query;
- responsabilidades: documentação de equipes/ownership quando existir; informação do responsável pode ser citada como declaração operacional, **não como compromisso formal de Produto ou de outra equipe**.

Todo ponto importante deve ter **referência privada rastreável** (`arquivo + seção/trecho + data/versão quando relevante`). Sem fonte, classificar explicitamente como `HIPÓTESE`, `INFERÊNCIA` ou `PENDENTE DE VALIDAÇÃO`, não como fato confirmado. Se o handoff divergir de artefatos mais atuais, registrar a divergência e fundamentar a seleção da evidência vigente.

## 3. Reconstrução conceitual: demanda executiva → observabilidade dos desenvolvedores

Reconstruir TODOS os indicadores/linhas selecionados no levantamento original, sem inventar ou renumerar IDs nem pressupor que todos eram originalmente KPIs calculáveis em Datadog.

Para CADA indicador, descrever:
- pergunta original da área de Produto em português natural (ou citar/parafrasear fielmente a fonte);
- fórmula, numerador/denominador, unidade e horizonte esperados, **se efetivamente definidos na fonte**;
- qual resultado de negócio depende de etapas/sistemas externos e não pode ser afirmado pelo monitor do time;
- o que o recorte observável dos serviços realmente permite medir;
- quais widgets foram escolhidos e por que uma visualização só era insuficiente ou um único KPI seria matematicamente enganoso;
- o que cada widget **resolve**, o que **complementa**, o que **acrescenta além do indicador original** e o que **NÃO resolve**;
- nível de aderência: `DIRETA`, `PROXY OPERACIONAL`, `COMPLEMENTAR DIAGNÓSTICO`, `INSTRUMENTO DE SAÚDE`, `NÃO COBERTO / DEPENDÊNCIA EXTERNA`, sempre com explicação;
- limite e risco de interpretação errada, incluindo cobertura parcial;
- valor **estratégico** (decisão, visibilidade, priorização, redução de tempo de detecção/diagnóstico, governança) sustentado pelo sinal, sem inventar ganho de tempo ou dinheiro;
- valor **técnico** (onde procurar, serviço/evento/fonte, separação por fluxo, correlação, retry, falha, DLQ, origem, Kafka/APM etc.);
- como um desenvolvedor utilizaria a informação (gatilho de atenção, consulta de confirmação, comparação com widgets correlatos, hipótese a verificar, próximo passo permitido);
- quem deveria consumir a informação: dev/operador e, se aplicável, Produto/BI, distinguindo responsabilidade de monitoramento da propriedade do indicador;
- situação atual versus futura evolução necessária para cumprir a ambição executiva, sem reclassificar objetivos parcialmente cobertos como plenamente atingidos.

Cobrir honestamente indicadores sem widget de aferição de resultado de negócio: explicar o que o monitor parcial **não é** e qual evidência externa seria necessária. Não preencher lacunas com taxas sintéticas.

## 4. Inventário INDIVIDUAL de TODOS os widgets e justificação da escolha

Ler o JSON efetivamente atual e inventariar TODOS os widgets de dados existentes (última contagem conhecida: 21, incluindo os 2 auxiliares), na ordem visual real dos grupos. Não confundir note/group com widget de dados.

Produzir uma **matriz de rastreabilidade completa**:
`grupo | widget exato/ID | indicador(es) a que se relaciona | tipo/fonte | unidade medida | pergunta respondida | papel (direto/proxy/complemento/saúde) | por que essa visualização | benefício técnico | benefício estratégico | limitações | estado comprovado | ação sugerida ao dev | fonte/evidência`.

E, para **cada widget**, uma ficha explicativa suficientemente concreta:
1. **O que vejo na tela**: título, visualização, principais dimensões/contagens/séries;
2. **Por que ele existe**: problema do indicador ou lacuna de observabilidade que motivou a escolha;
3. **Por que esse tipo de widget** (query value, timeseries, toplist, list stream etc.) foi apropriado para a pergunta; se motivo original não for documentado, dizer `justificativa técnica inferida` e avaliar criticamente;
4. **De onde vem a informação**: família de fonte (logs, traces/APM, Kafka, métricas) e evento/medida; queries literais no apêndice privado se úteis, não obrigatoriamente no texto executivo;
5. **Qual a unidade real**: hits, log, tentativa, proposta rastreável, mensagem, descarte, transação local, série temporal — sem chamar de operação concluída algo que não seja;
6. **O que resolve/complementa/acrescenta** relativamente ao indicador executivo;
7. **Como o dev interpreta e age**: dois exemplos condicionais típicos — sinal presente/alto e `No Data`/baixo — sem criar cenários fictícios que impliquem incidentes reais;
8. **Limitações e pontos cegos**, recorte de serviço, eventos raros, ausência de sistema externo, contagens infladas por retry, qualidade de instrumentação, retenção/indexação, etc.;
9. **O que não é permitido afirmar** em apresentação;
10. **Prova/status atual por ambiente**, quando existente, com data e distinção entre export, validação visual e hipótese.

Os dois widgets auxiliares adicionados para correlação por proposta **não devem ser descritos como cumprimento integral das fórmulas executivas**; esclarecer por que agregação de ocorrências de erro/retry por identificador e stream especializado ajudam a investigar, sem filtrar a visão bruta que retém eventos sem chave.

Os dois widgets de Kafka corrigidos precisam ser explicitamente discutidos: fonte APM e recurso de consumo real, validação da query correta no Datadog, limitação entre `hits` e contagem de operações financeiras. Não transformar a correção já feita em nova atividade.

Widgets de erro/estorno/DLQ com ausência de registros precisam de explicação rigorosa: eventos condicionais podem aparecer em UAT por testes automatizados e não aparecer em PROD, mas isso não prova ausência universal de falhas. Se tiver algum widget historicamente com `No Data`, não omitir.

**Ponto de qualidade editorial:** não acrescentar interpretação de um indicador percentual mostrado incidentalmente em captura que o responsável já declarou ser outro widget e pediu desconsiderar.

## 5. Documentar a escolha do agrupamento visual e o fluxo de leitura do dashboard

Explicar a arquitetura de informação do dashboard:
- por que os grupos foram organizados por origem/processadora, acompanhamento de processamento/sucesso local, saúde operacional e cancelamento/conciliação (utilizar nomes exatos existentes);
- ordem ideal de leitura para dev (panorama → evidência de execução → erro/anomalia → investigação por proposta → fronteira/escalação);
- widgets complementares que devem ser **lidos em conjunto**, com exemplos concretos ancorados nos sinais reais;
- diferença entre `visão executiva solicitada` e `cockpit operacional da equipe de engenharia`;
- efeitos do recorte por processadora/ambiente, filtros e janela temporal;
- como distinguir anomalia, evento raro, ausência de tráfego e ausência de telemetria;
- por que algumas linhas da planilha exigiriam Data Warehouse/BI e não podem ser resolvidas por APM/logs.

Apresentar uma **matriz de jornadas de investigação** (mínimo 4, se houver sinais reais suficientes): sintomas observados → widgets que consultar → interpretação → checagens adicionais → ação técnica/escalonamento possível; não prescrever execução em PROD sem autorização.

## 6. Modelo operacional real: indicadores originados em Produto, acompanhamento por desenvolvedores

Esta seção é **obrigatória e central**, não um rodapé:
- explicar por que um indicador formulado por Produto é um **requisito de visibilidade**, não automaticamente um **runbook acionável para devs**;
- separar responsabilidades de definição do resultado de negócio, operação dos sistemas, validação dos dados, escalonamento e evolução do KPI;
- formular proposta de governança **não formalizada**, diferenciando `quem acompanha`, `quem pode agir`, `quem valida significado de negócio`, `quem resolve dependência externa`;
- para cada grupo de widgets, sugerir explicitamente **o que um desenvolvedor deveria observar**, o tipo de investigação que pode conduzir e quando depende de Produto, outro time, parceiro ou BI;
- explicar quais visualizações são **reativas** (investigação quando houver suspeita) e quais poderiam dar suporte a **monitoramento ativo** caso alertas sejam implementados depois;
- não afirmar que já existe escala, SLA, thresholds, pager, on-call ou contrato de ownership se não houver fonte;
- registrar a situação comunicada pelo responsável: Produto originou os indicadores, mas a equipe dev fará uso operacional; isso **não implica julgamento ou desqualificação de Produto**;
- propor uma **tabela de acordos pendentes** para tornar a operação sustentável (dono de monitor, jornada de observação, comunicação, thresholds, critérios para abrir incidente, limite do que dev consegue provar).

**Não decidir esses acordos pelo responsável**. Identificar o que ele deve levar à reunião como decisão a combinar.

## 7. Guia de apresentação: técnico, claro e defensável

Além da referência analítica, gerar um **roteiro pronto para apresentar em 10–15 minutos**, com audiência mista (Produto, engenharia, liderança):

- mensagem principal e narrativa sugerida: necessidade original → perímetro técnico → interpretação fiel dos KPIs → grupos → exemplos de valor prático → limites → próximo estágio;
- agenda em 6–8 blocos, com duração aproximada, frases-chave e pontos de demonstração ao vivo nos widgets reais (sem depender de log sensível, valores voláteis ou exposição de proposta individual);
- **roteiro de fala simples**, não só tópicos, e uma versão ultraexecutiva de ~90 segundos;
- sugestões de 2–3 percursos de demonstração pela UI, com fallback caso um widget de evento excepcional esteja `No Data` no dia;
- seção `Perguntas difíceis e respostas objetivas`, cobrindo, no mínimo:
  - "Por que não reproduz exatamente a taxa pedida por Produto?"
  - "O que significa cobertura parcial?"
  - "Por que UAT tem registros e PROD não em alguns widgets?"
  - "Como sei que não é um dashboard quebrado?"
  - "Os números de Kafka são transações/faturas concluídas?"
  - "O que mudou com a instrumentação do identificador de proposta?"
  - "Qual a diferença entre uma tentativa, uma mensagem e uma proposta?"
  - "Se o dev vai acompanhar, quem define o que exige reação?"
  - "O que permanece fora do nosso domínio?"
  - "O que ainda falta para termos alertas automáticos?"
- **frases seguras para dizer** e **frases a evitar**, ligadas a evidências reais; não disfarçar ausência de KPI ou afirmar resultados não observados;
- lista curta de pontos de alinhamento a solicitar ao final da apresentação (governança, monitoria, ownership e evolução dos KPIs).

**Não gerar slides nem material externo ainda**: usuário pediu documentação para compreender e apresentar; o roteiro será Markdown privado pronto para adaptação posterior.

## 8. Entregas privadas, integridade e revisão

Gerar **dois documentos Markdown privados** no espaço de documentação do projeto, adotando nomes exclusivos e compatíveis com o diretório real (criar subpasta local de documentação somente se adequado ao padrão existente):
1. **DOCUMENTAÇÃO ANALÍTICA PRINCIPAL** — título sugerido `Darwin Monitor — Rastreabilidade entre Indicadores Executivos e Widgets: Finalidade, Limitações e Uso Operacional`. Conteúdo: enquadramento, matriz por indicador, matriz completa dos widgets, fichas individuais, guias de investigação, limitações, modelo de uso por dev, glossário de termos, fontes e backlog.
2. **GUIA DE APRESENTAÇÃO E PERGUNTAS** — título sugerido `Darwin Monitor — Roteiro de Apresentação e Defesa Técnica`. Conteúdo: roteiro 10–15 min, versão 90 s, ordem de navegação, exemplos de interpretação, respostas difíceis, decisões de governança a alinhar e fontes de apoio.

Adicionar **um índice de navegação e referências cruzadas**, com caminhos reais dos dois documentos; não alterar o handoff anterior por padrão. Se o modelo recomendar incluí-los no índice do handoff, apenas mencionar a ação futura no resultado, não sobrescrever o documento sem autorização específica.

### Critérios de aceite documentais

- todos os indicadores selecionados na planilha mapeados — inclusive os sem cobertura plena;
- **100% dos widgets de dados atuais** inventariados e explicados individualmente;
- cada widget responde claramente: `por que existe / o que mede / o que adiciona / limitação / como o dev age`;
- separação explícita entre utilidade para Produto e **uso operacional da equipe dev**;
- benefícios estratégicos **sem números fabricados** ou promessas de resultado externo;
- evidências documentais rastreáveis a fontes reais; divergências antigas documentadas, não apagadas;
- nenhum `No Data` interpretado como `saúde comprovada`;
- nenhum hit de consumo ou log de retry confundido com resultado final ou entidade única;
- roteiro suficiente para alguém que não participou do discovery explicar o monitor com precisão;
- se algo estiver sem fonte, rotular como indeterminado em vez de preencher com opinião.

Ao terminar, informar no terminal:
- caminhos exatos dos dois documentos e número de indicadores/widgets efetivamente cobertos;
- as **três principais limitações** a explicar ao público, fundamentadas em documentos;
- as **três contribuições operacionais** mais defensáveis;
- os pontos que exigem acordo entre Produto e engenharia;
- qualquer lacuna real de documentação;
- Git local antes/depois, com lista de arquivos novos e **zero alterações em dashboards, canônicos, código, configurações e artefatos existentes**.

**PARADA OBRIGATÓRIA:** executar somente documentação, apresentar entregas e aguardar a revisão do responsável. Não criar monitores, não editar widgets, não publicar, não atualizar Git nem iniciar a etapa técnica seguinte.