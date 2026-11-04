# Darwin Monitor — Prompt 03E: verificar a versão implantada em UAT e a ausência do cdPropostaSeguro

## Missão (somente diagnóstico; nenhuma alteração)

Investigue **qual código/artefato da aplicação `sboot-pseg-base-orch-gestao-eventos-conta-cartao` está efetivamente implantado em UAT** e determine se ele contém a instrumentação `cdPropostaSeguro` da branch `feature/SEGNP2-5340`. Compare a implementação local com a versão comprovadamente implantada, sem presumir que `main`, `develop`, `release`, uma branch de QA ou a branch aberta sejam a fonte do deploy.

**Pergunta decisiva:** a ausência de `cdPropostaSeguro` nos logs UAT se explica por versão implantada diferente, por falta de preenchimento da `ExchangeProperty`, por serialização/ingestão do MDC ou ainda permanece indeterminada?

Esta é uma **verificação de implantação e diagnóstico de causa**, NÃO uma nova auditoria de indicadores e NÃO uma tarefa de correção.

## Evidência já obtida pelo responsável — não repetir testes

1. Na branch local `feature/SEGNP2-5340`, o arquivo `SolicitacaoRecorrenciaViaPubSub.java` mostra a sequência `ValidaEventoGeradoProcessor` → `ObservabilityProcessor("cdPropostaSeguroByRequest", tracer)` → logs do fluxo. A mesma rota registra `onCompletion().process(e -> org.slf4j.MDC.clear())`.
2. `ObservabilityProcessor.java` lê `exchange.getProperty(propertyName, String.class)` e, se o valor não for nulo nem vazio, executa `MDC.put("cdPropostaSeguro", propostaId)` e tenta adicionar a tag homônima ao span atual.
3. No **Datadog UAT**, o responsável encontrou logs do serviço e confirmou que o evento real possui um objeto `mdc` estruturado com campos como `categoria`, `clientId`, `nomeAplicacao`, `nomeServico` e `ticket`. Um registro da rota Google Pub/Sub foi inspecionado: **`cdPropostaSeguro` não estava no objeto `mdc`**.
4. A pesquisa que adiciona `@mdc.cdPropostaSeguro:*` ao filtro de início de recorrência não retorna registros no período examinado. Isso demonstra ausência de correspondências **naquela consulta e janela**, não ausência universal do campo em todo o histórico.
5. Logs de início, sucesso local, falha de negócio e retry já foram confirmados em UAT em pesquisas anteriores. **Não reexecutar as consultas A–E nem pedir testes de List/Timeseries**.
6. O relatório `planos/10-auditoria-instrumentacao-e-suficiencia-ind01-conta-cartao.md` concluiu que a rota Pub/Sub local parece instrumentada e que a telemetria estruturada em UAT ainda não foi comprovada. **Sua conclusão não prova que a mesma versão esteja publicada em UAT**.

A presença de **outros campos do MDC** torna inadequado afirmar genericamente que “o Datadog não recebe MDC”. O problema pode ser específico do atributo, da versão ou de algum ponto do fluxo.

## Limites obrigatórios

- **Somente leitura e análise.** Não alterar Java, testes, branches, commits, pipelines, manifestos, configurações, dashboards, monitores, JSON nem scripts.
- **Não realizar deploy, build com efeito remoto, restart, rollback, merge, push, PUT ou PATCH.**
- Use somente **ferramentas, repositórios e informações de implantação já autorizados e disponíveis** ao ambiente. Não buscar credenciais, cookies, tokens ou URLs internas por tentativa e erro. Não contornar restrições de acesso.
- Caso não haja acesso ao registro de deploy/cluster, **assuma o limite explicitamente** e entregue a informação mínima que o responsável precisa obter junto ao time/plataforma. Não invente SHA, imagem, tag, número de release, versão ou horário.
- Não declarar que `cdPropostaSeguro` deveria aparecer na UAT só porque existe em uma branch. Não assumir que o processo local, o commit de um PR, um merge e o artefato em execução são a mesma coisa.
- O escopo é **apenas o IND-01 e a aplicação Conta Cartão**. Não reabrir IND-07 ou outros indicadores.

