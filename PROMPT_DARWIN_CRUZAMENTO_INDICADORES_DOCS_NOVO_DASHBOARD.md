# PROMPT — DARWIN MONITOR: CRUZAMENTO DE INDICADORES COM DOCUMENTAÇÃO DAS APLICAÇÕES E NOVA DEFINIÇÃO DE DASHBOARD

## 0. Contexto e parâmetro

Atue como **arquiteto de software e especialista em observabilidade e Datadog APM**, com capacidade de interpretar documentação técnica e de negócio, analisar indicadores, distinguir evidências de hipóteses e especificar dashboards de forma auditável.

**RAIZ_DOCS:** <diretório-da-pasta-docs>

A raiz acima contém, ou deverá permitir localizar:
- `darwin-monitor/`: documentação já produzida sobre indicadores, planilha de referência, discovery de telemetria, fontes e definições existentes de dashboards;
- outras pastas irmãs: documentação gerada a partir de aplicações, com arquivos Markdown sobre assuntos, domínios, regras de negócio, integrações, endpoints e fluxos.

**Objetivo:** analisar todas essas fontes, cruzar os indicadores da planilha com os indicadores e dashboards já documentados no Darwin Monitor e identificar se as documentações das aplicações revelam **evidências adicionais** que tornem outros indicadores da planilha candidatos à construção de dashboard. Ao final, **criar um novo arquivo Markdown de definição de dashboard**, seguindo o modelo, a linguagem, as seções, o grau de detalhamento e a organização dos dashboards já existentes.

**Execute a investigação e grave o novo arquivo. Não entregue apenas um plano.**

A nova documentação é uma **especificação**, não uma ordem para criar recursos no Datadog.

---

## 1. Regras e limites da tarefa

### Permitido

- Ler os documentos em `RAIZ_DOCS`, incluindo a planilha quando disponível e os arquivos de descoberta técnica já produzidos;
- Inspecionar código-fonte em modo somente leitura, **apenas se já estiver acessível e se a documentação deixar dúvida essencial**, registrando que houve verificação adicional;
- Consultar o Datadog real **somente em modo leitura**, caso exista acesso autorizado e funcional e caso a consulta seja necessária para confirmar a viabilidade atual de um widget;
- Criar **um novo arquivo Markdown** de definição de dashboard dentro de `darwin-monitor/dashboards/`, sem destruir o material histórico;
- Se já existir um arquivo dedicado a esta mesma análise, revisá-lo incrementalmente, sem duplicá-lo e preservando evidências humanas.

### Proibido

- Alterar código das aplicações, dependências, configurações ou instrumentação;
- Alterar os documentos gerados de cada aplicação nesta execução;
- Reescrever indicadores da planilha ou alterar sua fórmula/definição oficial;
- Modificar, excluir ou recriar os dashboards existentes ou seus documentos por conveniência;
- Criar/editar dashboards, métricas, monitores, alertas ou integrações **dentro do Datadog**;
- Inventar consultas Datadog, nomes de services, operations, resources, tags/facets, IDs de negócio, métricas customizadas ou sinais de sucesso;
- Tratar documentação de código como prova automática de existência, retenção, indexação ou disponibilidade da telemetria no Datadog;
- Reaproveitar dados de produção, tokens, credenciais, cookies, IDs reais, payloads pessoais ou informações confidenciais nos relatórios.

**Importante:** não adicione widgets só para "completar" o dashboard. Um indicador não suportado pelas evidências deve permanecer identificado como pendente ou bloqueado.

---

## 2. Fase 1 — Descoberta dos materiais e do modelo existente

Antes de gerar qualquer arquivo:

1. Confirme o caminho real de `RAIZ_DOCS` e localize `darwin-monitor/`.
2. Faça o inventário das pastas irmãs, identificando quais são documentações de aplicações e quais são outros materiais não pertinentes.
3. Leia os índices `README.md`, `INDICE_RAG.md`, `GLOSSARIO.md` e `VALIDACOES_PENDENTES.md` quando existirem, além das páginas de:
   - `assuntos/`
   - `dominios/`
   - `integracoes/`
   - `arquitetura/`
   - `api/`
   - `dados/`
   - documentação operacional e de fluxos.
