# DARWIN MONITOR — ETAPA 03/04
## Consolidação Documental dos Indicadores e Planejamento dos Dashboards

### OBJETIVO E RESPONSABILIDADE

Atue como engenheiro sênior de software, observabilidade e documentação técnica.

**Execute a Etapa 03 — Consolidação Documental.** A Etapa 02 foi aprovada com pendências documentadas. Seu trabalho é incorporar suas evidências aos documentos canônicos existentes, eliminar contradições entre definições e deixar claros o estado atual, o MVP documental, o dashboard-alvo, as instrumentações eventualmente necessárias e os impedimentos de negócio.

**Não faça novo discovery amplo. Não implemente código. Não crie, edite ou exclua dashboards/widgets/monitores reais no Datadog. Não arquive ou exclua documentos nesta etapa.**

Use como entrada principal `REVALIDACAO_TECNICA_ETAPA_02.md`, corrigido após revisão independente. O relatório é **registro do diagnóstico, não fonte de verdade superior ao código, às planilhas ou aos dados realmente observados no Datadog**.

A finalidade é produzir documentação **útil para implementação e estimativa técnica**, sem substituir evidência por suposições.

### 1. PREPARAÇÃO OBRIGATÓRIA: CONFIRMAR A RAIZ E O ESTADO DO PROJETO

1. Confirme o diretório corrente e a estrutura real. Este agente pode iniciar já em `darwin-monitor/`; **não crie `docs/darwin-monitor/` dentro de `darwin-monitor/`**.
2. Leia:
   - `./REVALIDACAO_TECNICA_ETAPA_02.md` (versão corrigida);
   - `./AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md`;
   - `./README.md`, `./01-arquitetura-e-fontes.md`, `./gaps-e-proximos-passos.md`, **somente se existirem**;
   - `./README_FERRAMENTAS_AMBIENTE.md` apenas para conhecer os recursos já comprovados;
   - os documentos relevantes de `indicadores/`, `dashboards/` e `planos/`.
3. Identifique a convenção existente de documentação (Markdown, tabelas, referências cruzadas, padrão de arquivos, seções e terminologia). **Preserve o padrão em vez de inventar uma estrutura paralela.**
4. Verifique os caminhos reais de todos os arquivos antes de planejar a edição. **Não crie substitutos para arquivos não localizados**; registre qualquer divergência de caminho.
5. Consulte o estado Git em leitura, se disponível, para não sobrescrever alterações preexistentes sem avaliá-las; **não faça commit/push**.
6. Antes de escrever, monte internamente um mapa `arquivo existente -> seções a alterar -> evidência de apoio -> efeito no escopo`. Trabalhe a partir desse mapa.
7. Não recatalogue novamente todos os artefatos históricos; limite a leitura aos itens relevantes para os seis indicadores e documentos transversais que realmente necessitem de correção.

### 2. ESCOPO FUNCIONAL E LIMITES

**Quatro indicadores prioritários de responsabilidade SEGUROS**, conforme a leitura da planilha na Etapa 02:

| Indicador | Aplicação(ões) informada(s) |
|---|---|
| Índice de Sucesso | Gestão Eventos Conta Cartão |
| Taxa de Falha | Gestão Eventos Conta Cartão |
| Taxa de Falha no Cancelamento | Gestão Eventos Conta Cartão e Gestão Eventos Pagamento |
| Taxa de Reprocessamento | Gestão Eventos Conta Cartão |

**Dois indicadores mantidos como pendentes de decisão**, não implementáveis nesta entrega sem confirmação de escopo:

| Indicador | Aplicação indicada | Situação |
|---|---|---|
| Parcela Cobrada × Parcela Lançada | Gestão Eventos Pagamento | `Responsável = bvseg` reportado; inclusão na squad de Seguros depende de PO/Negócio |
| Origem | Venda Digital | `Responsável = bvseg` reportado e regra funcional ambígua; depende de PO/Negócio |

**Não exclua os dois indicadores do inventário**, não os marque como tecnicamente impossíveis e não crie entregáveis de implementação para eles como se estivessem aprovados. Preserve seus documentos existentes como registro técnico com status de pendência claramente visível.

