# PROMPT 06M — Auditoria dos Componentes Quantitativos dos Indicadores Executivos

> **Executor:** Gemini CLI, no workspace corporativo autorizado.
>
> **ORDEM DE TRABALHO ALTERADA PELO RESPONSÁVEL:** esta auditoria deve ocorrer **ANTES** da consolidação da documentação de apresentação do monitor. O prompt de documentação/apresentação anterior continua como referência, mas sua consolidação final fica **SUSPENSA** até esta análise e eventual criação/homologação dos widgets quantitativos decididos posteriormente.
>
> **SEGURANÇA:** este arquivo reside em GitHub público e contém somente instruções metodológicas sanitizadas. Não levar ao repositório nomes internos de serviços, queries, atributos corporativos literais, IDs/URLs, códigos, logs, planilhas, artefatos JSON, relatórios técnicos privados, métricas da empresa, valores de proposta ou dados de clientes. Toda leitura e produção detalhada fica no **workspace corporativo local**.
>
> **AUTORIZAÇÃO DESTA ETAPA:** investigação, leitura seletiva de fontes privadas, inventário dos widgets atuais, auditoria do código/telemetria, elaboração de consultas diagnósticas para **execução manual autorizada**, provas locais/offline se houver evidência disponível e elaboração de um **relatório privado completo de diagnóstico e proposta**. **NÃO autoriza** criar/alterar/publicar widgets, importar JSONs, alterar canônicos, dashboards, monitores, métricas, código, instrumentação ou infraestrutura, gerar tráfego artificial, alterar autenticação, comitar ou fazer push. Git exclusivamente local, apenas leitura de status/diffs/histórico.
>
> **CONTEXTO HUMANO:** a planilha original contém indicadores concebidos pela área de Produto, mas o acompanhamento operacional do painel será feito por desenvolvedores. O responsável notou que os componentes quantitativos das fórmulas originais não estão claramente identificados como widgets individuais. **Não confundir a inviabilidade de uma taxa executiva fiel com a inviabilidade de exibir volumes brutos separados.** A pergunta aqui é sobre quatro componentes e suas unidades, não sobre criar quatro novas taxas.
>
> **ESTADO CONHECIDO:** o dashboard possui duas instâncias (UAT e PROD), com um inventário anterior de 21 widgets de dados em cada uma; passou por implantação de correlação estruturada, revisão de filtros que ocultariam falhas, correções de consultas Kafka e saneamento editorial. Recuperar a configuração atual **do JSON canônico/exports mais recentes comprovados**, não dos candidatos antigos nem da memória de outra sessão.

## 1. Questão central — responder sem pressupor a conclusão

A auditoria deve responder, com referência ao levantamento inicial:

**Indicador de sucesso:**
1. O que significa e como medir **volume de recorrências recebidas**?
2. O que significa e como medir **volume de recorrências enviadas**?

**Indicador de falha:**
3. O que significa e como medir **quantidade de adesões com erro**?
4. O que significa e como medir **total de adesões**?

Para **cada** um desses quatro componentes:
- há um widget existente que o mede literalmente, ou só uma aproximação?
- ele já existe como log, trace/APM, métrica, contador, evento ou agrupamento? Com qual unidade e cobertura?
- é possível visualizá-lo em um novo widget **sem alterar código**, mesmo se a taxa final for insegura?
- qual evento define o instante da contagem (entrada, encaminhamento, conclusão local, erro transitório, erro final)?
- como evitar duplicidade por retry, NACK, reentrega e múltiplos logs por transação?
- que evidência demonstraria sua viabilidade em UAT e PROD?
- se não for viável, **o que falta exatamente**: evento, atributo, autorização, dado externo, correspondência de serviço, retenção, qualidade da telemetria ou definição de negócio?

**Não** aceitar frases genéricas como `Datadog não permite`, `Flex não suporta`, `é impossível` ou `instrumentação insuficiente` sem prova técnica específica. Separar limitações da ferramenta, da fórmula, da instrumentação, da semântica de negócio e do escopo do time.

## 2. Fontes e hierarquia de verdade