## 1. Confirme o workspace e o estado do código local

A raiz de trabalho do Darwin Monitor é a pasta **`darwin-monitor/`**, que contém diretamente `planos/`, `dashboards/`, `uat_darwin_monitor_dashboard.json` e `update_uat_dashboard.py`. O repositório Java pode estar em outra pasta: localize-o antes de operar e não tente escrever fora das raízes permitidas pelo ambiente do Gemini.

Na aplicação `sboot-pseg-base-orch-gestao-eventos-conta-cartao`, obtenha **somente leitura**:

- nome do repositório e caminho real;
- branch ativa, HEAD SHA completo e `git status` (diferenciando alterações locais não commitadas);
- último(s) commit(s) que introduziram ou corrigiram `ObservabilityProcessor`, sua chamada em `SolicitacaoRecorrenciaViaPubSub` e a extração de `cdPropostaSeguroByRequest`, com SHA e arquivos afetados;
- referências de branches locais/remotas que **existem de fato**; não inferir que uma branch por nome equivale ao deploy UAT.

Se o código instrumentado estiver apenas no working tree, deixe explícito: uma alteração não commitada **não é evidência de implantação**.

## 2. Descubra a origem verificável do código executado em UAT

Procure, em ordem de preferência e sem acesso não autorizado:

1. **Registro real do deploy UAT:** pipeline/execução de implantação, job, release, plataforma de CI/CD, repositório de configuração/deploy, manifesto, Helm values, GitOps ou outro mecanismo existente na empresa. Identifique somente recursos efetivamente encontrados, sem supor Jenkins, GitHub Actions, ArgoCD ou Kubernetes como certos.
2. **Identidade do artefato em execução**, se for acessível apenas para leitura: SHA ou revision, build number, versão, image tag **e, se existir, digest imutável**, horário, serviço e ambiente.
3. **Rastreabilidade do artefato ao fonte:** vincule o artefato executado à execução de build e ao SHA exato do código de origem. Uma tag mutável ou nome de branch sozinho é evidência fraca; se não houver vínculo inequívoco, diga isso.
4. Se existir mais de uma instância/réplica, verifique se há diferentes revisões em UAT (rollout parcial). Caso só haja o identificador do host no log, não o trate automaticamente como versão.
5. Confira se os logs examinados foram produzidos **depois** da implantação da revisão identificada; registro anterior ao deploy não demonstra comportamento da versão nova.

**Produto desta seção:** `UAT revision/commit = ...`, `evidência = ...`, `grau de confiança = comprovado / indício / indisponível`. Inclua caminhos, referência a execução/PR/manifesto e datas **apenas quando efetivamente verificadas**.

Não se limite a `git log` ou a comparar HEAD local com HEAD remoto: isso **não prova o que está rodando em UAT**.

## 3. Compare o código local com o código-fonte do artefato UAT

**Somente após** identificar de forma confiável o commit do build efetivamente implantado:

- Compare o SHA de UAT com a branch `feature/SEGNP2-5340` e com o commit que introduziu a instrumentação; considere cherry-picks e commits equivalentes, não apenas ancestralidade do grafo Git.
- No SHA de UAT, verifique objetivamente se existem:
  - `ObservabilityProcessor.java`, sua lógica de `MDC.put("cdPropostaSeguro", propostaId)` e proteção contra valor ausente;
  - a chamada do processor **na rota Pub/Sub correta**, depois de `ValidaEventoGeradoProcessor` e antes dos logs usados pelo IND-01;
  - a atribuição efetiva da `ExchangeProperty` `cdPropostaSeguroByRequest` a partir dos payloads processados;
  - a configuração relevante de logging/MDC, se versionada junto ao artefato.
- Registre comparação por **comportamento e trechos reais**, incluindo classe/método e SHA; não use somente contagem de arquivos/diff nem presuma que nomes de classes bastam.