Para `Origem`, não conclua que o significado é necessariamente Pismo/DXC, canal de venda ou MOB. Separe as hipóteses e exija definição funcional do Negócio. `Processamento de Seguros — MOB` é outro conceito até prova contrária.

**Nenhum indicador fora desses seis poderá ser incorporado ao escopo ativo**, ainda que esteja documentado em arquivos antigos. Os demais documentos apenas poderão receber marcação de contexto histórico/fora da entrega, se isso for necessário para evitar contradição nos documentos transversais. A movimentação para arquivo pertence à Etapa 04.

### 3. CLASSIFICAÇÕES DA ETAPA 02 — NÃO ALTERAR SEM EVIDÊNCIA NOVA

A Etapa 02 corrigida concluiu, em termos documentais:

1. **Índice de Sucesso:** depende de query/widget adequado ao sucesso *funcional*. O MVP anterior que mede HTTP 2xx não comprova sucesso de negócio. Referências anteriores incluem Evento 31/status 8 e logs de início/sucesso; conferir os detalhes na fonte existente.
2. **Taxa de Falha:** existem indícios documentados de logs de falha funcional, por exemplo `[EVENTO-31][FALHA]`. A ausência de widget não prova necessidade de instrumentação; a lacuna indicada é configurar/validar consulta e visualização.
3. **Taxa de Falha no Cancelamento:** há métrica de falhas definitivas, mas **a taxa completa não foi comprovada**, especialmente por ausência de query/denominador correspondente.
4. **Taxa de Reprocessamento:** há sinal relativo a DLQ/retries, mas **a taxa completa não foi comprovada**, especialmente por ausência de query/denominador e definição de população/deduplicação.
5. **Parcela Cobrada × Parcela Lançada:** pendente de decisão `bvseg` e possivelmente de fontes de conciliação adicionais.
6. **Origem:** pendente de decisão `bvseg` e regra de negócio.

Essas afirmações servem como **ponto de partida a conferir na documentação atual**. Não transforme rótulos `calculável hoje` de relatórios antigos em conclusão atual. Distinga sempre:
- lógica identificada no código;
- log/evento que a aplicação aparentemente emite;
- telemetria efetivamente vista no Datadog;
- query documentada mas ainda não executada;
- query que retornou dados;
- indicador de negócio calculado de forma correta;
- proposta de instrumentação sem validação.

Não use nomes de logs, classes, IDs de widgets ou queries **sem recuperá-los das fontes verdadeiras**, nem os invente.

### 4. ARQUIVOS PRIORITÁRIOS DE CONSOLIDAÇÃO

O plano corrigido da Etapa 02 apontou os arquivos abaixo como existentes. **Confirme sua existência e conteúdo antes de editar**:

- `indicadores/01-recorrencia-indice-de-sucesso.md`
- `indicadores/02-recorrencia-taxa-de-falha.md`
- `indicadores/06-cancelamento-taxa-falha-cancelamento.md`
- `indicadores/04-recorrencia-taxa-reprocessamento.md`
- `dashboards/02-dashboard-mvp.md`
- `indicadores/08-postagem-parcela-cobrada-vs-lancada.md`

Verifique também, quando existirem e forem pertinentes:
- `dashboards/01-indice-sucesso-recorrencia.md`
- `dashboards/03-dashboard-alvo.md`
- `dashboards/04-gaps-de-instrumentacao.md`
- `dashboards/README.md`
- `planos/01-instrumentacao-cdpropostaseguro.md`
- `01-arquitetura-e-fontes.md`, `gaps-e-proximos-passos.md` e `README.md`
- documento existente de `Origem`, **localizado pelo conteúdo e confirmado pelo nome real**, sem assumir que o arquivo de `MOB` é seu documento.

**Princípio de edição:** atualizar somente arquivos preexistentes que de fato precisem da consolidação. Se uma definição já estiver correta, preservá-la. Evite criar novos documentos para explicar o que deveria constar nos documentos canônicos.

**Proteção:** `AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md`, `REVALIDACAO_TECNICA_ETAPA_02.md` e `README_FERRAMENTAS_AMBIENTE.md` devem permanecer inalterados. São evidências/relatórios de entrada e um guia de ferramentas, não saídas da Etapa 03.