Ler seletivamente:
1. HANDOFF privado mais recente, para localizar artefatos e decisões;
2. **planilha original** na aba relevante, sobretudo as linhas e colunas de definição/objetivo/fórmula dos dois indicadores;
3. relatórios do discovery **inicial**, planejamento original e decisões posteriores sobre sucesso, falha, denominadores, contagens e rejeição de razões enganosas;
4. canônico UAT atual e export/snapshot PROD mais recente e comprovado; inventário **real** de widgets, queries, fórmulas, agregações, filtros e descrições;
5. relatórios de instrumentação/aderência da correlação por proposta, inclusive limitações observadas em fluxos assíncronos;
6. trechos **pertinentes** do código dos serviços, produtores/consumidores, routers, logs de entrada/resultado local, métricas e configurações; consultar também apenas os relatórios que os referenciam;
7. testes manuais anteriores no Datadog, quando diretamente relevantes e datados, sem afirmar que demonstram os quatro componentes se não foram desenhados para isso.

Estabelecer fonte de verdade:
- **fórmula pretendida:** planilha original e decisões documentadas; preservar redação literal no relatório privado;
- **dados disponíveis:** código + eventos/logs/métricas realmente observados, distinguindo branch, versão implantada e telemetria;
- **configuração vigente:** JSON efetivamente reconciliado, não proposta em planejamento;
- **capacidade da consulta:** amostra ou teste real; sem evidência, `VIABILIDADE NÃO COMPROVADA`.

**Verificar com especial cuidado a ordem exata dos termos da fórmula de sucesso tal como está escrita na planilha** e se a unidade em porcentagem é conceitualmente coerente. **Não inverter por convenção sem aprovação de Produto**, não substituir por uma fórmula mais intuitiva sem declarar a divergência. Para taxa de falha, conferir o que exatamente constitui `erro` e o que entra no `total de adesões`; a redação da planilha pode não equivaler ao universo de eventos observáveis.

## 3. Reconciliar conceitos antes de contar

Antes de qualquer proposta, registrar um **glossário de eventos e entidades** derivado dos sistemas e documentos:
- recorrência, solicitação, adesão, proposta, contrato, processamento, tentativa, mensagem, evento de negócio e registro de log;
- recebimento versus envio: **quem recebe e quem envia**, em qual serviço, direção e fronteira? Pub/Sub, HTTP e downstream não são sinônimos;
- sucesso de handoff local versus aceite/resultado do parceiro ou sucesso do negócio;
- falha transitória, rejeição funcional, falha técnica recuperável, esgotamento e descarte;
- registros únicos vs múltiplos por proposta; novos ingressos vs reentregas; erro reprocessado vs falha definitiva;
- período de emissão, janela de ingestão, eventos tardios, filtros de ambiente e diferenças entre testes automatizados UAT e tráfego de PROD.

**Regra de rigor:** um log de envio não prova necessariamente que uma recorrência de negócio distinta foi enviada com sucesso ao destino; um hit APM não equivale automaticamente a uma adesão; agrupar pelo identificador estruturado de proposta não prova contagem distinta exata no período; não somar cardinalidades distintas por buckets como se fossem entidades únicas em uma janela maior.

**Não confundir `No Data` com zero nem ausência de erro com saúde plena.** É aceitável um componente bruto medir ocorrências de eventos, desde que sua unidade seja explicitamente rotulada. Não rotular `adesões` quando a consulta conta `tentativas`.

## 4. Auditoria reversa dos 21 widgets atuais

Inventariar **todos** os widgets dos dois indicadores (ou relacionados), verificando:
- título, ID e grupo; tipo; fonte; query; compute/agrupamento;
- unidade de medida efetiva; condições de emissão/fluxo; janela e filtros;
- componente da fórmula que pretende representar; existência de proxies/contadores paralelos;
- se hoje aparece número bruto, gráfico de volume, query value, taxa técnica ou correlação;
- por que **não existe** hoje um widget específico para o componente, quando for o caso;
- decisão anterior que o impediu/despriorizou, com fonte e data, ou falta de decisão documentada.

Produzir **uma tabela de correspondência componente → widgets existentes**, permitindo que um componente seja coberto por mais de um widget e que um widget seja apenas complementar. Não marcar `já atendido` se um widget tem sinal parecido mas pergunta de negócio diferente.

