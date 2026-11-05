# Darwin Monitor — Prompt 04E: validação final das rotas Camel e preparação técnica para CI (Eventos Pagamento)

**INSTRUÇÃO PARA UMA JANELA NOVA DO GEMINI CLI — EXECUÇÃO LOCAL.**

## Missão única

Concluir a verificação técnica da instrumentação de `cdPropostaSeguro` já implementada em **Eventos Pagamento**, sem reabrir auditorias anteriores. Há apenas **três pendências**:

1. Verificar e, se comprovadamente redundante, eliminar **somente a chamada nova** de `ObservabilityProcessor` na rota SGAT, sem perder o `MDC`/tracing preexistente.
2. Criar e **executar testes direcionados com execução da composição Camel real ou equivalente demonstrável** para `cancelamentoRecorrencia` e `estornoDebito`, usando mocks para impedir integrações externas.
3. Verificar **lifecycle, limpeza, isolamento e eventual propagação entre threads** de `MDC.cdPropostaSeguro`, incluindo sucesso, exceção e próximo Exchange; corrigir apenas problemas reproduzidos.

**Proibido encerrar com mera análise estática e sem executar testes.** Se algum teste Camel completo for realmente inviável, execute os testes unitários/de composição possíveis e demonstre precisamente a limitação, sem alegar cobertura de integração que não ocorreu.

## Contexto suficiente — o que já aconteceu

- Projeto: **Darwin Monitor**, Banco BV, monitoramento limitado às fronteiras observáveis dos serviços. Ambiente-alvo posterior: Datadog UAT.
- Repositório Java a alterar: `sboot-pseg-base-orch-gestao-eventos-pagamento`. A **branch real verificada no 04D** era `feature/SEGNP2-5340`, HEAD `305ca64cc05a01e55dcbf354645c5ea6a1491676`. **Verifique o Git novamente**, não presuma que nada mudou.
- Diretório de documentação local: `darwin-monitor/planos/`, situado no workspace do usuário (não duplicar pastas). Relatórios relevantes: `16-auditoria-plano-instrumentacao-cdpropostaseguro-eventos-pagamento.md`, `17-revisao-corretiva-testes-cdpropostaseguro-eventos-pagamento.md` e especialmente **`18-testes-efetivos-instrumentacao-cdpropostaseguro-pagamento.md`**. **Leia somente as seções necessárias**, confrontando com código/testes reais.
- Em 04B foram criados `camel/processor/ObservabilityProcessor.java` e alterações em `camel/router/PubSubRouter.java` nas rotas `listen-status-agendamento-sgat`, `cancelamentoRecorrencia` e `estornoDebito`.
- Em 04C o Gemini inspecionou, mas **não realizou testes nem correções**.
- **Em 04D, houve execução real de 8 testes unitários novos** em `ObservabilityProcessorTest`, comando informado `mvn test -Dtest=ObservabilityProcessorTest`: **8 executados, 8 aprovados, 0 falhas/erros/ignorados**, BUILD SUCCESS (~1 min 33 s). Os testes abrangem propriedade do Exchange, JSON raiz em bytes, precedência propriedade/body, ID ausente, vazio, nulo, JSON inválido e Tracer nulo. Foi detectada por teste uma `NullPointerException` quando `tracer == null` e adicionada proteção `if (tracer != null)`, com reteste aprovado. **Preserve esses oito testes e sua correção.**
- O responsável também executou **`mvn clean install` com sucesso** antes do 04D. É sucesso informado por ele, não é prova de propagação do MDC nas rotas.
- **SGAT**: contrato `AgendamentoRecorrencia`; identificador **aninhado** em `payload.seguro.codigoPropostaSeguro`. O novo `ObservabilityProcessor` procura `cdPropostaSeguro` em uma propriedade ou na raiz JSON, por isso aparentemente **não contribui** antes do `SgatAgendamentoStatusProcessor`; este processador já extrai o ID aninhado e executa `MDC.put("cdPropostaSeguro", ...)` e instrumentação/tracing existente. A chamada nova pode ser redundante. Isso exige confirmação da ordem, do MDC e do span, **não remover a instrumentação preexistente**.
- **Cancelamento**: contrato reportado `CancelamentoRecorrenciaPubSubRequest`, identificador na raiz `cdPropostaSeguro` (`Long`). Rota `cancelamentoRecorrencia` chama o novo `ObservabilityProcessor`. O processor específico posterior pode registrar a mesma informação em Exchange property.
- **Estorno**: contrato reportado `AgendamentoRecorrenciaEstorno`, identificador na raiz `cdPropostaSeguro` (`Long`). Rota `estornoDebito` chama o novo `ObservabilityProcessor`.
- Em cancelamento e estorno, **os logs iniciais de recebimento/body/cabeçalhos estão antes** do novo `ObservabilityProcessor` e portanto não recebem o atributo dessa instrumentação. Não chamar isso de cobertura total. **Não ampliar exposição de payload/cabeçalhos, nem reposicionar esses logs por reflexo.** O objetivo é correlação dos logs de negócio posteriores, de falha e retry, quando efetivamente emitidos.
- O relatório 18 informa `onCompletion().process(e -> MDC.clear())` nas rotas; **não há evidência de testes Camel de finalização/threads**. A chamada pode limpar campos MDC pertencentes a outros mecanismos e, em processamento assíncrono, pode ocorrer em thread diferente. **Inspecione e teste o comportamento do Camel realmente utilizado**.
- No Datadog UAT, `mdc.cdPropostaSeguro` **já foi confirmado em Conta Cartão**; **ainda não foi confirmado em Eventos Pagamento** com a nova implementação. **Ainda não houve CI/PR/deploy dessas alterações.** Nenhum dado remoto comprova seu comportamento pós-publicação.
- Esta tarefa **não é** de indicadores, taxa de falha IND-02 ou dashboard. Não alterar `darwin-monitor/uat_darwin_monitor_dashboard.json` nem `update_uat_dashboard.py`, nem código de Conta Cartão.

