# PROMPT 06P — Documentação Definitiva dos Indicadores, Justificativa dos 25 Widgets e Preparação da Apresentação

> **Executor:** Gemini CLI no workspace corporativo autorizado.
>
> **OBJETIVO:** produzir a **documentação definitiva da versão atualmente homologada do Darwin Monitor** e um **guia completo para o responsável apresentá-lo** a engenharia, Produto e liderança. Esta é uma etapa exclusivamente documental. Aproveitar as especificações da etapa de documentação anterior, mas atualizar integralmente as conclusões à luz dos quatro contadores acrescentados e da consolidação posterior.
>
> **SEGURANÇA — REPOSITÓRIO PÚBLICO:** este prompt é apenas um método de execução. Nunca publicar nele nem em GitHub dados da instituição, nomes reais de serviços, queries, identificadores de widgets/dashboards, URLs privadas, código, logs, planilhas, arquivos internos, números operacionais, capturas ou relatórios. **TODOS** os documentos produzidos ficam exclusivamente no workspace privado/local.
>
> **ESTADO ATUAL INFORMADO PELO RESPONSÁVEL:** o operador importou e testou **quatro novos contadores operacionais** em UAT e PROD, constatando números coerentes e visualizações funcionais. **Uma consolidação local equivalente ao trabalho que seria feito na etapa 06O já foi executada por iniciativa do operador**, documentada em relatório privado numerado **48**, com canônico UAT reconciliado, export PROD arquivado, backups, atualização do handoff e inventário de **25 widgets de dados em cada ambiente**, distribuídos em quatro grupos. **NÃO executar novamente o 06O, não refazer importações nem reconstruir arquivos JSON**. Verificar essas informações nas fontes privadas antes de afirmar qualquer detalhe técnico.
>
> **ORDEM APROVADA:** a etapa de auditoria dos componentes quantitativos e construção/homologação dos contadores ocorreu **ANTES** da documentação definitiva. **Nenhuma fórmula percentual foi criada ou aprovada nesta entrega**. A discussão de taxas fica futura e depende de consistência da população/unidade e decisão do responsável. O foco da apresentação é demonstrar os números individuais e a capacidade de identificar problemas no fluxo.
>
> **INTERPRETAÇÃO OPERACIONAL RATIFICADA PELO RESPONSÁVEL:** o monitor será acompanhado **principalmente por desenvolvedores**. Seu valor é detectar e investigar erros técnicos, erros funcionais, gargalos, retentativas, falhas de integração, processamento e saúde dos componentes da equipe. No recorte **LOCAL**, solicitação encaminhada e retorno positivo do parceiro constituem sucesso da responsabilidade daquele componente; retorno negativo/erro local observável é falha do fluxo correspondente. Não exigir comprovação de liquidação externa para reconhecer êxito local, mas não afirmar que isso equivale ao sucesso fim a fim do negócio. Distinguir falhas funcionais de erros técnicos e reconhecer tentativas/transações ainda pendentes.
>
> **AUTORIZAÇÃO:** leitura, análise e escrita de documentação Markdown **NOVA** no workspace privado; inspeção somente de leitura dos JSONs, relatórios, planilha e trechos de código necessários para fundamentar afirmações. **NÃO** editar dashboards nem canônicos/exports/JSONs, mudar widgets, queries, fórmulas, descrições, scripts, instrumentação, infraestrutura, métricas, monitores, ou executar qualquer publicação/deploy. Git apenas local para leitura de status/diff/histórico; **zero commits/push/ações destrutivas**.

---

## 1. Fontes obrigatórias e padrão de evidência

Antes de redigir, abrir **as fontes reais**:
1. HANDOFF privado atualizado após a consolidação dos quatro contadores, incluindo sua seção de estado final;
2. relatório de consolidação **48**, seus arquivos canônicos e snapshots/exports efetivamente validados de UAT e PROD;
3. relatório de construção dos quatro contadores e auditoria quantitativa anterior, inclusive semântica de eventos e decisões de não aplicar fórmulas;
4. prompt e eventuais documentos gerados na etapa original de documentação para apresentação, se existirem; aproveitar o trabalho já feito sem copiar dados obsoletos;
5. planilha original com os indicadores, fórmulas e objetivos, incluindo **linhas selecionadas e não selecionadas** e os motivos documentados;
6. discovery inicial, decisões de cobertura, homologação UAT, publicação PROD, instrumentos de correlação, auditoria de `No Data`, correções Kafka e saneamento editorial, **somente nos trechos relevantes**;
7. código/configuração de emissão de sinal **somente se indispensável** para determinar o que um widget mede, não abrir nova auditoria Java completa.