### 5. CONSOLIDAÇÃO DAS QUATRO DEFINIÇÕES CANÔNICAS

Em cada documento de indicador de **SEGUROS**, mantenha o estilo atual e garanta conteúdo verificável para:

**A. Definição funcional conhecida**
- objetivo do Negócio e fórmula aprovada ou pendente;
- numerador e denominador, população elegível, estados contabilizados, exclusões e janela;
- diferença entre contagem bruta de eventos e percentual/taxa;
- periodicidade, agregação e thresholds somente quando realmente definidos por Negócio;
- questões funcionais não resolvidas, com dono da decisão.

**B. Fluxo e fontes técnicas**
- aplicações responsáveis e limite de responsabilidade de cada uma;
- repositório/arquivo/classe/método, evento, log, persistência ou integração relevante, quando referenciados no diagnóstico;
- correlação, retries, DLQ, estados finais, idempotência e risco de contagem duplicada, se aplicáveis;
- a fonte real ou provável de cada lado do cálculo, identificando a certeza de cada afirmação.

**C. Observabilidade atual**
- o que foi efetivamente consultado no Datadog e o que consta apenas nos documentos/código;
- query/filtro reais **somente quando recuperados e comprovados**; sinal, serviço e período quando conhecidos;
- ID/link interno do dashboard/widget somente se real e confirmado;
- resultado observado, cobertura e limitações;
- NÃO usar `No Data` como prova de inexistência de eventos.

**D. MVP documental e evolução**
- o que pode ser medido **agora** sem mudanças de código, sob quais ressalvas;
- o que precisa apenas de query/widget;
- o que demanda log/evento/métrica adicional, **somente se a lacuna for justificada**;
- o que depende de banco, BI, outra aplicação, permissão ou regra funcional;
- definição do estado-alvo de forma verificável, com dependências.

**E. Prova e rastreabilidade**
- para fatos: referência em código, planilha ou Datadog;
- para hipóteses: rótulo explícito de `HIPÓTESE / A VALIDAR` e responsável;
- para limitações: impacto concreto sobre cálculo, dashboard, monitor ou alerta.

**Não reproduza relatórios inteiros dentro de cada documento:** use resumos canônicos e links relativos que funcionem.

### 6. PONTOS ESPECÍFICOS POR INDICADOR

#### Índice de Sucesso
- Corrigir documentação que equivalha HTTP 2xx a sucesso da operação de seguro.
- Separar numerador de sucesso funcional e denominador de operações elegíveis.
- Conferir no código e na documentação a associação ao Evento 31/status 8 e logs de início/sucesso; **não afirmar observação no Datadog sem consulta real registrada**.
- Se os logs já contiverem o que é necessário, documentar **configuração de query/widget**, não nova instrumentação por padrão.
- Registrar o MVP de sucesso técnico como **parcial e com nome que não induza à interpretação funcional**.

#### Taxa de Falha
- Registrar os logs de falha funcional apontados pelo diagnóstico, se confirmados nos documentos/código.
- Construir a definição de query desejada **sem alegar que já foi validada** quando não foi.
- Distinguir falha definitiva de tentativa transitória e identificar denominador elegível.
- A inexistência de widget não é evidência de necessidade de instrumentação.

#### Taxa de Falha no Cancelamento
- Registrar explicitamente numerador de falhas definitivas e lacuna para denominador de cancelamentos elegíveis.
- Documentar integração entre Conta Cartão e Pagamento, eventos pendentes e retries se sustentado.
- Não rotular a taxa como calculável enquanto sua fórmula, população e dados não forem demonstrados.
- Separar widget de contagem de erros de widget de taxa percentual.

#### Taxa de Reprocessamento
- Registrar que DLQ pode representar falha/encaminhamento, e não necessariamente reprocessamento efetivo.
- Distinguir tentativas totais, operações únicas reprocessadas e sucesso pós-retry.
- Documentar denominador necessário e deduplicação/correlação.
- Não converter uma contagem de DLQ em percentual de reprocessamento sem regra aprovada e evidência.

### 7. DOIS INDICADORES PENDENTES (`bvseg`)

