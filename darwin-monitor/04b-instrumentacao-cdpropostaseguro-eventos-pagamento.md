# Darwin Monitor — Prompt 04B: auditoria, plano e instrumentação mínima de `cdPropostaSeguro` em Eventos Pagamento

> **EXECUÇÃO LOCAL NA SESSÃO DO GEMINI CLI.** Esta tarefa autoriza investigar o código, elaborar um plano e **implementar somente as correções justificadas no repositório de Eventos Pagamento**, com testes. **Não autoriza deploy, CI/CD, PR, push, alteração do Datadog nem de dashboards.**
>
> **Critério de sucesso:** distinguir ausência de dado na mensagem, ausência de instrumentação, falha de propagação assíncrona, falha de serialização/ingestão e ausência de tráfego/versão em UAT; corrigir só o que o código comprovar. Não mudar o contrato de integração sem autorização dos responsáveis.

## 1. Contexto e evidências já disponíveis

Projeto **Darwin Monitor**, Banco BV, com objetivo de medir a fronteira observável dos serviços durante o desmembramento de venda-digital e a transição DXC/Bankly/Pismo.

- Serviço-alvo desta tarefa: **`sboot-pseg-base-orch-gestao-eventos-pagamento`** (Eventos Pagamento).
- Serviço de referência, **sem autorização para alterações**: **`sboot-pseg-base-orch-gestao-eventos-conta-cartao`**.
- Em Conta Cartão, a instrumentação `cdPropostaSeguro` foi implantada em UAT e o responsável **confirmou que logs recentes contêm o campo estruturado `mdc.cdPropostaSeguro`**. Foi observada correlação parcial entre logs de início e de etapas intermediárias, incluindo uma proposta com múltiplos logs de início. Ainda não há tráfego suficiente para comprovar todos os caminhos de conclusão.
- **Em Eventos Pagamento, o responsável ainda não observou `mdc.cdPropostaSeguro` nos logs de UAT.** Isso **não comprova ausência de código ou defeito de instrumentação**: pode decorrer de fluxo não executado, payload sem o ID, versão antiga implantada, configuração de logging/Datadog ou de propagação/limpeza do MDC.
- Há achados históricos de instrumentação prévia para SGAT em Eventos Pagamento, inclusive possível `cdPropostaSeguro` em processors; **investigue e aproveite tudo que já existe, sem duplicar código**.
- A investigação do IND-02 (Taxa de Falha) foi documentada em `darwin-monitor/planos/15-investigacao-ind02-taxa-de-falha.md`: taxa por adesões únicas ainda não comprovada; os widgets operacionais do IND-01 mostram volumes de falhas e retries. **Não tentar resolver taxas ou modificar dashboards nesta tarefa**.
- Na etapa posterior, o usuário depende de **revisão/aprovação de CI e deploy**. Por isso o produto desta execução precisa estar pronto para revisão, mas **nenhuma ação de entrega remota está autorizada**.

### Raízes do workspace

Localize a raiz física correta dos repositórios Java e de `darwin-monitor/`. Não presuma que o repositório de Eventos Pagamento fica dentro de `darwin-monitor`. Os documentos de relatório ficam diretamente em **`darwin-monitor/planos/`**, sem criar `docs/darwin-monitor/planos` aninhado. Não usar o repositório público `senaadrianojr/tmp-prompt` para código ou arquivos internos.

## 2. Etapa A — fotografar o estado e investigar antes de escrever código

1. Identifique repositório, branch, HEAD, `git status --short`, arquivos modificados existentes e o build real (Java/Maven/Spring/Camel). **Não descartar, sobrescrever, resetar, comentar ou incluir alterações de terceiros.** Se houver conflito com arquivos já modificados, interrompa apenas a edição conflitante e reporte.
2. Investigue o fluxo de **entrada → extração do identificador → processors/rotas → logs/traces → erros/retries → limpeza** em Eventos Pagamento. Inicie pelos fluxos de SGAT/débito, solicitações de recorrência e outros consumidores/rotas de Pagamento efetivamente relevantes; não faça uma varredura indiscriminada de todo o ecossistema.
3. Procure **todas as ocorrências já existentes** de `cdPropostaSeguro`, `cdPropostaSeguroByRequest`, `MDC.put`, `MDC.remove`, `MDC.clear`, `ObservabilityProcessor`, `currentSpan()`, `tracer`, correlatos em payload, cabeçalhos e propriedades de Exchange; identifique classes e condições reais, incluindo Kafka/PubSub/HTTP e possíveis threads assíncronas. Não supor que o ID exista em todos os fluxos.
4. Trace o contrato efetivamente recebido e a origem do campo em cada operação. Distinga:
   - **ID disponível no payload/propriedade/header**, mas ainda não adicionado ao MDC;
   - **MDC adicionado**, mas perdido antes do logger, em threads/callbacks ou em rotas subsequentes;
   - **ID inexistente no contrato de entrada**, exigindo mudança em serviço upstream ou contrato (fora da autorização de alteração unilateral);
   - **Código aparentemente correto**, mas diferença de versão implantada, configuração do appender JSON, ingestão Datadog, sampling ou falta de eventos;
   - **campo presente com caminho diferente de `mdc.cdPropostaSeguro`** — apenas se houver evidência real.