**Hierarquia de verdade:**
- objetivo original de negócio: planilha/decisões aprovadas;
- estado e configuração atual dos widgets: canônico/export **mais recente** comprovado pelo relatório 48;
- semântica do sinal: query real + código/reportes rastreáveis;
- funcionamento em UAT/PROD: testes e afirmações datadas do operador, **não** mera existência de JSON;
- ownership: registrar o que foi informado pelo responsável, sem inventar escalas ou acordos formais entre equipes.

Cada afirmação técnica substantiva deverá apontar **arquivo local, seção/linha/decisão e, quando relevante, data**. Se um documento anterior disser algo superado pela instrumentação ou pelas correções mais recentes, indicar a **versão vigente** e a razão. Dados faltantes e hipóteses serão marcados explicitamente; não preencher com números fictícios.

**Não iniciar outra reconciliação dos dashboards.** Apenas confirmar por leitura o estado reportado, registrando divergências se encontradas.

## 2. Mensagem central para a apresentação

Elaborar uma narrativa acessível, porém rigorosa:

**"O Darwin Monitor parte de indicadores solicitados por Produto e os traduz em sinais operacionais que os desenvolvedores conseguem observar e investigar dentro da responsabilidade dos componentes. Nem toda fórmula de negócio é reproduzível no APM; por isso, o painel mostra volumes, sucessos locais, falhas, saúde da integração e evidências para troubleshooting, preservando as limitações reais."**

Explicar:
- **o que Produto queria saber** e por que esses indicadores surgiram;
- **o que os serviços permitem observar** em entrada, execução, envio, retorno, retentativas, Kafka e erro;
- **por que os widgets escolhidos são úteis aos devs** mesmo quando o indicador de negócio não pode ser reproduzido integralmente;
- **o que o painel não comprova**: desfechos externos sem sinal correspondente, reconciliação histórica financeira, deduplicação não demonstrada, taxas executivas ainda não homologadas;
- **como usar o painel no cotidiano**: quando olhar, o que comparar, como distinguir problemas de tráfego de falhas pontuais, o que fazer após observar uma anomalia;
- **como o dashboard poderá amadurecer com o uso real**, revisando widgets redundantes e incluindo novos somente por necessidade comprovada — sem executar essas mudanças agora.

Escrever em tom profissional: não acusar Produto nem a Engenharia e não vender cobertura além da evidência. Diferenciar **responsável pela definição do indicador** de **equipe que observa e investiga sinais operacionais**.

## 3. Rastreabilidade completa dos indicadores executivos

Reconstruir a partir das **linhas efetivamente selecionadas da planilha** todos os indicadores cobertos ou avaliados (inclusive os de cobertura parcial ou dependência externa). **Não inventar IDs, fórmulas ou classificações; verificar a versão do relatório mais recente.**

Para cada indicador, documentar:
- título, linha de origem e **pedido literal/objetivo da planilha**;
- fórmula original *quando existir*, sem trocar numerador e denominador;
- pergunta que se desejava responder e público consumidor;
- o que o monitor realmente consegue responder **na fronteira local**;
- widgets associados (incluindo complementares e de saúde) e por que foram escolhidos;
- quais componentes de volume agora aparecem isoladamente nos novos contadores e quais continuam apenas proxies;
- limites matemáticos e funcionais, denominadores indisponíveis, evento repetido, status não conclusivo e dependências externas;
- contribuição **estratégica** (decisão ou visibilidade efetivamente viabilizada) e **técnica** (investigação possível e fonte real);
- qual ação um desenvolvedor pode tomar quando a leitura chama atenção;
- classificação da aderência: `DIRETO`, `PARCIAL`, `PROXY OPERACIONAL`, `COMPLEMENTAR`, `NÃO COBERTO/DEPENDÊNCIA EXTERNA`, com justificação;
- o que seria necessário para evolução futura, sem afirmar que será executada.