4. Em `darwin-monitor/`, localize e leia os materiais relevantes, conforme encontrados:
   - indicadores, catálogo e critérios de cálculo;
   - planilha de referência dos indicadores;
   - dashboards e respectivas definições existentes;
   - `discovery/` e `discovery/services/`;
   - fontes e evidências de APM, spans, traces, metrics e logs;
   - lacunas, limitações de instrumentação e próximos passos;
   - estudos anteriores de viabilidade e validações de queries.
5. Descubra o **padrão efetivamente usado nas definições existentes**: nomenclatura, numeração de arquivos, estrutura de seções, tabelas, detalhamento de widgets, consultas, evidências, status e instruções de montagem pela UI do Datadog.

**Não presuma** que os arquivos tenham os nomes dos exemplos acima. Explore a estrutura real.

### Localização da planilha

Procure o arquivo de planilha original ou seus derivados rastreáveis (XLSX, XLS, CSV, Markdown ou exportação estruturada), começando por `darwin-monitor/` e seus documentos de referência. Caso existam várias versões, identifique a mais confiável/atual por data, autoria documental e consistência com os documentos, sem confundir data de modificação com versão de negócio validada.

Se a planilha original não estiver disponível:
- use apenas um catálogo derivado **que preserve fielmente a lista e a definição dos indicadores** e marque a origem como derivada;
- não declare que analisou a planilha diretamente;
- se nem a planilha nem um catálogo confiável estiverem disponíveis, **não fabrique a lista**. Registre o impedimento no resultado e não produza uma definição baseada em um cruzamento que não pôde ser feito.

---

## 3. Fase 2 — Estabelecer uma linha de base (baseline)

Antes de identificar novos candidatos, catalogue os indicadores **já previstos na planilha** e seu estágio documental.

Para cada indicador, identifique:
- nome e identificador oficial, quando houver;
- objetivo de negócio e regra/fórmula de cálculo documentada;
- unidade de medida e dimensão temporal;
- entidade que deve ser contada: proposta, recorrência, evento, pagamento, parcela, requisição técnica etc.;
- condições de inclusão/exclusão;
- sinal de entrada, sinal de sucesso/falha e correlação necessária;
- material Darwin já relacionado;
- existência de definição em dashboard atual;
- implementação no Datadog **somente quando houver evidência**;
- gaps já reconhecidos e necessidade de validação humana.

Separe rigorosamente:
1. **Indicador de planilha já coberto por definição de dashboard**;
2. **Indicador de planilha analisado/documentado, mas ainda sem definição executável**;
3. **Indicador de planilha anteriormente bloqueado por falta de evidência**;
4. **Indicador de planilha ainda não investigado**;
5. **Indicador adicional sugerido pela documentação das aplicações, porém ausente da planilha** — manter **separado** dos indicadores oficiais; não promovê-lo automaticamente a requisito.

Não duplique widgets ou indicadores que já estejam adequadamente cobertos por arquivos de definição existentes. Quando novas evidências puderem melhorar algo já definido, registre como **oportunidade de revisão**, não como um novo indicador.

---

## 4. Fase 3 — Cruzamento com as documentações das aplicações

Analise **todas** as aplicações documentadas pertinentes em `RAIZ_DOCS`, não somente aquelas que já haviam sido foco do Darwin Monitor.

Para cada indicador oficial ainda não coberto, rastreie nas documentações:

- **Assuntos/domínios:** qual processo de negócio poderia produzir o dado;
- **Regras de negócio:** critérios reais para entrada, aceitação, rejeição, sucesso, falha e situação final;
- **Fluxos:** etapas, responsáveis, sistemas envolvidos e ordem dos eventos;
- **Endpoints:** operações expostas, payloads, IDs de negócio, parâmetros, respostas e códigos;
- **Integrações:** produção/consumo de eventos, comunicação entre sistemas e retorno de resultado;
- **Dados:** estados, registros persistidos, entidades, históricos e identificadores de correlação;
- **Resiliência:** retries, redelivery, reprocessamento, duplicações possíveis e idempotência comprovada;
- **Observabilidade já documentada:** resources, operations, services, métricas, logs e tags efetivamente descobertos anteriormente.

