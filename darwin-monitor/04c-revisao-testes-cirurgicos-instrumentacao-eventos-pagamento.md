# Darwin Monitor — Prompt 04C: revisão corretiva e testes cirúrgicos de `cdPropostaSeguro` em Eventos Pagamento

> **Execute em sessão do Gemini CLI com acesso ao repositório real de Eventos Pagamento.** A tarefa é **revisar a implementação local feita no 04B, comprovar o seu funcionamento por testes específicos e corrigir apenas defeitos demonstrados**. Não recomeçar a instrumentação do zero.
>
> **Nova evidência fornecida pelo responsável:** ele próprio executou **`mvn clean install` com sucesso** no projeto após o 04B. Portanto, o timeout de cinco minutos do Gemini no 04B **não deve ser tratado como falha de build ou pendência automática de CI**. O que ainda falta é **evidência de comportamento**, especialmente sobre extração, ordem, MDC, tracing, rotas e limpeza. Não alegar que o sucesso do build comprova tudo isso.

## 1. Contexto

Projeto **Darwin Monitor**, Banco BV. A referência funcional é a propagação do identificador `cdPropostaSeguro` como campo estruturado **`mdc.cdPropostaSeguro`** nos logs relacionados aos fluxos sob responsabilidade do time.

- **Serviço-alvo:** `sboot-pseg-base-orch-gestao-eventos-pagamento` (Eventos Pagamento).
- **Referência somente leitura:** `sboot-pseg-base-orch-gestao-eventos-conta-cartao`, que já possui instrumentação confirmada nos logs de UAT, embora sem comprovação de todas as etapas terminais.
- **Relatório anterior:** `darwin-monitor/planos/16-auditoria-plano-instrumentacao-cdpropostaseguro-eventos-pagamento.md`. Leia para orientar a revisão, mas **valide suas afirmações contra o código real**.
- **Arquivos modificados no 04B, conforme relatório:** novo `.../camel/processor/ObservabilityProcessor.java` e alteração em `.../camel/router/PubSubRouter.java`; os caminhos completos devem ser localizados, não presumidos.
- **Três rotas instrumentadas pelo 04B:** `listen-status-agendamento-sgat`, `cancelamentoRecorrencia`, `estornoDebito`.
- **Instrumentação preexistente:** `SgatAgendamentoStatusProcessor` já realizava `MDC.put` segundo auditoria anterior. Verificar redundância, cobertura e ordem antes de qualquer edição.
- **Implementação 04B:** `ObservabilityProcessor("cdPropostaSeguro", tracer)` obtém o identificador de `exchange.getProperty(fieldName, String.class)` ou, como fallback, de **campo homônimo na raiz de um corpo JSON representado em `byte[]`**; em seguida faz `MDC.put(fieldName, correlationId)` e `currentSpan().tag(...)` se aplicável. Revisar limitações à luz dos **contratos reais**.
- Em trechos do `PubSubRouter` apresentados no relatório, `ObservabilityProcessor` foi adicionado **antes** de processors específicos que podem extrair atributos; alguns logs de corpo/cabeçalhos aparecem **antes** da instrumentação; blocos `onCompletion().process(e -> MDC.clear())` foram introduzidos. Isso constitui **hipóteses de problema que exigem teste**, não condenação prévia.
- **Datadog:** a ausência do campo em logs anteriores de Pagamento NÃO prova ausência de instrumentação publicada, falha do processamento ou falta de identificador em todas as mensagens; ainda não houve validação pós-deploy da implementação 04B.
- O relatório anterior deixou branch/HEAD não especificados. **Desta vez registre valores reais**.

**Não alterar** Conta Cartão, `darwin-monitor/uat_darwin_monitor_dashboard.json`, `update_uat_dashboard.py`, widgets, dashboards, monitores, indicadores, contratos externos ou lógica de negócio.

## 2. Etapa A — inspeção e matriz de evidências ANTES de qualquer correção

1. Identifique e registre raiz física do repositório de Eventos Pagamento, branch real, `git rev-parse HEAD`, `git status --short` e `git diff` inicial (incluindo arquivos untracked que sejam da implementação). **O 04B pode estar apenas no working tree**; não fazer reset, checkout forçado, stash, commit, cherry-pick ou sobrescrita de trabalho existente.
2. Leia integralmente `ObservabilityProcessor.java`, os trechos pertinentes de `PubSubRouter.java`, processors específicos das três rotas, DTOs/contratos de mensagem, mecanismo de desserialização e configuração Camel/MDC. Consulte loggers relevantes antes, durante e depois do processador.
3. Para **cada uma das três rotas**, registre:
   - tipo e exemplo **sintético/sem dados reais** do body na entrada (`byte[]`, `String`, objeto, envelope JSON, outra estrutura), confirmado pelo código;
   - caminho exato do `cdPropostaSeguro` ou atributo equivalente **comprovar equivalência**; ele chega na entrada, é extraído depois, está aninhado ou não consta do contrato?
   - ponto em que passa a estar disponível no Exchange e ponto onde ocorre o `MDC.put`;
   - quais logs realmente são posteriores à instrumentação, quais são anteriores, e se a chave é visível em todos os eventos de negócio relevantes;
   - comportamento em processamento normal, condição de descarte, erro e retentativa;
   - cleanup e contextos thread/async utilizados.