**Atenção especial aos indicadores de sucesso e falha:** documentar a decisão do responsável de **exibir volumes absolutos isolados primeiro**, sem criar fórmulas nesta versão. A relação possível entre total, sucesso e erro só se aplica a uma população bem definida, com resultados mutuamente exclusivos e sem dupla contagem; não confundir contador de falhas funcionais com todas as falhas técnicas + negócio, nem solicitações digitais com o denominador da recorrência se forem universos distintos. **Não transformar a documentação em mais uma contestação de escopo**: explicar de forma objetiva o valor dos contadores entregues e a cautela com taxas futuras.

## 4. Inventário e fichas INDIVIDUAIS dos 25 widgets existentes

Recuperar a ordem visual real e o inventário dos JSONs atuais. Último estado informado: **quatro grupos e 25 widgets de dados em UAT e PROD**; conferir antes de apresentar como final. Não contar note/group como widget de dados.

Entregar:
1. **Uma matriz de rastreabilidade de 100% dos widgets**:
   `Grupo | Widget/ID real | Tipo | Fonte | Unidade | Indicador(s) relacionado(s) | Pergunta que responde | Papel | Por que este widget | Benefício para dev | Limitação | Estado UAT/PROD | Evidência`.
2. **Uma ficha por widget**, com estrutura padronizada:
   - o que ele apresenta na tela, em palavras simples;
   - condição/evento e **unidade realmente contada**;
   - motivo da escolha do tipo de visualização (toplist, query_value, timeseries, stream etc.), distinguindo justificativa histórica de inferência;
   - qual parte do indicador original **resolve**, **complementa** ou **acrescenta**;
   - contribuição estratégica e técnica defendida por dados;
   - exemplo de utilização para **detectar ou investigar erro de negócio, erro técnico, gargalo, volume, integração ou saúde**, sem afirmar que houve incidente;
   - como interpretar valores altos/baixos/ausentes; em especial `No Data` versus zero;
   - o que **não** se pode concluir por causa da limitação da fonte;
   - status/validação real em UAT e PROD com data quando documentado;
   - ação investigativa inicial e sinal correlato a consultar, respeitando as fronteiras do time.

**Cobertura obrigatória dos quatro contadores recém-entregues**:
- contador do volume de contratações digitais;
- contador de mensagens/recorrências recebidas na esteira;
- contador de sucessos locais;
- contador de falhas funcionais.

Explicar por que eles melhoraram a leitura em relação aos antigos gráficos temporais e como a equipe pode **comparar volumes de entrada, retorno e falha** sem forçar uma taxa ou somar eventos que não representem a mesma unidade.

**Outros pontos indispensáveis**:
- widgets Kafka corrigidos: a fonte de hits APM/consumo e seu limite como proxy, não equivaler automaticamente a quantidade de liquidações/conclusões financeiras;
- correlação por identificador estruturado: valor para rastreabilidade, ranking de atrito e stream especializado, mas não ocultar logs sem campo, nem alegar contagem distinta exata de propostas sem comprovação;
- segregação por processadora/origem sem converter essa divisão em prova de resultado externo;
- eventos condicionais de erro, estorno e DLQ: UAT frequentemente executa testes automatizados, enquanto PROD pode não ter a mesma ocorrência; **`No Data` não prova sucesso ou saúde plena**;
- indicadores de monitoramento de sucesso/erro técnico: retorno local não é resultado financeiro final;
- avisos editoriais superados e saneados: explicar o estado VIGENTE, não repetir o diagnóstico antigo de instrumento aguardando deploy;
- excluir de qualquer interpretação um percentual incidental de screenshot que o responsável expressamente declarou não ser parte dos widgets em foco.

Se algum widget não tiver vínculo claro com um indicador, explicar por que ele permanece como **suporte transversal de saúde/diagnóstico**, ou marcar como candidato para avaliação futura de utilidade, sem apagá-lo.