5. Inspecione configuração de logger/Logback e mecanismo de ingestão **apenas no workspace autorizado**, sem acessar APIs de Logs bloqueadas, tentar contornar autenticação ou modificar configurações corporativas. Se não houver meios de confirmar UAT, indique o limite, sem afirmar causa raiz.

**Entregue uma matriz por fluxo realmente encontrado:** entrada; onde ID nasce ou chega; forma do ID; local exato em que é gravado/consumido; pontos em que logs deveriam herdá-lo; eventual perda por contexto assíncrono; modo de cleanup; fonte de evidência (arquivo/método e trecho). Identifique impacto, prioridade e proposta mínima para cada lacuna.

## 3. Etapa B — plano técnico de correção, antes de editar

Registre no relatório uma proposta de alteração mínima que responda:

- Quais classes/métodos/rotas precisam ser tocados, por quê e qual evento observável se deseja correlacionar.
- Qual identificador está comprovadamente disponível (não inventar default, não extrair de logs por regex frágil e não reutilizar um código de outro domínio sem prova de equivalência).
- Como garantir que `cdPropostaSeguro` exista **antes dos logs relevantes**, seja preservado na mesma unidade de execução e limpo com segurança ao final, inclusive falhas, retries, descarte e exceções.
- Para fluxos assíncronos, qual mecanismo concreto preserva contexto sem vazar dados entre mensagens/threads; **MDC é thread-local e um `MDC.put` inicial não garante propagação a outro thread**. Validar comportamento efetivo do framework e do processamento.
- Se fizer sentido usar span de APM e houver tracer já disponível, como adicionar tag de correlação compatível com o padrão existente; **não criar tags de ID em métricas de alta cardinalidade**, não introduzir dependências desnecessárias e respeitar políticas corporativas.
- Como validar localmente sem depender de novos logs em UAT.
- Se algum fluxo exigir **alteração de contrato com outros times, payload ou upstream**, **não implemente essa parte**: documente dependência, compatibilidade e aprovação necessária; implemente apenas melhorias locais independentes comprovadas.

Não adicione telemetria com IDs pessoais em mensagens abertas, nem serialize cabeçalhos/tokens/cookies ou payloads sensíveis só para depuração.

## 4. Etapa C — implementar SOMENTE o que foi demonstrado como necessário

**Autorização nesta etapa:** alterações locais focadas, exclusivamente no repositório **Eventos Pagamento**, de instrumentação e seus testes associados.

1. Reutilize helpers/processors existentes quando adequados. Evite `MDC.put` dispersos por todo o código, duplicação de processadores e novos comportamentos de negócio. Se a instrumentação já existir corretamente, **não reescreva por reflexo**.
2. Prefira obter o ID em um ponto em que seu valor tenha sido validado, antes dos logs de negócio. Não colocar uma tag em span se ainda não houver valor válido.
3. Garanta a limpeza do MDC em escopo adequado por mensagem/exchange/rota, inclusive excepcionais, **sem limpar chaves pertencentes a outros fluxos, sem apagar prematuramente o contexto de callbacks e sem afetar observabilidade padrão**. Usar `MDC.clear()` apenas se for comprovadamente o padrão seguro nesse ponto; caso contrário, restaurar/remover a chave específica.
4. Não alterar a semântica de ACK/NACK, tratamento HTTP, persistência, transações, idempotência, roteamento, contrato de mensagens, autenticação ou lógica funcional.
5. Não adicionar `cdPropostaSeguro` como tag de métrica Micrometer de alta cardinalidade; não introduzir logs verbosos adicionais desnecessários.
6. Se a inspeção indicar que a instrumentação está correta e a ausência no Datadog decorre possivelmente de versão, tráfego, ingestão ou contrato, **não fabricar uma mudança no Java**. Registre `SEM ALTERAÇÃO JUSTIFICADA` e descreva o próximo teste/dependência.