**Auditar decisões anteriores sem reabri-las automaticamente:** há histórico de recusa de taxas por quocientes sintéticos e duplicidade assíncrona. Explicar se essa razão inviabiliza:
(A) o percentual executivo fiel; (B) os numeradores/denominadores brutos individuais; (C) um proxy operacional explicitamente rotulado. Não tratar A, B e C como equivalentes.

## 5. Matriz obrigatória dos quatro componentes

Para **cada** componente, elaborar ficha completa com estes campos:

`indicador | componente literal | definição de negócio na planilha | entidade/unidade | fronteira de observação | evento de referência no código | fonte (log/APM/métrica) | condição de emissão | filtro real e ambiente | risco de duplicidade | widget existente e aderência | possibilidade de novo widget | consulta candidata | evidência disponível | limitação | status | teste humano mínimo | decisão recomendada`.

**Status possíveis, sem misturar:**
- `JÁ MEDIDO FIELMENTE EM WIDGET EXISTENTE`;
- `JÁ OBSERVÁVEL, MAS NÃO EXIBIDO ISOLADAMENTE`;
- `OBSERVÁVEL COMO PROXY/CONTAGEM DE EVENTOS, COM RESSALVAS`;
- `VIÁVEL APÓS CONFIGURAÇÃO/QUERY, A VALIDAR`;
- `EXIGE INSTRUMENTAÇÃO OU EVENTO ADICIONAL`;
- `DEPENDE DE DADO/RESULTADO FORA DO PERÍMETRO`;
- `DEFINIÇÃO DE NEGÓCIO AMBÍGUA`;
- `NÃO COMPROVADO COM AS FONTES ATUAIS`.

É permitido ter mais de uma limitação associada ao mesmo componente; escolher **um status principal** e explicar os impedimentos. Distinguir observabilidade da unidade de negócio de disponibilidade do campo em alguns logs.

## 6. Viabilidade da fórmula original, SEPARADA da viabilidade dos componentes

Para cada indicador:
- reconstruir literalmente a fórmula original e os conceitos por trás de cada termo;
- demonstrar se numerador e denominador têm **mesma população, unidade, boundary, tempo de referência e condição de inclusão**;
- avaliar se entrada e saída em fluxos assíncronos devem compartilhar um mesmo lote/coorte em vez de uma janela de relógio;
- distinguir resultado local, eventos de encaminhamento e desfecho externo;
- tratar múltiplos serviços/rotas, retry e sucessos tardios sem contagem duplicada;
- apontar incompatibilidades e possíveis subcontagens, inclusive casos em que a chave estruturada não está presente;
- se houver definição de `adesão com erro` e `total` ambígua, preparar as perguntas precisas para Produto, sem escolher unilateralmente;
- apresentar **três resultados independentes**: (1) viabilidade dos volumes separados; (2) viabilidade de um proxy operacional rotulado; (3) viabilidade da taxa executiva original.

Se o cálculo for inviável hoje, mostrar qual é o **menor conjunto de dados adicionais** necessário e em qual sistema seria produzido. Não reintroduzir taxas anteriormente rejeitadas só porque dois números aparecem em tela.

**Importante:** a operação de contagem distinta sobre logs e a criação de séries de métricas de alta cardinalidade são abordagens diferentes. Não recomendar identificador único como tag de métrica Micrometer; se considerar contagem distinta via consulta de logs, documentar limitações, janela e prova real. Não declarar impossibilidade universal por analogia com o antipadrão de séries dimensionadas por ID.

## 7. Provas, queries e validação manual — focar no que falta

Se houver telemetria/local dataset confiável, fazer testes **somente offline** em cópias ou leitura. Preparar no relatório privado até **uma query candidata principal e uma consulta de controle por componente**, baseadas em eventos reais extraídos das fontes, nos ambientes pertinentes, para execução manual pelo operador quando realmente necessária.

Cada query deve documentar: ferramenta (Log Explorer/Metrics Explorer/APM), filtros de serviço/ambiente/index/storage, intervalo, campo e unidade, resultado que comprovaria viabilidade e problema a investigar se não houver registros.

**Não executar GET/PUT ou pesquisas autenticadas via mecanismos bloqueados**. Não pedir cookies, session tokens, chaves, extração de navegador, scripts evasivos ou acesso administrativo alternativo. Não inventar hits. Em UAT, separar tráfego de testes automatizados de operação PROD; nenhuma injeção de falha, cancelamento, proposta, reprocessamento ou tráfego sintético em PROD será proposta como prova obrigatória.