## 5. Estrutura visual e manual operacional para desenvolvedores

Explicar a composição e o motivo da ordem dos grupos, conforme títulos reais dos dashboards. A apresentação deve demonstrar **como o painel pode ser usado na prática**, não virar leitura de 25 cards sem contexto.

Produzir:
- mapa de leitura sugerida: **panorama/volumes → processamento e sucesso local → falhas de negócio e técnicas → saúde/retentativas → cancelamento/estorno/Kafka → rastreamento por proposta**, adaptado à disposição real;
- tabela `sintoma/hipótese | widgets para comparar | o que confirmar | como interpretar | próximo passo de investigação`;
- pelo menos **cinco jornadas curtas** se existirem sinais suficientes: tráfego cai, falhas aumentam, sucesso local cai diante de entradas, retentativas/esgotamento, Kafka sem sinais, divergência por processadora ou outro exemplo documental relevante;
- como não confundir erro temporário recuperado com falha definitiva; como eventos tardios/duplicados podem mudar comparações;
- uso de janelas temporais, filtros e ambiente sem afirmar semelhança automática de UAT e PROD;
- papel do Log Explorer, APM e evidências adicionais como suporte, sem exigir exportação de dados sensíveis;
- limite de responsabilidade: quando dev investiga o componente, quando aciona outra equipe ou precisa de BI/Produto;
- quadro objetivo de **"observação → hipótese → validação → ação"**, sem simular incidentes reais nem instruir alterações em PROD sem autorização.

**Não criar runbooks de correção com comandos destrutivos.** O foco é interpretação e triagem.

## 6. Governança: Produto definiu os indicadores, dev acompanha

Esta seção tem papel central:
- distinguir **requisito de visibilidade de negócio** de **monitoramento técnico acionável**;
- explicar como a decomposição em widgets operacionais evita falsa precisão e ajuda a equipe que de fato acompanhará o monitor;
- propor uma divisão de responsabilidades, **indicada como PROPOSTA e não acordo já estabelecido**, entre Produto/Seguros, Engenharia/Dev, Observabilidade/Infra e BI ou parceiros;
- mapear `pergunta de negócio | quem define | sinal técnico | quem acompanha | quem pode agir | dependência externa | escalonamento`;
- apontar os acordos ainda necessários: frequência de acompanhamento, responsável pela triagem, criticidade e limiares, canal de notificação, critérios de incidente, revisão de queries e de descrições, gestão de backlog;
- não alegar que há monitores, alertas automáticos, on-call, escalas ou SLA se não estiverem formalmente criados;
- destacar que **a próxima fase de alertas automáticos é planejamento posterior**, não capacidade já entregue.

## 7. Roteiro de apresentação técnico-executiva

Criar um segundo documento autônomo, focado no responsável que precisa apresentar o painel:

**A. Roteiro completo de 10–15 minutos**
- sequência clara em 6 a 8 blocos com tempos estimados e **fala sugerida pronta** em linguagem natural;
- audiência mista (devs, Produto, liderança), com ênfase no uso de dev;
- demonstrar diretamente os grupos e os quatro novos contadores de volumetria;
- destacar 2–3 percursos úteis de navegação real, sem mostrar proposta/cliente identificável nem depender de um evento excepcional presente na hora;
- prever fallback caso widget condicional esteja `No Data`.

**B. Resumo de 90 segundos**
- relato objetivo da necessidade, abordagem, entregas operacionais e limitações, sem jargão excessivo.

**C. Perguntas difíceis e respostas seguras**
Incluir, com respostas apoiadas em evidência:
- Por que os indicadores originais de Produto não são todos calculados como porcentagens?
- O que os novos quatro contadores acrescentaram?
- O que significa sucesso ou erro **dentro do nosso componente**?
- Como sei se falha técnica e falha funcional estão sendo tratadas separadamente?
- Por que entradas, sucessos e erros não são sempre somáveis automaticamente?
- Por que UAT mostra eventos de DLQ/estorno e PROD pode não mostrar?
- `No Data` quer dizer ausência de falha?
- Hits Kafka representam quantidade de faturas liquidadas?
- Como o identificador de proposta permite investigar o fluxo?
- Por que um widget permanece mesmo quando sua métrica raramente tem registros?
- Se Produto definiu indicadores, quem decide se há um incidente e quem atende?
- O que falta para alertas automáticos e métricas executivas mais fiéis?
- Qual é a fronteira do que o time consegue observar, e o que depende de sistemas externos?