### Evidência obrigatória de cada correspondência

Registre a cadeia:

`indicador da planilha → conceito/regra de negócio → documento da aplicação → componente/fluxo de origem (se documentado) → sinal técnico → evidência de telemetria → possibilidade real de mensuração`

Se algum elo estiver ausente, **não preencha por inferência silenciosa**. Marque o elo que falta e explique o impacto.

Para cada correspondência, distinga:
- **COMPROVADO NA DOCUMENTAÇÃO DA APLICAÇÃO**: o comportamento/campo/regra foi documentado com fonte;
- **COMPROVADO NO CÓDIGO**: fonte diretamente inspecionada nesta execução;
- **OBSERVADO NO DATADOG**: já existe evidência de consulta real identificável, com período/contexto, ou uma nova consulta real de leitura foi realizada;
- **NÃO COMPROVADO NO DATADOG**: o sinal consta somente do código/documentação;
- **CONTRADITÓRIO**: fontes divergem;
- **NÃO LOCALIZADO**: nenhuma evidência pertinente encontrada no escopo inspecionado.

Nunca converta "campo existe no DTO", "consta no log do código" ou "endpoint retorna 200" em "atributo indexado no span", "log disponível" ou "sucesso funcional".

### Perguntas de validade semântica

Para cada candidato:
1. **O que exatamente o indicador quer medir?** Volume, taxa, latência, proporção, ocorrência, conciliação ou resultado financeiro?
2. **Qual é a unidade da contagem?** A contagem de spans/requests pode estar superestimada por retries ou múltiplas etapas?
3. **Como distinguir tentativa técnica de evento único de negócio?**
4. **Como identificar o resultado funcional definitivo?** HTTP 2xx não é prova de sucesso de negócio.
5. **Como correlacionar as etapas?** Um ID presente em código não necessariamente é tag pesquisável no Datadog.
6. **Numerador, denominador e filtros realmente representam a fórmula oficial?**
7. **O que falta para configurar a query hoje?** Métrica existente, faceta indexada, tag, correlação, retenção, instrumentação ou validação?
8. **Existe risco de criar um proxy técnico com nome de indicador funcional?** Se existir, separe e renomeie corretamente.

---

## 5. Fase 4 — Matriz de aderência e viabilidade

Inclua no **novo arquivo de dashboard**, antes da especificação dos widgets, uma matriz consolidada. Exemplo de colunas:

| Indicador | Planilha/ref. | Definição atual Darwin | Evidência nas aplicações | Fonte Datadog | Confiança | Situação | Gap | Decisão |
|---|---|---|---|---|---|---|---|---|

Use referências relativas para arquivos Markdown e referências rastreáveis à planilha (aba/linha/célula quando acessíveis, ou ID/linha do catálogo derivado).

### Classificação obrigatória

- **A — PRONTO PARA DEFINIÇÃO/MONTAGEM:** fórmula fiel ao indicador, sinais necessários disponíveis, queries/filtros previamente validados no Datadog real ou revalidados nesta execução, contagem semanticamente defensável e sem gap impeditivo;
- **B — MVP TÉCNICO VIÁVEL:** dados reais permitem um proxy técnico útil, porém **não comprovam o indicador funcional**. Nome, fórmula, unidade e ressalvas devem deixar isso explícito;
- **C — CANDIDATO CONDICIONAL:** documentação nova indica um caminho promissor, mas falta comprovar telemetria, query, facet, tag, ID/correlação ou semântica; pode constar como proposta **não executável / pendente**, sem ser apresentado como pronto;
- **D — BLOQUEADO:** depende de instrumentação, fonte inexistente, validação de negócio ou mudança fora deste escopo;
- **E — JÁ COBERTO:** já há definição equivalente, sem duplicação;
- **F — NÃO RELACIONADO / EVIDÊNCIA INSUFICIENTE:** a documentação não sustenta a associação.