Se o SHA de UAT **não puder ser identificado**, **não invente um diff “contra UAT”**. Em vez disso, diga quais verificações ficaram bloqueadas e por qual evidência faltante.

## 4. Árvore decisória para explicar o campo ausente

Separe as conclusões:

**Cenário A — UAT utiliza artefato SEM a instrumentação.**
Conclusão sustentada: diferença de versão/artefato. Informe qual mudança/commit não está no build e qual fluxo de integração/publicação deve ser verificado com o time. **Não faça deploy**.

**Cenário B — UAT utiliza artefato COM a instrumentação, mas o log não contém a chave.**
Investigue apenas pelo código/configuração disponível, nesta ordem:
1. O log inspecionado realmente é **posterior** ao processor e pertence ao fluxo de entrada previsto? Não confundir logs de outro processor/rota com os de início;
2. `ValidaEventoGeradoProcessor` popula `cdPropostaSeguroByRequest` em todos os formatos válidos de mensagem? Que condições a deixam nula/vazia ou encerram o fluxo antecipadamente?
3. O `ObservabilityProcessor` de fato é executado para aquele caminho, ou há bypass/curto-circuito?
4. Existe mudança de thread, processo assíncrono, limpeza antecipada de MDC, interceptador de logging ou configuração que poderia impedir o valor chegar ao evento?
5. Outros campos de MDC aparecem na UAT: foque especificamente em como o atributo novo seria serializado/filtrado; configuração de ingestão só é “culpada” se houver evidência.

**Cenário C — artefato em UAT não identificável.**
Conclusão: **causa não determinada**. Registre o ponto exato bloqueado, sem culpar código, MDC ou pipeline. Sugira **uma única obtenção objetiva de informação** (por exemplo: SHA/build ID da implantação UAT com vínculo ao artefato). Não peça nova rodada de consultas de logs sem mudança material de hipótese.

Não declarar sucesso da instrumentação só por `MDC.put` e não declarar defeito definitivo apenas por `0 logs`.

## 5. Entregável enxuto e verificável

Produza relatório novo em **`darwin-monitor/planos/11-verificacao-versao-implantada-uat-ind01-conta-cartao.md`** — isto é, pasta `planos/` **diretamente na raiz `darwin-monitor/`**. Não criar `docs/darwin-monitor/planos/` debaixo dessa raiz. Se a ferramenta de edição disser `path not in workspace`, use a raiz de workspace real que esteja autorizada; não crie uma estrutura duplicada nem tente contornar a restrição.

Organize o relatório assim:

1. **Resumo executivo:** hipótese confirmada, refutada ou ainda indeterminada; confiança e evidência.
2. **Identidades comparadas:** workspace/branch/HEAD/estado local; referência de UAT; SHA, build, imagem/digest, origem e horário **quando comprovados**.
3. **Matriz de evidências**, com colunas `Pergunta | Evidência primária | Resultado | Nível de confiança | Pendência`.
4. **Diff comportamental** entre versão de UAT e branch instrumentada, **somente se houver SHA comprovado**.
5. **Diagnóstico da ausência de cdPropostaSeguro:** classifique A, B ou C e explique por que.
6. **Próximo passo mínimo, sem executar:** uma ação concreta e seu responsável (Gemini/time de plataforma/usuário), com indicação de qual nova evidência mudaria a decisão.

Ao concluir, responda no terminal com:
- **UAT contém a instrumentação?** SIM COMPROVADO / NÃO COMPROVADO / INDETERMINADO;
- **Causa mais bem sustentada para ausência no log;**
- **Evidência específica (SHA/build/pipeline/arquivo), ou limitação real;**
- **Único próximo passo recomendado;**
- **Caminho exato do relatório criado e confirmação de existência.**

O IND-01 mantém os três widgets operacionais de volume como candidatos viáveis independentemente desta investigação; **não implementar ou publicar widgets nesta etapa**.