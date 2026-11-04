# Darwin Monitor — Prompt 01: Inventário confiável de indicadores e aplicações

## Papel e objetivo

Atue como analista técnico de observabilidade do projeto Darwin. Execute **somente a etapa de preparação e classificação dos indicadores**. O objetivo desta execução é produzir um inventário rastreável e enxuto dos indicadores que deverão ser investigados posteriormente, **um de cada vez e por aplicação**.

O Darwin Monitor serve para estabelecer e acompanhar uma linha de base de comportamento operacional durante a migração/desmembramento do monólito de venda digital e a transição para Pismo. Não é necessário demonstrar a conclusão ponta a ponta de operações executadas por serviços de outros times. A futura monitoria deve representar fielmente os eventos, resultados e falhas **observáveis na fronteira das aplicações sob responsabilidade do time**.

**Nesta execução, não crie nem corrija queries, widgets, dashboards, instrumentações, código ou monitores. Não acesse o Datadog.**

## Fontes e ordem de confiança

1. **Planilha original "Monitoramento Cartões 1"**: fonte dos requisitos de negócio, objetivos, fórmulas e observações. Localize o arquivo real no ambiente e a aba **Monitoramento**. Se o arquivo ou a aba não estiverem acessíveis, interrompa e informe exatamente o que falta; não reconstrua a planilha a partir de relatórios.
2. **Planilha auxiliar de indicador × fonte/aplicação** (aba **Plan1**, quando disponível): ponto de partida para localizar aplicações, classes e fluxos. É incompleta e pode conter mapeamentos desatualizados ou imprecisos.
3. **Código-fonte dos repositórios locais acessíveis**: fonte de evidência para confirmar ou refutar o mapeamento de aplicações e classes, apenas quando necessário para esta etapa de classificação.
4. **Documentos existentes em docs/darwin-monitor**, inclusive **03-dashboard-alvo.md** e **03-relatorio-validacao-dashboard-uat-e-plano-correcao-gaps.md**: histórico e hipóteses; **não são prova** de que logs, métricas, fluxos ou queries realmente existam ou funcionem.

Não substitua um dado da planilha original por afirmações do relatório anterior. Registre divergências.

## Etapa 1 — Seleção estrita na planilha original

1. Localize o arquivo e registre nome, caminho, aba, cabeçalhos reais e quantidade de registros examinados.
2. Leia **todas as linhas de dados da aba Monitoramento**. Não aplique filtro a outras abas por engano.
3. Localize a coluna **Responsável** (aceite apenas variações de acentuação, caixa e espaços no cabeçalho) e a coluna **OBSERVAÇÕES**.
4. Selecione somente linhas em que:
   - **Responsável** seja **Seguros** OU **Seguros - Monitoramento do V6**. Para comparação, ignore caixa, acentos e espaços excedentes; aceite variações meramente tipográficas de separadores. **Não use correspondência por contém**, nem aceite outras equipes por aproximação.
   - **OBSERVAÇÕES** esteja efetivamente vazia (nula ou apenas espaços). Se houver qualquer anotação substantiva, a linha deve ser excluída. Não interprete uma observação como autorização para incluí-la.
5. Preserve literalmente, em cada item incluído: **linha original**, **Indicador Executivo**, **Objetivo**, **Fórmula / Métrica**, **Responsável** e **OBSERVAÇÕES**; quando os nomes reais dos cabeçalhos forem diferentes, use os nomes encontrados e informe a correspondência.
6. Se houver indicadores com o mesmo título, **não os agrupe automaticamente**. Diferencie-os pela linha de origem, objetivo e fórmula. Em particular, variações de "Parcela Cobrada x Parcela Lançada" podem representar requisitos distintos.
7. Registre os totais: linhas lidas, excluídas por responsável, excluídas por observações e elegíveis. Não force uma contagem esperada; derive-a do arquivo.

Não inclua linhas excluídas no inventário de execução. Pode apresentar apenas suas contagens e, se necessário, inconsistências de filtro em seção separada.

## Etapa 2 — Cruzamento com a planilha auxiliar

Para cada linha elegível:

1. Busque o indicador correspondente na planilha auxiliar, considerando nome, objetivo e fórmula, e não somente igualdade do título.
2. Registre quais aplicações/classes a auxiliar sugere e de qual linha/aba veio a informação.
3. Se não houver correspondência, marque **SEM MAPEAMENTO AUXILIAR**.
4. Se a associação for ambígua, marque **ASSOCIAÇÃO A CONFIRMAR**, sem escolher arbitrariamente.
5. Não trate a coluna **Fontes** da auxiliar como comprovação de execução do fluxo.

## Etapa 3 — Checagem limitada das aplicações

O escopo inicial são três aplicações:

- **eventos-conta-cartao**: recorrência e outros indicadores pertinentes.
- **eventos-pagamento**: débito, cobrança, postagem, cancelamento, conciliação e outros indicadores pertinentes.
- **venda-digital**: sobretudo o indicador de origem de contratos DXC × Pismo.

**Pistas técnicas informadas pelo time, não restrições de busca:**

