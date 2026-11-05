# Darwin Monitor — Prompt 04D: fechar a lacuna de testes de `cdPropostaSeguro` em Eventos Pagamento

**USE EM UMA NOVA SESSÃO DO GEMINI CLI.** Você não tem acesso confiável ao raciocínio nem a resultados não documentados da sessão anterior. Este prompt é autossuficiente para o escopo. Trabalhe somente com **evidência do código e resultados executados**, não com conclusões inventadas. **Não recomece a auditoria global.**

## Missão única desta sessão

**Criar e executar testes cirúrgicos para a instrumentação já adicionada no serviço Eventos Pagamento; corrigir somente defeitos comprovados; entregar evidências para revisão humana antes de CI.** Não produzir outro relatório de análise estática sem testes. A falta de `CamelTestSupport` pré-configurado **não impede testes unitários isolados de um `Processor`**.

Se um teste específico for tecnicamente inviável, execute os outros e registre o impedimento **concreto, reproduzível e específico**, com comando/erro — nunca use “complexidade” ou “tempo disponível” como explicação genérica.

## Contexto fechado — o que sabemos, sem precisar pesquisar novamente

**Projeto:** Darwin Monitor, Banco BV, ambiente de observabilidade Datadog UAT. A telemetria precisa refletir o comportamento na **fronteira dos serviços sob responsabilidade do time**, não fingir conclusão final de negócio.

**Serviço-alvo e único repositório Java editável:** `sboot-pseg-base-orch-gestao-eventos-pagamento`.

**Referência somente leitura (NÃO editar):** `sboot-pseg-base-orch-gestao-eventos-conta-cartao`. Ali o responsável já confirmou em logs UAT o atributo estruturado `mdc.cdPropostaSeguro`, com correlação parcial entre eventos; não há ainda prova de conclusão de ponta a ponta.

**Histórico desta implementação:**

- **04B:** o Gemini criou `ObservabilityProcessor.java` e alterou `PubSubRouter.java` para instrumentar as rotas `listen-status-agendamento-sgat`, `cancelamentoRecorrencia` e `estornoDebito`. Criou também documentação em `darwin-monitor/planos/16-auditoria-plano-instrumentacao-cdpropostaseguro-eventos-pagamento.md`. O build do Gemini terminou por timeout; **depois o responsável executou `mvn clean install` com SUCESSO**. Isto comprova o build executado pelo responsável, **não** a efetividade de MDC ou tracing.
- **04C:** o Gemini analisou estaticamente a solução e criou `darwin-monitor/planos/17-revisao-corretiva-testes-cdpropostaseguro-eventos-pagamento.md`, mas declarou expressamente **“Correções: nenhuma; Testes: nenhum”**. Mencionou falta de `CamelTestSupport` e de tempo. **Esse trabalho NÃO atendeu ao objetivo dos testes**. Não repetir esse padrão.
- Na análise do 04C, `listen-status-agendamento-sgat` foi sinalizada por possível **duplicação de `MDC.put`** entre o novo `ObservabilityProcessor` e o `SgatAgendamentoStatusProcessor` preexistente. `cancelamentoRecorrencia` e `estornoDebito` foram consideradas *aparentemente* corretas pela leitura estática, **sem testes**. Não tratar aparência como aprovação.
- O novo `ObservabilityProcessor` (conforme prints) implementa `org.apache.camel.Processor`, recebe `fieldName` e `io.micrometer.tracing.Tracer`, obtém ID de `Exchange.getProperty(fieldName, String.class)` ou de `exchange.getIn().getBody(byte[].class)` interpretado por Jackson `ObjectMapper` para procurar campo de mesmo nome **na raiz** do JSON; quando presente, usa `MDC.put(fieldName, correlationId)` e `tracer.currentSpan().tag(...)`, quando aplicável. **Confira o código REAL e os contratos reais antes de afirmar compatibilidade**.
- As três rotas possuem trechos com `onCompletion().process(e -> MDC.clear())` introduzidos pelo 04B; há mensagens de recebimento/body/headers logadas **antes** do novo processor, ao menos nos trechos exibidos. O ponto de extração do identificador e eventual processamento assíncrono ainda precisam de comprovação.
- **Eventos Pagamento ainda não teve `mdc.cdPropostaSeguro` confirmado nos logs de UAT**; não houve deploy autorizado destas alterações. Não concluir que a ausência comprova bug de código.
- **IND-02:** taxa de falha por adesões únicas ainda não comprovada. Não modificar indicadores nem fabricar taxa. Esta sessão é **exclusivamente testes de instrumentação**.
- **Repositório de prompts `senaadrianojr/tmp-prompt`** serve apenas para prompts, nunca para enviar código/relatórios corporativos.