## Escopo e segurança — antes de qualquer edição

Execute e registre resultados reais:
```bash
pwd
git rev-parse --show-toplevel
git branch --show-current
git rev-parse HEAD
git status --short
git diff --check
```

Leia alterações em `PubSubRouter.java`, `ObservabilityProcessor.java`, `ObservabilityProcessorTest.java` e componentes necessários à montagem dos fluxos, bem como `pom.xml` e testes Camel existentes. Preserve modificações não relacionadas; **não `git reset`, `git checkout`, `git clean`, `git stash`, nem mudanças destrutivas**.

**Autorização:** apenas modificações **locais** focadas na instrumentação de Eventos Pagamento e testes associados, mais o relatório em `darwin-monitor/planos/`. Nenhuma alteração de regra de negócio, schema ou contrato externo, chamada de serviço, autenticação, ACK/NACK, idempotência, retentativas, persistência ou payload; não alterar dependências desnecessariamente; não comentar/desabilitar testes para obter verde.

**NÃO** criar commit, branch, PR, push, CI, deploy ou publicar no Datadog. **NÃO** modificar dashboards/JSON/Python, aplicações de outros times ou fazer requisições reais a Pub/Sub/HTTP/DB. Dados de teste devem ser sintéticos.

## Etapa 1 — SGAT: confirmar redundância e fazer a menor correção

1. Inspecione a rota `listen-status-agendamento-sgat`, o contrato recebido, `SgatAgendamentoStatusProcessor` e o novo `ObservabilityProcessor`; determine **exatamente** quando cada um obtém o ID, faz `MDC.put` ou adiciona tag ao span.
2. Demonstre com **teste focalizado executado** (ou com teste de composição de processors reproduzindo ordem real e explicitando sua limitação) se remover a **chamada nova** do SGAT preserva MDC, span e o processamento preexistente. Se o teste não puder comprovar span/ordem, **não afirmar equivalência integral**.
3. **Somente se a redundância for demonstrada**, remova a chamada nova dessa rota; mantenha o processador nativo, os pontos de log e o comportamento funcional. Não remova a classe `ObservabilityProcessor`: cancelamento e estorno ainda a utilizam.
4. Caso a chamada nova também produza observabilidade válida não coberta pelo SGAT, **não a remova por suposição**: descreva diferença e tome a opção mínima que preserve cobertura.

## Etapa 2 — testes de integração local: cancelamento e estorno

Investigue o mecanismo de teste efetivo do projeto, incluindo Camel `AdviceWith`, mocks/endpoint replacement, `DefaultCamelContext` ou helpers existentes. Preferir teste de rota **sem rede**; não exigir `CamelTestSupport` caso não seja dependência presente.

**Para CADA rota** crie e execute teste(s) que comprovem:

- O identificador no **payload real representado por fixture sintética** está disponível no ponto esperado, sob a chave exata `cdPropostaSeguro`.
- O processador consegue colocar `cdPropostaSeguro` no MDC **antes de um log ou processor de negócio posterior**, com valor igual ao da mensagem; se houver span ativo, verificar tag da mesma correlação.
- Em caminhos de **falha/exceção/retry acessíveis sem dependências externas**, o MDC é observado no ponto correto e o comportamento existente de erro/retentativa permanece intacto.
- Duas mensagens com IDs distintos processadas em sequência não compartilham ID anterior; uma mensagem sem identificador não herda indevidamente ID de outra.
- O corpo, os headers e as Exchange properties essenciais continuam intactos após observabilidade; não vazar conteúdo sensível nos logs dos testes.

**Critério de evidência:** capturar a chave MDC **durante a execução da rota/processor no ponto de negócio**, não apenas após a execução, pois o cleanup pode já tê-la removido. Se substituir etapas externas por mocks, explicitar quais segmentos da rota foram de fato executados.