- Eventos Pagamento: **PostagemFaturaConsumer.java** e **PostagemFaturaRouter.java**. Se o primeiro nome não existir, procure variantes plausíveis de grafia, mas não afirme a existência da classe sem encontrá-la.
- Eventos Conta Cartão: **SolicitacaoRecorrenciaViaPubSub.java**.
- Venda Digital: procure o fluxo de contratação e a identificação de origem/processadora com base no código efetivamente disponível.

Para cada indicador, faça **somente a investigação mínima necessária para estabelecer uma aplicação candidata**. Comece pelas pistas da planilha auxiliar e pelas classes acima, mas siga chamadas e componentes adjacentes se isso for necessário para verificar a associação. Não use números de linha de código fornecidos em documentos anteriores; localize o código atual.

Se o repositório não estiver acessível, **não simule a inspeção**. Marque a associação como provisória e aponte a pendência. Se um indicador atravessar duas aplicações, mantenha ambas e indique os papéis sugeridos de cada uma, sem inventar a responsabilidade de negócio.

**Limite desta etapa:** não faça auditoria completa de fluxo, não execute testes, não procure exaustivamente logs, não construa queries. O objetivo é apenas preparar o roteiro de investigação posterior.

## Regras contra dedução indevida

- **NÃO** declare que um indicador é viável apenas porque sua linha não tem observações.
- **NÃO** confunda início de processamento, entrega a um serviço externo, sucesso técnico de HTTP e resultado final de negócio.
- **NÃO** atribua conclusão de negócio a uma aplicação se o resultado final pertence a outro time e não é observável localmente.
- **NÃO** trate DLQ automaticamente como sinônimo de taxa de reprocessamento, nem consumo de Kafka como sucesso de negócio.
- **NÃO** invente classes, métodos, logs, tags, recursos APM, consultas, mensagens ou estados.
- **NÃO** conclua que uma mensagem está disponível no Datadog por encontrá-la no código.
- **NÃO** reutilize as classificações "ALTA / 100% viável / implementado" dos relatórios anteriores sem validação; isso será trabalho dos prompts seguintes.
- **NÃO** investigue o código de aplicações de outros times por iniciativa própria: registre a dependência externa quando pertinente.
- **NÃO** armazene dados de clientes, identificadores reais de propostas, cookies, tokens nem URLs internas em relatórios.

## Saída exigida — um único arquivo Markdown

Crie **docs/darwin-monitor/planos/04-inventario-filtrado-indicadores.md** (ou, se a pasta não existir, use o diretório equivalente já existente no workspace). Não sobrescreva documentos existentes; se esse nome estiver ocupado, escolha uma versão incremental e informe o caminho.

Estruture o documento assim:

### 1. Fontes efetivamente lidas
- Arquivo, aba, caminho, data/versão quando disponível.
- Quais fontes foram acessíveis e quais não foram.

### 2. Resultado do filtro
- Quantidades de linhas examinadas, excluídas por cada critério e selecionadas.
- Critério exato aplicado e eventuais casos ambíguos.

### 3. Inventário mestre de indicadores elegíveis
Tabela com **ID local estável (por exemplo, IND-01)**, linha da planilha original, indicador, objetivo, fórmula/métrica, responsável, aplicação(ões) candidata(s), evidência do mapeamento e status de associação.

Estados permitidos da associação:
- **CONFIRMADA NO CÓDIGO**: apresente repositório, arquivo e método/ponto de entrada localizado.
- **INDICADA PELA AUXILIAR**: existe pista documental, mas não houve confirmação no código.
- **PENDENTE**: associação insuficiente ou conflitante.

O status aqui trata **somente do mapeamento de aplicação**, não da viabilidade técnica do indicador.

### 4. Filas por aplicação
Organize os IDs elegíveis em três subseções: **Eventos Conta Cartão**, **Eventos Pagamento** e **Venda Digital**. Repita o ID nas aplicações envolvidas se um indicador depender de mais de uma. Crie uma subseção **Pendente de associação** para casos ainda não resolvidos.

Para cada ID, informe em **uma ou duas linhas** o ponto de partida sugerido (classe ou fluxo, com sua qualidade de evidência). Não descreva a solução nem crie queries.

### 5. Divergências e pendências
Liste, de forma objetiva, conflitos entre original, auxiliar, código e documentos históricos, além de arquivos inacessíveis. Não resolva uma divergência por preferência pessoal.

### 6. Próximo passo recomendado
Identifique **somente o primeiro indicador a ser investigado** e por qual aplicação, justificando brevemente a escolha. **Não execute essa investigação agora.**

## Controle de execução e encerramento

Trabalhe em sequência: **localizar a planilha → aplicar filtro → cruzar auxiliar → conferir associações pontuais no código → gravar inventário → encerrar**.

Responda no chat apenas:
- caminho do documento produzido;
- quantidade de indicadores elegíveis;
- distribuição por aplicação (um indicador pode aparecer em mais de uma);
- pendências impeditivas;
- primeiro indicador recomendado para investigação.

**Encerre após essa entrega. Não avance para prompts por aplicação, instrumentação, Datadog ou dashboards sem nova instrução.**