Caso alguma consulta de controle já tenha sido testada nos documentos anteriores, recuperar a evidência, mas **não superestimar o que ela comprova**.

## 8. Proposta de layout NOVO, mas SEM implementar

Com base na matriz, recomendar **somente os widgets adicionais efetivamente necessários** (de **zero a quatro**, ou menos se algum componente já estiver bem representado).

Para cada candidato:
- nome/título conceitual honesto, indicador e componente;
- Query Value ou Timeseries ou alternativa com justificativa do tipo;
- fonte/evento, unidade (`ocorrências`, `mensagens`, `adesões` somente se possível), dimensões, janela, filtros e ambiente;
- se é contador bruto, proxy operacional ou numerador/denominador executivo fiel;
- descrição com ressalvas sobre retry, ausência de ID ou fronteira externa;
- qual widget atual complementa e qual seria redundante;
- posicionamento recomendado sem comprometer a leitura dos grupos;
- risco, dependência e **critério de aceite em UAT/PROD**;
- estado `PROPOSTO` / `EXIGE VALIDAÇÃO` / `DEPENDE DE INSTRUMENTAÇÃO`; não alegar que está pronto só por schema conhecido.

**Não gerar JSON candidato nem alterar widgets**. É um planejamento para que o responsável escolha, após o diagnóstico, se autoriza a etapa seguinte de construção local. Preservar 21 widgets e vetos de segurança vigentes. Se forem necessários mais de quatro para uma decomposição legítima dos fluxos, explicar por que, mas não ampliar unilateralmente o escopo.

## 9. Relatório privado e decisão dirigida ao responsável

Gerar **UM relatório Markdown privado** no próximo número livre de `planos/` (sugestão `46-auditoria-componentes-quantitativos-sucesso-falha.md`, não sobrescrever existente).

Estrutura obrigatória:
1. resumo decisório em português claro, indicando o que já é possível medir;
2. formulação ORIGINAL de Produto, citada da planilha e eventuais ambiguidades (incluindo a ordem da divisão);
3. glossário de entidades, eventos e fronteira;
4. inventário dos widgets atuais relacionados e decisões que levaram ao desenho;
5. matriz detalhada dos **quatro componentes**;
6. comparação das viabilidades: **volume individual vs proxy vs fórmula executiva**;
7. fontes reais, condições de emissão, riscos de duplicidade e cardinalidade;
8. queries candidatas privadas/testes humanos mínimos necessários e suas condições de interpretação;
9. novos widgets propostos **somente onde há lacuna real**;
10. quadro `OBJEÇÕES HISTÓRICAS DO GEMINI → TESTADAS → CONCLUSÃO REVISADA`, distinguindo evidência de limitação presumida;
11. plano futuro de construção local, homologação UAT e promoção controlada PROD, **aguardando autorização**;
12. perguntas para Produto e Engenharia ainda necessárias, com impacto de cada resposta;
13. estado do Git local e declaração de nenhuma alteração em dashboards, canônicos, scripts, código ou instrumentações;
14. implicações diretas para revisão posterior da documentação de apresentação: quais narrativas/afirmações devem ser atualizadas depois dos resultados.

Na mensagem final:
- apresentar uma **tabela de quatro componentes** com widget atual, estado de viabilidade e recomendação objetiva;
- responder se a ordem da fórmula de sucesso na planilha é coerente ou merece validação com Produto, **sem corrigir automaticamente**;
- informar o número exato de novos widgets recomendados (se sustentado), separando os prontos para construção dos dependentes de sinais novos;
- listar as **mínimas verificações humanas indispensáveis**;
- informar caminho do relatório e quais decisões requerem aprovação do responsável;
- **não declarar a documentação de apresentação como encerrada**: ela será revisada **DEPOIS** da eventual construção e validação desses componentes.

**PARADA OBRIGATÓRIA:** entregar análise e proposta, sem implementar, sem criar/importar JSONs, sem publicar dashboards e sem comitar/push. Aguardar orientação humana antes da construção dos widgets e da revisão final da documentação para apresentação.