4. Avalie especificamente a duplicação com `SgatAgendamentoStatusProcessor`: o novo `ObservabilityProcessor` substitui, complementa, conflita ou simplesmente repete a instrumentação? **Não duplicar `MDC.put` nem reescrever SGAT por reflexo**.
5. Valide se o uso de `getIn().getBody(byte[].class)` converte o corpo por TypeConverter sem perda/mutação, se é compatível com os contratos das rotas e se pode gerar leitura vazia. Não adicionar fallback especulativo para formatos que não ocorrem nesses fluxos.
6. Observe que **MDC é thread-local**. Não presumir que `onCompletion` e `MDC.clear()` limpam ou preservam o contexto correto em toda execução assíncrona. Verifique como Camel gerencia threads, rotas, callbacks e MDC nesse projeto e se o código novo pode apagar outras chaves corporativas, deixar valor de proposta anterior ou limpar a chave cedo demais.
7. **Se o código do 04B estiver correto em um fluxo, deixe-o intocado.** Se alguma lacuna depender de contrato upstream, ambiente, deploy ou novos dados em UAT, classifique `PENDENTE DE EVIDÊNCIA`, não invente correção Java.

Produza uma matriz **por rota** com evidência código/teste e classificação `CORRETO / DEFEITO COMPROVADO / NÃO COMPROVADO / DEPENDE DE UPSTREAM OU UAT`. Separe problema de posicionamento, obtenção do ID, propagação de contexto, logger e cleanup.

## 3. Etapa B — testes cirúrgicos obrigatórios

**Objetivo: provar comportamento, não apenas compilar.** Testes novos devem usar a stack e o padrão de testes existentes (JUnit, Mockito, CamelTestSupport etc., conforme o projeto) e fixture **sintética**, sem dados reais de clientes.

### B1. Testes do `ObservabilityProcessor`

Cobrir, conforme os contratos reais:
- ID presente em Exchange property, depois em body conforme formato realmente usado; estabelecer precedência consistente se ambos estiverem presentes.
- ID nulo, vazio ou espaços: não inserir MDC inválido, não marcar span com valor inválido, não falhar o fluxo.
- ID ausente ou payload malformado: comportamento seguro, com tratamento apropriado e **sem exceção que altere ACK/NACK do fluxo**, salvo comportamento preexistente explícito.
- Body `byte[]`/outro tipo **somente se esse tipo for real**; não transformar o body incidentalmente nem impedir processors seguintes de lê-lo.
- Tracer inexistente ou sem span ativo: não lançar `NullPointerException`. Conferir `MDC.put` com a chave exata `cdPropostaSeguro`.
- Se adicionar tag de tracing, comprovar tag no span quando há span; **nunca** tag de alta cardinalidade em métrica Micrometer.

### B2. Testes de integração local das rotas

Para cada rota (`listen-status-agendamento-sgat`, `cancelamentoRecorrencia`, `estornoDebito`):
- Usar Camel test/adviceWith/mocks ou mecanismo existente para **não chamar serviços externos, tópicos Pub/Sub reais, HTTP corporativo, banco ou filas de verdade**.
- Com uma mensagem sintética representativa e ID válido, comprovar a ordem **extração → MDC → log de negócio posterior**; coletar contexto no local correto, não somente verificar que o processador foi instanciado.
- Confirmar se logs iniciais de body/cabeçalhos ficam sem ID pela ordem atual e decidir, com base na finalidade e no risco de expor payload/tokens, se é necessário alterar posição ou **remover a necessidade de logar informações sensíveis**. Não aumentar logging de payload.
- Exceção/erro/descartes relevantes: preservar a semântica existente e verificar cleanup e inexistência de vazamento para a próxima mensagem na mesma thread quando o ambiente de teste permitir.
- Em processamento assíncrono, comprovar ou explicitar a **limitação demonstrável** de propagação do MDC; não forçar teste verde com um único thread se a rota real usar thread diferente.
- SGAT: comprovar que o MDC final não é sobrescrito por null/ID diferente e que a instrumentação anterior continua válida.