**Arquivos esperados, verifique caminhos exatos:**
- `src/main/java/br/com/votorantim/pseg/base/gestao/eventos/pagamento/camel/processor/ObservabilityProcessor.java` (untracked após 04B, conforme 04C);
- `src/main/java/br/com/votorantim/pseg/base/gestao/eventos/pagamento/camel/router/PubSubRouter.java` (modificado após 04B);
- classe preexistente `SgatAgendamentoStatusProcessor` e processors de cancelamento/estorno, DTOs/contratos associados.

**Branch e HEAD são DADOS A VERIFICAR**, não assumir `main` — relatório anterior escreveu “main (assumido)”, o que não é comprovação. O HEAD alegado anteriormente foi `305ca64cc05a01e55dcbf354645c5ea6a1491676`; confirme usando Git, sem mudar de branch.

## Regras inegociáveis

1. **Comece pelo estado REAL:** `pwd`, `git rev-parse --show-toplevel`, `git branch --show-current`, `git rev-parse HEAD`, `git status --short`, `git diff --check`, `git diff` dos arquivos afetados, dependências e estrutura de testes. Preserve mudanças existentes. **NÃO dar reset/checkout/stash/clean.**
2. Não alterar lógica de negócio, ACK/NACK, idempotência, chamadas HTTP/PubSub, contratos upstream, tratamento de exceções funcional, payload ou segurança. Não desabilitar, comentar, remover ou afrouxar testes existentes.
3. **NÃO** executar CI, publicar, fazer deploy, criar PR, push ou commit; não mexer no dashboard, JSON versionado, `update_uat_dashboard.py`, Datadog ou Conta Cartão. **Autorizado** editar somente implementação local de Eventos Pagamento e testes relacionados, além de criar relatório local em `darwin-monitor/planos/`.
4. **NÃO** adicionar métricas com ID de alta cardinalidade, logs sensíveis, novos dumps de payload/cabeçalhos ou segredos em fixtures. Use dados artificiais.
5. Não tentar resolver falta de tráfego UAT ou a ausência de campo remoto nesta sessão. Os resultados aqui são **validade local**, não confirmação pós-deploy.
6. **Mantenha contexto enxuto:** leia somente as classes necessárias e os documentos 16/17 em seus trechos relevantes. Não rastrear todos os repositórios ou reabrir planilha/IND-01. Não criar to-do gigante nem emitir vários encerramentos. Se um bloqueio exigir outro trabalho, registre para depois.

## Plano de execução obrigatório — testes primeiro

### Etapa 1 — descobrir ambiente de testes (curta)

Inspecione `pom.xml` (dependências de testes e plugins), arquivos `src/test` próximos e versões reais do Camel/JUnit/Mockito. Use o framework já disponível. Se não houver `CamelTestSupport`, faça **teste unitário do `Processor` sem subir rotas**: `Exchange`/Camel Context de teste leve, MDC do SLF4J e Tracer/Span simulado conforme bibliotecas efetivas; se não existir helper específico, crie fixture simples sem infraestrutura externa.

**Antes da edição**, confirme contratos reais de entrada das três rotas, se `cdPropostaSeguro` já está no body, header ou property, e em que momento processors existentes o extraem. Não suponha que o campo está na raiz do JSON; não suponha que seja lido apenas como `byte[]`.

### Etapa 2 — implementar e executar testes UNITÁRIOS do `ObservabilityProcessor` (prioridade máxima, obrigatória salvo impedimento reproduzível)

Escreva testes executáveis que verifiquem, conforme contratos reais:

- Identificador presente na propriedade do Exchange → MDC recebe exatamente `cdPropostaSeguro`; corpo e headers continuam íntegros.
- Identificador presente no formato **real** do body → é extraído e inserido no MDC sem mutar o body; se body real for JSON raiz, teste isso. Teste estrutura aninhada **apenas se ocorrer no contrato**.
- Quando property e body existirem, definir/testar precedência; não sobrescrever um ID válido por outro inesperadamente.
- Identificador ausente, nulo, `""` ou espaços → não cria valor inválido; não reaproveita um valor de outra mensagem por engano.
- JSON inválido ou formato de body diferente → tratamento seguro sem mudar semântica da rota nem mascarar erros relevantes.
- Tracer/Span presente → tag no span com a chave correta; ausência de span/tracer, se permitida pelo contrato, não provoca NPE.
- Verificar limpeza/isolamento do MDC no escopo adequado a cada teste; os testes não podem contaminar uns aos outros.

Use nomes descritivos e assertivas reais. **Execute estes testes imediatamente antes de passar à próxima etapa**. Se falharem, registre reprodução e corrija somente código da instrumentação quando comprovado.