Confiança é uma **estimativa heurística baseada em evidências**, não uma probabilidade estatística. Para interpretações de negócio, **abaixo de 85%** não afirme a regra como estabelecida; registre perguntas e bloqueios. Mesmo acima de 85%, a confiança não substitui a confirmação de disponibilidade da telemetria.

**Regra decisiva:** somente classe **A** pode receber o status `PRONTO PARA MONTAR — TESTADO NO DATADOG`. Classe **B** deve ser chamada explicitamente de MVP técnico. Classes **C/D** não entram no conjunto de widgets prontos.

O resultado do cruzamento deve deixar claro **o que a nova documentação acrescentou** aos estudos existentes, incluindo mudança de status, evidência nova ou contradição encontrada.

---

## 6. Fase 5 — Seleção de outros indicadores para o novo dashboard

Priorize **outros indicadores da planilha** que:
1. Ainda não possuam definição adequada em dashboard;
2. Tenham correspondência real com ao menos uma aplicação documentada;
3. Possuam condições observáveis para mensuração, ou um MVP técnico claramente identificado;
4. Compartilhem propósito, domínio ou fontes e possam formar uma visualização coerente, sem reunir números incompatíveis.

A documentação adicional de aplicações pode revelar indicadores que não estavam relacionados antes. **Não crie escopo de negócio novo automaticamente**: se algum candidato não estiver na planilha, liste-o separadamente como sugestão para futura aprovação, com evidências.

Selecione widgets de classes A e B para a seção principal do novo dashboard. Para a classe C, crie seção separada de **widgets condicionais — ainda não prontos**, somente quando a especificação for útil e a limitação estiver explícita. Não gere consultas exatas especulativas para classes C/D.

Se **nenhum** indicador tiver evidência suficiente, não force uma definição vazia de widgets prontos; crie o arquivo como **análise de viabilidade e bloqueios**, registrando o cruzamento, o que foi observado e o que falta para tornar um dashboard executável.

---

## 7. Fase 6 — Criar o novo arquivo de definição de dashboard

Localize `darwin-monitor/dashboards/` e identifique o **arquivo existente mais adequado como modelo**. Siga sua nomenclatura e estrutura, incluindo numeração sequencial se aplicável.

Crie **um novo arquivo** em:

`RAIZ_DOCS/darwin-monitor/dashboards/<nome-coerente-com-o-padrao-existente>.md`

Exemplo meramente ilustrativo: `<proximo-numero>-dashboard-indicadores-adicionais.md`.

**Não use uma numeração fixa sem consultar os arquivos atuais.** Se já existir um documento dedicado ao cruzamento proposto, atualize esse arquivo de forma incremental; não crie duplicata.

### Conteúdo mínimo obrigatório do arquivo

#### 1. Identificação e objetivo
- Nome descritivo do dashboard/visão;
- Objetivo de negócio;
- Origem dos indicadores: planilha e catálogo Darwin;
- Aplicações envolvidas;
- Público-alvo e perguntas que o dashboard pretende responder;
- Data da análise, fontes e recorte examinado;
- Status do documento e do conjunto de widgets.

#### 2. Fontes e referências
- Planilha: arquivo, abas e referências localizáveis;
- Documentação Darwin que foi usada;
- Documentação de cada aplicação que contribuiu;
- Evidências Datadog já registradas, com datas, e novas consultas realizadas (se houver);
- Ambiguidades e documentos ausentes.

#### 3. Matriz de comparação
- Inventário dos indicadores oficiais pertinentes;
- Cobertura atual;
- Correspondência com as aplicações;
- Situação A/B/C/D/E/F;
- Evidências que justificam a alteração de viabilidade;
- Explicação do que é **novo**, do que já estava documentado e do que continua bloqueado.