**Parcela Cobrada × Parcela Lançada**
- Preservar o documento técnico existente e as fontes previamente identificadas, sem prometer implementação.
- Marcar claramente: `FORA DA IMPLEMENTAÇÃO APROVADA / PENDENTE DE DECISÃO DE RESPONSABILIDADE`.
- Documentar lacunas de conciliação entre fontes, sem inventar acesso a banco ou BI.

**Origem**
- Localizar o documento correto antes de alterar qualquer coisa; se só houver um documento MOB, **não renomear nem reutilizar MOB como Origem**.
- Preservar a informação confirmada sobre Venda Digital ou Pismo/DXC, quando houver evidência, como capacidade técnica e não regra funcional aprovada.
- Registrar perguntas objetivas para PO/Negócio: `Origem de quê?`, `canal da contratação ou via do pagamento?`, `qual universo/agrupamento?`, `por que a responsabilidade consta como bvseg?`.
- Se não existir documento canônico de Origem, registrar a pendência no documento transversal de escopo apropriado já existente; **não criar novo documento sem aprovação**.

Os dois devem permanecer rastreáveis e considerados em backlog/pendências, mas não compor o MVP dos quatro de Seguros.

### 8. DASHBOARD MVP, DASHBOARD-ALVO E GAP DE INSTRUMENTAÇÃO

**Atualize os arquivos existentes** de `dashboards/` pertinentes, distinguindo claramente:

**MVP (cenário atual):**
- apenas widgets/consultas fundamentados em dados efetivamente observados ou viáveis sob hipótese explicitada;
- etiqueta de `OPERACIONAL VALIDADO`, `CONFIGURÁVEL, NÃO VALIDADO`, `PARCIAL` ou `BLOQUEADO`;
- nome/objetivo, fonte, sinal, query (se confirmada), janela, unidade, fórmula, limitações;
- destaque para métricas de contagem que **não são taxas**;
- nenhum `No Data` presumido resolvido sem consulta.

**Dashboard-alvo (após adequações):**
- desenho da visão executiva para os quatro indicadores priorizados;
- dependências de instrumentação/queries, banco/BI e decisões de Negócio, identificadas por indicador;
- condições de alerta/monitor apenas como propostas quando thresholds ou responsáveis não estiverem aprovados;
- não incluir Origem ou Parcela Cobrada × Lançada como entregues ou aprovados sem decisão.

**Plano de instrumentação e integrações:**
- para cada gap, indicar aplicação, componente e evento/campo ausente, somente se comprovado;
- separar `QUERY/WIDGET`, `INSTRUMENTAÇÃO DE APLICAÇÃO`, `FONTE EXTERNA / BANCO / BI`, `DEFINIÇÃO FUNCIONAL` e `ACESSO/PERMISSÃO`;
- **não exigir alteração de código só porque não há widget**;
- preservar propostas ainda não confirmadas com estado `PROPOSTA / NÃO VALIDADA`;
- atualizar documentos existentes de planos quando houver relação direta e fundamentada.

**Importante:** produzir documentação de configuração não autoriza publicar widgets. Não crie JSON de dashboard executável isolado, scripts de automação nem novas ferramentas nesta etapa, salvo se um arquivo existente já tiver esse formato e a atualização for estritamente documental e necessária.

### 9. DOCUMENTAÇÃO TRANSVERSAL, CANONICIDADE E LINKS

Se README, arquitetura ou gaps ainda apontarem indicadores fora do escopo como ativos:
- corrija **somente** as seções necessárias, sem apagar histórico;
- destaque quais quatro indicadores são prioritários e quais dois estão pendentes por `bvseg`;
- torne o documento de cada indicador a referência canônica da sua definição, os documentos de dashboard a referência canônica da visualização e os de plano a referência canônica da instrumentação;
- evite copiar a mesma query/definição em muitos arquivos; use referências relativas claras;
- mantenha a distinção entre decisão de Negócio, evidência técnica e proposta.

**Não mova, apague, compacte ou renomeie arquivos antigos.** O arquivamento e a limpeza de versões redundantes ocorrerão na Etapa 04, após revisão humana.