**Ordem dos testes:** unitários focalizados primeiro, depois testes das rotas afetadas, e somente ampliar a suíte se algum resultado justificar. O responsável **já executou `mvn clean install` com sucesso**; não gastar tempo repetindo uma instalação integral sem necessidade.

### B3. Execução e rastreabilidade

- Identifique os nomes reais dos testes e módulos. Execute comandos Maven focalizados compatíveis com o projeto e Java instalado; não inventar comandos específicos que não existam no `pom.xml`.
- Registre comando exato, total executado, sucesso/falha, tempo e trecho do erro se houver.
- Testes preexistentes são imutáveis em sua intenção: **não comentar, desabilitar, excluir ou afrouxar assertivas**. Corrigir regressões reais introduzidas pelo 04B; distinguir falha preexistente/ambiental de regressão.
- Se um teste não puder ser feito automaticamente, descrever cenário manual/test harness, entrada e asserção esperada; **não reportar como validado**.

## 4. Etapa C — correções condicionadas à comprovação

Depois dos testes/inspeção, corrigir **somente** o que estiver demonstrado:

1. Posicionar instrumentação **após a extração real do ID e antes dos logs relevantes** quando apropriado, mantendo tratamento de mensagens inválidas, retry e roteamento idênticos ao existente.
2. Adequar leitura do `cdPropostaSeguro` ao **contrato comprovado** em cada fluxo: propriedade do Exchange, corpo e/ou header se já documentado. Não adivinhar nomes equivalentes nem introduzir alteração de contrato upstream.
3. Preservar a instrumentação anterior do SGAT; consolidar a nova apenas se realmente necessária e demonstradamente segura.
4. Ajustar limpeza para restaurar/remover contexto corretamente nos pontos comprovados, sem `MDC.clear()` indiscriminado que destrua outros campos, sem vazamento entre mensagens e sem quebrar o tracing corporativo.
5. Evitar alterações em autenticação, conteúdo da mensagem, ACK/NACK, exceções de negócio, idempotência, headers de transporte, payload de saída ou qualquer funcionalidade não relacionada.
6. Reexecutar **somente os testes afetados** e revisar o diff. **Não esconder falhas**, não afirmar efetividade de UAT sem deploy.

Se uma correção possível depender de aprovação para mudar payload/contrato entre equipes, **não executar essa parte**. Registre quem deve fornecer a evidência/decisão e o mínimo necessário para continuidade.

## 5. Revisão final e entrega sem ações remotas

1. Compare `git status` inicial/final, `git diff --check` e os arquivos criados/modificados. Não commitar, criar PR, fazer push, iniciar pipeline ou deploy.
2. Confirme que alterou somente arquivos do serviço Eventos Pagamento (instrumentação e testes) e o relatório local em `darwin-monitor/planos/`. Não tocar em código de Conta Cartão, JSON de dashboard, scripts Python, Git de `darwin-monitor` ou Datadog.
3. Produza o relatório **`darwin-monitor/planos/17-revisao-testes-cirurgicos-instrumentacao-eventos-pagamento.md`**, com:
   - baseline real de branch/HEAD/status e relação com mudanças do 04B;
   - matriz por rota e por requisito/teste `PASSOU / FALHOU / NÃO EXECUTADO`;
   - rastreamento da extração até `MDC.put` e logs de negócio, com classes/linhas;
   - diferenças entre instrumentação SGAT preexistente e nova;
   - evidência de cleanup (inclusive exceções/threading) e tag de tracing;
   - lista de correções feitas, diff resumido e justificativa, ou `NENHUMA CORREÇÃO NECESSÁRIA`;
   - comandos Maven e resultados reproduzíveis;
   - limitações que dependem de upstream/QA/UAT;
   - checklist objetivo de prontidão para revisão humana/CI e critérios de validação após deploy.

**Resposta final única e curta:**
- Branch/HEAD verificados;
- 3 rotas e status de extração, ordem, MDC, cleanup;
- Instrumentação anterior SGAT preservada? SIM/NÃO/COM RESSALVAS;
- Correções executadas (ou nenhuma) e arquivos alterados;
- Testes executados: nomes e números de pass/fail, sem inventar;
- `mvn clean install` do responsável: **SUCESSO INFORMADO**, distinto dos testes efetivamente executados aqui;
- Impedimentos reais para CI;
- Caminho do relatório;
- **PR/CI/DEPLOY/PUSH/DATADOG/DASHBOARD/JSON: NÃO EXECUTADOS/ALTERADOS**.

**Importante:** execução concluída apenas quando houver evidência observável de implementação local + testes direcionados ou bloqueio fundamentado. **Não repetir instruções de encerramento nem apresentar hipóteses como causa comprovada.**