**D. Limitações e posicionamento**
- 5 frases tecnicamente seguras para usar durante a apresentação;
- 5 afirmações a evitar (taxa não validada, causalidade não comprovada, conclusão externa, `No Data` como zero, contagem de entidade distinta etc.);
- checklist de preparação para reunião (janela do Datadog, filtro de ambiente, dashboard real, cenário de demo, não expor logs sensíveis);
- decisões pontuais a solicitar da liderança/Produto/Dev após a demonstração, sem impor um processo não homologado.

**Não gerar slides/pptx/PDF nem apresentações prontas nesta etapa.** O pedido atual é documentação e guia de fala; eventual material visual vem mediante autorização futura.

## 8. Entregáveis, estrutura, qualidade e parada

Produzir **dois documentos Markdown privados**, com nomes únicos e estáveis no espaço documental existente:

1. **Documento principal:** `darwin-monitor-documentacao-analitica-indicadores-e-25-widgets.md` — referência completa com sumário, matriz por indicador, inventário de 25 widgets, fichas individuais, valor técnico/estratégico, interpretação de saúde/erro, jornadas de investigação, limitações, governança proposta, glossário, índice de fontes e backlog separado.
2. **Guia de apresentação:** `darwin-monitor-guia-apresentacao-tecnica-e-executiva.md` — roteiro 10–15 min, versão 90 s, sequência de demonstração, perguntas/respostas e checklist.

Usar diretório compatível com o workspace (não sobrescrever arquivos anteriores); gravar caminhos reais e links relativos navegáveis entre os documentos. Se houver rascunhos da etapa documental anterior, aproveitar com revisão crítica, criando **novas versões** sem alterá-los automaticamente.

**Critérios obrigatórios de aceite:**
- 100% dos indicadores originalmente selecionados mapeados ou claramente declarados como não cobertos;
- **25/25 widgets de dados reais** explicados individualmente, ou divergência do inventário documentada sem improviso;
- quatro novos contadores plenamente incorporados à narrativa, com unidade e fronteira corretas;
- demonstrar `o que resolve, complementa, acrescenta, limita e orienta o dev a fazer`;
- distinção clara Produto (origem da pergunta) × Engenharia (uso operacional) sem inventar ownership formal;
- nenhum percentual calculado ou validado por suposição;
- nenhum `No Data` apresentado como saúde comprovada;
- não confundir hits de consumo, mensagens, tentativas, propostas distintas e resultado de negócio externo;
- justificar benefícios estratégicos e técnicos sem métricas de impacto fictícias;
- citar fontes internas reais para os elementos essenciais; marcar inferências e pontos sem comprovação;
- não recriar o dashboard, não reabrir 06O, não iniciar a etapa de alertas, não modificar o handoff sem pedido;
- relatório de **estado Git local antes/depois** e declaração de somente dois documentos novos, sem outras alterações.

**Ao finalizar**, informar ao responsável:
1. títulos e caminhos reais dos dois documentos;
2. quantos indicadores foram cobertos e quantos widgets foram examinados;
3. as três contribuições mais defensáveis do monitor aos desenvolvedores;
4. as três limitações mais importantes para a apresentação;
5. pontos que precisam de alinhamento com Produto/Engenharia, sem travar a entrega documental;
6. eventual lacuna documental genuína, com origem e evidência;
7. confirmação de ausência de publicação, alteração remota, alterações Java/JSON/canônicos e commits.

**PARADA OBRIGATÓRIA:** produzir somente a documentação privada e aguardar revisão humana. Não fazer novas consultas remotas invasivas, não alterar widgets, não importar/publicar, não criar fórmulas ou monitores, não editar os artefatos consolidados e não iniciar nova etapa sem autorização.