### Restrições obrigatórias para testes

- Preserve todos os testes existentes. **NUNCA comentar, apagar, desabilitar ou relaxar assertivas para forçar build verde.**
- Escreva testes direcionados para: ID presente/ausente; mensagem válida/inválida; execução normal; exceção e retry; não vazamento de MDC entre mensagens; propagação através de etapas assíncronas quando aplicável; ausência de impacto nas rotas já cobertas.
- Execute os testes com o comando e Java/Maven realmente suportados no ambiente. Reporte comandos, totais, falhas e causas. Não afirmar `BUILD SUCCESS` sem log efetivo. Não modificar certificados, permissões, proxy ou configuração corporativa para contornar falhas externas.
- Diferencie falhas preexistentes/ambientais de regressões da mudança. Corrija regressões ocasionadas por suas alterações; se não conseguir, pare e reporte o estado, **sem ocultar o problema**.

## 5. Etapa D — revisar o diff e preparar aprovação de CI

1. Execute `git diff --check` e a revisão de todos os arquivos tocados. Compare com `git status` inicial. Liste todos os arquivos modificados/criados por esta execução e explique a razão de cada um; não incluir arquivos estranhos.
2. Registre branch/HEAD atuais, testes executados, cobertura possível, riscos de context propagation, dependências de upstream e logs que deverão ser verificados após a implantação.
3. Apresente plano de validação **pós-deploy**: versão/commit implantado realmente conferido, log novo do serviço Pagamento em UAT, atributo estruturado `mdc.cdPropostaSeguro`, correspondência entre dois ou mais logs do mesmo fluxo, inexistência de vazamento entre mensagens e limites observáveis.
4. **Não criar PR, não commitar nem fazer push, não iniciar pipeline e não executar deploy.** O responsável revisará o diff e decidirá sobre commit, PR e CI em etapa posterior.
5. Não acessar nem modificar `uat_darwin_monitor_dashboard.json`, `update_uat_dashboard.py`, widgets, monitores ou configurações do Datadog.

## 6. Entregável e critérios de conclusão

Criar **`darwin-monitor/planos/16-auditoria-plano-instrumentacao-cdpropostaseguro-eventos-pagamento.md`**, com:

1. **Estado inicial real** (repo, branch, commit, arquivos modificados antes da execução).
2. **Tabela dos fluxos relevantes**, rastreamento do identificador, classes/métodos/trechos concretos e classificação de cada lacuna: `CONFIRMADA`, `HIPÓTESE`, `JÁ IMPLEMENTADO`, `DEPENDE DE UPSTREAM`, `DEPENDE DE UAT`.
3. **Plano mínimo e decisão por fluxo**: implementado agora, preservado ou bloqueado; evidência e motivo.
4. **Diff implementado** por arquivo e explicação de por que a solução é correta quanto a ordem, MDC, async e cleanup.
5. **Testes** (comandos, resultados e cobertura; falhas e justificativas).
6. **Limitações e dependências** para aprovação de CI, implantação e validação em UAT.
7. **Critérios verificáveis** para distinguir código corrigido de telemetria de Pagamento comprovada no Datadog; não atribuir sucesso pós-deploy antes de ocorrer.
8. Nota de que a taxa por proposta do IND-02 continua sem validação de resultado terminal e de que não se deve confundir sucesso técnico ou conclusão local com adesão efetivada.

**Resposta final em formato enxuto:**
- Encontrou instrumentação anterior? Onde?
- Qual a causa comprovada da falta de `cdPropostaSeguro` em Pagamento? Se indeterminada, escreva `NÃO DETERMINADA`.
- Fluxos cobertos, alterações feitas e arquivos modificados.
- Testes e status real.
- Dependências/impedimentos para CI.
- Relatório criado e seu caminho.
- `CI/PR/DEPLOY: NÃO EXECUTADOS`; `DATADOG/DASHBOARD/JSON: NÃO ALTERADOS`.

**Não repita relatórios inteiros na resposta, não entre em loop e não trate planejamento como execução. Só declarar IMPLEMENTADO quando o diff real e os testes comprovarem.**