Se um teste de rota completa não for possível por razões demonstráveis, execute teste da **composição fiel da sequência de processors** e marque como `INTEGRAÇÃO PARCIAL`. Não classifique como `ROTA VALIDADA INTEGRALMENTE` com base apenas nos oito testes unitários já existentes.

## Etapa 3 — cleanup / thread safety: provar, não presumir

1. Revise `onCompletion().process(e -> MDC.clear())` sob o **Camel e a configuração realmente presentes**. Teste o caminho de sucesso e de exceção. Registre se o callback e a emissão de logs usam a mesma thread; se não conseguir comprovar propagação/correção entre threads, registre a limitação.
2. Teste preservação de chaves MDC corporativas preexistentes (por exemplo `traceId` ou uma chave sintética `contextoExistente`); **não use nome de uma chave externa como evidência de política real**. O teardown deve evitar apagar contexto de outro dono e evitar deixar `cdPropostaSeguro` de uma mensagem anterior.
3. Não trocar `MDC.clear()` indiscriminadamente por `MDC.remove` ou inversamente. **Corrija apenas se os testes ou o contrato real de execução demonstrarem a necessidade**, explicando a propriedade de ciclo de vida escolhida.
4. Atente para o fato de que MDC é **ThreadLocal**; `onCompletion` e callbacks podem mudar de thread. Não apresentar um teste monothread como prova de todas as variantes assíncronas.

## Etapa 4 — execução, regressão mínima e decisão

- Execute novamente **os oito unitários** de `ObservabilityProcessorTest` e todos os testes novos de SGAT/cancelamento/estorno/cleanup, registrando comandos, durações, nomes e totais reais de `Tests run`, `Failures`, `Errors` e `Skipped`.
- Só corrigir falhas comprovadas por esses testes e reexecutar os afetados. **Não afrouxar assertivas, mudar produção para atender fixture errada ou deixar testes intermitentes mascarados.**
- Evitar `mvn clean install` completo sem justificativa, pois o responsável já informou sucesso; use Maven direcionado conforme POM real.
- Faça `git diff --check`, `git status --short` e compare os arquivos atuais ao estado inicial; não incluir mudanças preexistentes indevidas. Se alertas CRLF/LF surgirem, não formatar em massa.
- Decisão final com rigor:
  - **PRONTO PARA REVISÃO HUMANA DE CI:** oito testes unitários preservados e aprovados; testes novos executados e aprovados no escopo realmente coberto; SGAT preservado; ausência de falhas conhecidas de cleanup; diferenças e limitações registradas.
  - **REVISÃO COM RESSALVAS:** testes de composição passaram, mas parte da execução Camel real/async não foi demonstrada; identificar a lacuna concreta.
  - **BLOQUEADO:** qualquer teste obrigatório falha ou não foi sequer executado sem impedimento reproduzível, ou existe defeito relevante não resolvido.
- `PRONTO PARA REVISÃO` **NÃO significa aprovado para produção ou comprovado em UAT**.

## Entregável: relatório 19

Crie **`darwin-monitor/planos/19-validacao-integracao-camel-final-eventos-pagamento.md`**, com:

1. Repositório/branch/HEAD/status inicial e final verificados.
2. SGAT: comportamento anterior, evidência da redundância, teste executado, decisão e alteração (se houver).
3. Cancelamento e estorno: contratos, mocks, etapas efetivamente executadas, asserts de MDC/span, caminhos de exceção/retry e resultados.
4. MDC lifecycle: resultado de testes de cleanup, contextos de thread e lacunas.
5. Testes: tabela `nome | comando | evidência | passou/falhou/não executado`, contagens reais e falhas corrigidas.
6. Diff por arquivo, riscos residuais e classificação rigorosa de prontidão para revisão humana.
7. Passo futuro **somente após revisão e deploy autorizado**: verificar versão/commit em UAT e pesquisar novos logs de Eventos Pagamento com `service:sboot-pseg-base-orch-gestao-eventos-pagamento env:uat @mdc.cdPropostaSeguro:*`, incluindo correlação de eventos nos fluxos que realmente executarem; **não afirmar validação remota nesta etapa**.

**Resposta final única (máximo ~12 linhas):**
- Branch/HEAD real;
- SGAT: redundância comprovada? chamada nova removida ou mantida? por quê?;
- Cancelamento e estorno: tipo de teste executado (rota ou composição) e resultados;
- MDC cleanup: comprovado/limitado/falhou;
- Testes totais: 8 anteriores + N novos, aprovados/falhos/ignorados;
- Correções feitas, arquivos modificados;
- Classificação `PRONTO / COM RESSALVAS / BLOQUEADO`;
- Relatório 19;
- `PR/CI/DEPLOY/PUSH/DATADOG/DASHBOARD/JSON: NÃO EXECUTADOS/ALTERADOS`.

**PARE após entregar o relatório. Não prosseguir para IND-02 nem reabrir as etapas 04B–04D. Não enviar várias mensagens de “tarefa concluída”.**