#### 4. Desenho lógico do dashboard
- Nome e propósito de cada seção;
- Ordem de leitura e agrupamento coerente dos indicadores;
- Filtros globais sustentados por evidência (por exemplo ambiente, service, recurso), sem pressupor que todas as fontes suportam as mesmas tags;
- Agrupamentos por negócio/tempo e interpretação pretendida;
- Separação visual de indicadores de negócio e métricas técnicas preliminares.

#### 5. Especificação detalhada de cada widget elegível (A ou B)

Para cada widget, siga o padrão existente e detalhe, no mínimo:

| Campo | Definição |
|---|---|
| ID do widget | Identificador estável |
| Indicador oficial relacionado | ID/nome exato na planilha |
| Nome exibido | Semântica fiel ao que se mede |
| Objetivo | Pergunta que responde |
| Classe / status | A ou B; pronto ou MVP |
| Tipo visual | Timeseries, Query Value, Top List etc., se justificado |
| Fonte | APM Metrics, APM Spans, Logs, custom metric etc. **conforme evidência real** |
| Service / env | Nomes confirmados e escopo |
| Operation / resource | Nomes confirmados quando relevantes |
| Tags / filtros | Tags observadas, acessibilidade e valores parametrizados |
| Group by | Apenas dimensões realmente suportadas |
| Agregação | count, avg, sum, count unique etc. com justificativa |
| Fórmula | Numerador, denominador e operações |
| Unidade | %, quantidade, tempo, valor financeiro etc. |
| Janela temporal | Período e granularidade sugeridos e fundamentados |
| Risco de duplicidade | Efeitos de retries, spans, consumers, múltiplas chamadas |
| Evidência negócio/código | Arquivos, regras e símbolos |
| Evidência Datadog | Consulta validada e período/resultado qualitativo |
| Limitações | O que **não** é possível concluir pelo widget |
| Montagem na UI | Passos consistentes com o modelo/documentação existentes |

**Queries e fórmulas exatas:** forneça apenas quando os componentes relevantes forem comprovados e quando a consulta tiver sido efetivamente validada. Diferencie uma definição conceitual da sintaxe real exigida pelo editor do Datadog. Não presuma que um campo pode ser agrupado, filtrado ou usado em cardinality só porque aparece em um payload ou trace.

**Validação de consulta:** para cada widget A, registre, quando disponível:
- Fonte e data da observação;
- Service → operation → resource → atributo/tag;
- Janela consultada;
- Query/configuração de filtros verificada;
- Resultado qualitativo: retornou dados / zero / facet indisponível / erro;
- Particularidades de amostragem, retenção e limites de consulta relevantes.

Se os dados usados são de discovery **anterior**, sinalize claramente a data e a necessidade de revalidação, em vez de apresentar a query como validada hoje.

#### 6. Indicadores condicionais e bloqueados
- Mantenha propostas C/D fora das instruções de montagem imediata;
- Liste dependências de instrumentação, indexação, correlação, métricas, definição de negócio e validação;
- Especifique quais aplicações e fluxos possuem os sinais **no código**, mesmo quando eles não chegam ao Datadog;
- Dê perguntas acionáveis a responsáveis de negócio e observabilidade.

#### 7. Comparação com dashboards existentes
- O que é novo e por quê;
- Indicadores já cobertos que **não** foram duplicados;
- Se há conflito de nomenclatura, semântica ou fórmula com documentos anteriores;
- Quais alterações em dashboards antigos poderiam ser estudadas **futuramente**, sem executá-las.

#### 8. Checklist de implementação futura
- Pré-requisitos de ambiente/datasource;
- Dependências;
- Ordem recomendada para montagem manual dos widgets prontos;
- Como validar semanticamente cada indicador com o responsável;
- Critérios para promover um candidato de C para A;
- Alertas de contagem indevida e interpretação errada.

#### 9. Conclusão e próximos passos
- Indicadores novos aptos;
- MVPs técnicos possíveis;
- Candidatos a investigar;
- Indicadores bloqueados e causas;
- Sugestões **documentais**, não alterações implementadas.