### Etapa 3 — conferir INTEGRAÇÃO LOCAL das três rotas, sem I/O remoto

Para cada uma:
- **SGAT:** reproduzir a ordem real entre `ObservabilityProcessor` e `SgatAgendamentoStatusProcessor`. Provar se há mesmo ID, dois `MDC.put` redundantes, sobrescrita errada ou valor ausente. Corrigir redundância SOMENTE se realmente demonstrada, preservando o comportamento SGAT preexistente.
- **Cancelamento:** confirmar `cdPropostaSeguro` disponível **antes** dos logs relevantes na rota, inclusive caminho erro/retry alcançável com mocks.
- **Estorno:** mesmo critério do cancelamento.
- **Cleanup e concorrência:** verificar o efeito de `onCompletion().process(e -> MDC.clear())` e do uso de threads assíncronas no Camel do projeto; `MDC` é thread-local, e a callback pode não executar na mesma thread que os logs. Testar não vazamento entre duas mensagens na mesma thread e proteção de chaves MDC corporativas preexistentes. Não substituir `MDC.clear()` por outro mecanismo sem evidência de que a mudança é segura e efetiva.

Priorize teste de rota com `AdviceWith`/mock de endpoint, se já suportado, ou teste **da composição de processors relevantes** para provar ordem e MDC, sem acessar HTTP, GCP Pub/Sub, banco, rede corporativa ou credenciais. **Não alegue que composição isolada comprova roteamento completo.** Se a integração completa exigir infraestrutura ausente, explique a limitação objetiva e preserve os unitários aprovados.

### Etapa 4 — correção mínima e reexecução focalizada

Se os testes confirmarem defeito:
1. Ajustar apenas instrumentação/posição/extração/limpeza necessárias; **preservar semântica de negócio**.
2. Testar o bug antes e depois (ou documentar o teste que falhou antes da correção e passou depois).
3. Executar somente testes afetados, usando `mvn -Dtest=NomeDaClasseTest test` ou variante correta **confirmada no POM**; se for multimódulo, usar módulo adequado, sem inventar nomes.
4. Fazer `git diff --check`; comparar `git status` inicial/final.

**Não repetir `mvn clean install` sem razão:** o responsável já informou SUCESSO. **Não** apresentar esse fato como se fosse teste executado pelo Gemini nesta sessão.

## Critérios explícitos de conclusão

**Aprovável para revisão humana de CI:** testes unitários novos executados e passando; testes de rotas possíveis executados, ou limitações de integração justificadas concretamente; SGAT preexistente preservado; nenhuma regressão comprovada; diff restrito e `git diff --check` limpo.

**Bloqueado:** testes unitários não executados ou falhando; ID inexistente nos contratos necessários e nenhum caminho comprovado; cleanup inseguro conhecido; alteração de lógica de negócio; branch/HEAD ou mudanças alheias sem proteção.

**Não use “concluído” se apenas inspecionou o código.** Se não puder executar teste algum, descreva o impeditivo REAL com o comando, erro e tentativas mínimas, e pare com status `BLOQUEADO`.

## Entregável

Crie o relatório **`darwin-monitor/planos/18-testes-efetivos-instrumentacao-cdpropostaseguro-pagamento.md`**, apenas com achados comprovados:

1. Repositório, branch e HEAD realmente lidos; arquivos sujos antes/depois.
2. Tabela `teste | objetivo | comando executado | resultado | evidência` com **contagens reais**, incluindo casos não executados.
3. Matriz das três rotas: extração real, posição do MDC, logs antes/depois, SGAT preexistente, cleanup, e limites async.
4. Eventuais correções mínimas e arquivos alterados (diff com justificativa), ou `SEM CORREÇÕES`.
5. Riscos, dependências e decisão `PRONTO PARA REVISÃO HUMANA DE CI` ou `BLOQUEADO` com causa.
6. Validação UAT futura: após aprovação e deploy, comprovar commit implantado e procurar `@mdc.cdPropostaSeguro:*` em logs **novos de Eventos Pagamento**; não afirmar agora.
7. Nota: o JSON/dashboard do Darwin Monitor não foram alterados.

**Resposta final única e curta**, com: branch/HEAD real; número de testes novos/criados/executados/aprovados/falhos; status por rota; alterações do código; se SGAT foi preservado; classificação pronto/bloqueado; caminho do relatório; confirmação `PR/CI/DEPLOY/PUSH/DATADOG/JSON: NÃO EXECUTADOS`.

**Pare após essa entrega. Não inicie novas tarefas nem reabra a investigação do IND-02.**