### 10. PROIBIÇÕES E PROTEÇÃO DO AMBIENTE

- **Permitido escrever:** somente arquivos Markdown preexistentes do Darwin Monitor em `indicadores/`, `dashboards/`, `planos/` e documentos transversais pertinentes (`README.md`, arquitetura/gaps), após verificar a necessidade real.
- **Proibido escrever:** relatórios Etapas 01/02, guia de ferramentas, planilhas, PDFs, códigos das aplicações, testes, bancos de dados, pipelines, configurações, secrets, coletores, dashboards/widgets/monitores reais.
- Não realizar chamadas mutáveis ao Datadog; leitura somente se indispensável para conferir uma afirmação já documentada e houver autorização.
- Não instalar dependências, executar scripts de migração nem realizar procedimentos que possam alterar produção.
- Não criar relatórios paralelos, `final_v2`, `rev3`, novas pastas ou arquivos de histórico.
- Não fazer commit/push.
- Não expor dados de clientes, IDs reais de propostas/CPF, valores financeiros identificáveis, cookies, headers ou credenciais.
- Se houver conflito forte entre documentos e a correção exigir uma decisão funcional não disponível, **registre a pendência em documento canônico e continue com o que for seguro**.

### 11. CRITÉRIOS DE ACEITE — ETAPA 03 DOCUMENTAL

Antes de encerrar:
1. Os **quatro indicadores Seguros** têm documentação canônica atualizada e coerente com a Etapa 02.
2. Os **dois indicadores bvseg** permanecem identificados, pendentes e sem promessas de implementação.
3. Nenhuma contagem isolada foi descrita como taxa calculável sem numerador, denominador e população elegível.
4. Nenhuma ausência de widget virou automaticamente "instrumentação necessária".
5. O MVP distingue sucesso técnico HTTP de sucesso funcional e não mistura métricas operacionais parciais com indicadores executivos validados.
6. O dashboard-alvo e as necessidades por aplicação apontam dependências e responsáveis reais, sem inventar nomes técnicos ou queries.
7. Os documentos possuem referências rastreáveis e links relativos válidos, sem placeholders `****`, IDs vazios ou conclusões sem suporte.
8. Somente arquivos Markdown preexistentes e pertinentes foram editados; os relatórios e guias de ferramentas permaneceram intactos.
9. Nenhum documento foi excluído/arquivado e nenhuma alteração ocorreu no Datadog ou nas aplicações.
10. Execute conferência de integridade (links relativos, caminhos existentes, classificação, consistência de nomenclatura e `git diff` quando possível).
11. O conteúdo está suficiente para planejamento das histórias por indicador, queries/widgets, monitores/alertas e instrumentações em etapa futura, **sem confundir consolidação documental com implementação**.

Caso um item não possa ser concluído, não o apresente como concluído: declare `ETAPA 03 PARCIAL` e registre exatamente o bloqueio.

### 12. ENTREGA FINAL

Ao terminar, apresente em português:

1. **Status:** `ETAPA 03 DOCUMENTAL CONCLUÍDA` ou `ETAPA 03 PARCIAL`, com justificativa.
2. **Arquivos modificados:** caminho exato + resumo objetivo da alteração.
3. **Resultado dos quatro indicadores Seguros:** estado atual, MVP proposto/documentado, gaps e próximo passo — sem inventar status operacional.
4. **Indicadores bvseg:** pendências mantidas e decisão esperada da PO.
5. **Matriz de gaps:** por aplicação, o que é somente query/widget, o que realmente exige instrumentação, o que depende de banco/BI e o que depende de definição.
6. **Dashboards:** o que foi consolidado como MVP e alvo, e o que permanece não validado.
7. **Validação de integridade:** links, paths, diff e confirmação de não criação/arquivamento/edição de código ou Datadog.
8. **Preparação para Etapa 04:** listar candidatos documentais ao arquivamento **sem movê-los agora**; não criar arquivo separado para isso.
9. **Pendências humanas:** perguntas específicas a PO/Negócio, Engenharia e Observabilidade para permitir a implementação.

**Pare e aguarde minha aprovação. Não inicie a Etapa 04, não publique dashboards e não implemente instrumentação.**