Use **Markdown legível, frontmatter YAML, títulos claros, links relativos, IDs e relações úteis para Obsidian/RAG**, respeitando exatamente a convenção dos arquivos existentes. Use diagrama Mermaid apenas se facilitar compreensão de um fluxo sustentado por documentos.

---

## 8. Atenção especial à realidade de observabilidade

Considere a existência histórica de documentação sobre:
- APM Traces / Spans;
- APM Metrics;
- Operations e resources do Spring/Camel, quando efetivamente observados;
- Gaps de logs;
- Falhas de consulta e timeouts;
- Dificuldade de contar unidades de negócio sem IDs adequadamente indexados.

**Esses pontos são contexto de investigação, não fatos atuais garantidos.** Confira os arquivos de discovery quanto a service, operação, fonte, ambiente, data e evidência. Não reaproveite uma operação de uma aplicação como se pertencesse a todas. Não deduza disponibilidade atual a partir de documentos antigos sem registrar o limite temporal.

Em especial:
- Um evento de mensagem processado não necessariamente corresponde a uma nova recorrência;
- Uma chamada HTTP de sucesso não necessariamente equivale a pagamento confirmado;
- Um span de integração não necessariamente representa uma transação de negócio única;
- Um ID documentado não necessariamente pode ser usado em `count unique`;
- Um erro técnico não necessariamente é cancelamento ou rejeição funcional;
- O fato de existir no banco uma informação não a torna automaticamente consultável no Datadog.

Para qualquer índice percentual, verifique unidade, numerador, denominador, recorte temporal e tratamento de eventos repetidos. Se não houver prova de semântica, o resultado deve ser denominado **métrica técnica/proxy**, não indicador de negócio definitivo.

---

## 9. Revisão final e controle de alterações

Antes de encerrar:

- [ ] Planilha ou catálogo fiel foi localizado; origem e versão estão claras.
- [ ] Pastas de aplicações documentadas foram inventariadas e escopo percorrido está registrado.
- [ ] Indicadores já documentados e dashboards existentes foram comparados.
- [ ] Novos candidatos decorrem de evidência rastreável das aplicações.
- [ ] A matriz de comparação aponta o que mudou em relação ao Darwin existente.
- [ ] Não houve duplicação de widgets já cobertos.
- [ ] Cada candidato tem status A/B/C/D/E/F e justificativa.
- [ ] Nenhum widget foi classificado como pronto sem validação Datadog rastreável.
- [ ] Queries, labels, IDs, unidades, filtros e fórmulas não foram inventados.
- [ ] O novo documento segue o padrão real dos dashboards existentes.
- [ ] Links Markdown/Obsidian, referências, frontmatter e IDs estão consistentes.
- [ ] Apenas o novo arquivo de definição, ou seu correspondente preexistente, foi escrito.
- [ ] Não foram alterados código, documentos das aplicações, planilha nem recursos Datadog.
- [ ] Nenhum segredo ou dado sensível foi transcrito.

## 10. Entrega — execute e reporte

**Grave o arquivo em disco** na pasta `darwin-monitor/dashboards/`.

Depois, apresente um resumo em português brasileiro com:

1. Caminho completo do arquivo criado/atualizado;
2. Quais documentações de aplicações foram lidas;
3. Qual planilha/catálogo foi encontrado e qual a sua origem;
4. Quantidade de indicadores oficiais analisados, já cobertos, recém-viabilizados, condicionais e bloqueados;
5. Quais indicadores adicionais justificaram o novo dashboard e qual evidência sustenta cada um;
6. Quantidade de widgets A (prontos), B (MVP técnico) e C (condicionais);
7. O que ainda precisa ser confirmado no Datadog e/ou com a área de negócio;
8. Quais definições anteriores foram usadas como modelo;
9. Confirmação de que **nenhum dashboard real foi criado ou alterado e nenhum código foi modificado**.

**Resultado esperado:** um cruzamento confiável entre planilha, Darwin Monitor e documentação das aplicações, materializado em **um novo arquivo de definição de dashboard alinhado aos modelos já existentes**, sem transformar hipóteses em métricas falsamente prontas.
