# Darwin Monitor — Prompt 03F: preparação dos widgets operacionais IND-01 sem aguardar novos logs UAT

## Contexto e decisão

Estamos continuando o **Darwin Monitor** em Datadog **BV-DUQ, UAT**, dashboard **`[UAT] Darwin Monitor`**, ID **`ymk-jgs-3dd`**. Esta etapa trata **somente do IND-01** e da aplicação `sboot-pseg-base-orch-gestao-eventos-conta-cartao`.

**Novidade:** o responsável confirmou que conseguiu implantar em UAT uma versão com a instrumentação `cdPropostaSeguro` após corrigir um deploy que havia falhado. **Ainda não há novos logs suficientes da versão recém-implantada para atestar a presença do campo no MDC**. A inspeção anterior encontrou outros campos `mdc`, mas não `cdPropostaSeguro`; os registros examinados não podem ser atribuídos com segurança à revisão nova. Assim, NÃO conclua que a instrumentação está quebrada, nem que já foi validada em runtime. Registrar `implantação relatada como bem-sucedida; validação de telemetria da revisão pendente`.

**Decisão de execução:** não deixar os widgets operacionais de volume parados esperando a correlação por proposta. Os filtros textuais dos eventos já foram validados manualmente pelo responsável, em momentos anteriores, no Log Explorer UAT. Construir agora a definição dos widgets e deixá-la pronta **localmente**; a publicação será solicitada e autorizada em etapa separada.

## Objetivo

Preparar exatamente os três widgets operacionais candidatos do IND-01, no JSON local que representa o dashboard UAT, SEM alterar Datadog remoto:

1. **A — Processamentos de recorrência iniciados (Pub/Sub)**: `Count` do log efetivamente emitido no início do fluxo, com consciência de NACK/reentregas; o número **não representa propostas únicas**, nem necessariamente todo recebimento anterior à validação.
2. **B — Retornos de sucesso local do orquestrador**: `Count` do log de retorno bem-sucedido após chamada externa; **não** medir efetivação final da apólice ou cobrança.
3. **C — Ocorrências de falhas de negócio e retentativas**: duas consultas/séries independentes, uma para `[FALHA-NEGOCIO]` e outra para `[RETRY-PUBSUB]`, cada qual com `Count`. Não misturar os tipos, não equiparar retry a DLQ ou falha definitiva, não agrupar por `message` inteiro.

**Proibido nesta etapa:** criar índice percentual (original ou alternativo), `distinct_count(cdPropostaSeguro)`, correlação de propostas, métricas inventadas, monitores e alertas. **Não exigir que `@mdc.cdPropostaSeguro:*` filtre os widgets A/B/C.**

## Evidência estabelecida — NÃO repetir auditorias gerais

O responsável já encontrou em Datadog UAT, com `service:sboot-pseg-base-orch-gestao-eventos-conta-cartao env:uat`, registros para:

- Início: `"Iniciando fluxo de recorrência assíncrono para proposta"` (prefixo `[EVENTO-31-PUBSUB]`).
- Sucesso local: `"Retorno com sucesso do orquestrador de recorrência"` (registrado também em janela histórica com tráfego).
- Falha de negócio: `"[EVENTO-31-PUBSUB][FALHA-NEGOCIO]"`.
- Retentativa: `"[EVENTO-31-PUBSUB][RETRY-PUBSUB]"`.

Em UAT, **Include Flex Logs** pode ser necessário. Presença em janelas históricas confirma os eventos, mas **não garante existência de logs recentes** nem sucesso da instrumentação recém-implantada.

As referências são:
- `planos/07-investigacao-ind01-indice-sucesso-conta-cartao.md`
- `planos/08-validacao-telemetria-ind01-conta-cartao.md`
- `planos/09-consolidacao-ind01-e-widgets-conta-cartao.md`
- `planos/10-auditoria-instrumentacao-e-suficiencia-ind01-conta-cartao.md`
- `planos/11-verificacao-versao-implantada-uat-ind01-conta-cartao.md` (anterior à confirmação humana de deploy posterior)

Documentos antigos com queries especulativas NÃO substituem filtros efetivamente testados. Não pedir ao usuário para mudar List → Timeseries apenas para provar novamente que a mensagem existe.

## Regras de workspace e publicação

A raiz lógica do workspace já está na pasta **`darwin-monitor/`**, com `planos/`, `dashboards/`, `uat_darwin_monitor_dashboard.json` e `update_uat_dashboard.py` diretamente sob essa raiz. **NÃO criar `docs/darwin-monitor/` debaixo dela.** Confirme o diretório físico realmente acessível pelo Gemini. Se editor indicar `path not in workspace`, utilize somente caminhos autorizados; não faça cópia estrutural duplicada.

- Faça leitura da estrutura real do JSON local, script existente e, se autorizado e tecnicamente acessível, da definição **remota atual** do dashboard. A última sincronização conhecida ocorreu após correções manuais do IND-07, porém pode ter havido mudanças desde então.
- **Antes de editar o JSON local**, preserve um backup com nome exclusivo e, quando possível, **sincronize remoto → local em modo somente leitura** usando a ferramenta existente e já autorizada. Se não houver leitura remota, NÃO alegue sincronização: pare antes de editar o JSON em risco de sobrescrever mudanças e reporte a pendência.
- Examine os grupos/widgets existentes, suas identidades, títulos, fórmulas, posição/layout e fonte de dados. Preserve INTEGRALMENTE os widgets corretos do **IND-07** e o widget de cancelamento criado manualmente. Não duplicar widgets já existentes: atualizar somente uma definição do IND-01 claramente identificada ou adicionar os três ao agrupamento coerente.
- Não presumir que o número de grupos raiz é número de widgets individuais.
- Use **fonte Logs** com query filters testados e agregação nativa **Count**. Não usar `sum:trace.log_count`, `sum:log.count` ou `message` como faceta; não inventar schema JSON. Obtenha o schema a partir de um widget de Logs válido no próprio dashboard/JSON e das estruturas efetivamente verificadas.
- Se a interface e o JSON oferecerem tipos diferentes de widget, escolha visualização que preserve a interpretação de séries e tendências de volume ao longo do tempo; mantenha a distinção A/B/C e as duas séries do C. Justifique rapidamente escolha e granularidade, sem percentuais não demonstrados. Não mascarar `No Data` de período curto como 0 volume, nem escolher janelas artificiais para aparentar tráfego.
- **Não executar o script de publicação, PUT/PATCH, deploy nem qualquer escrita remota.** Esta etapa é SOMENTE preparação, backup e conferência local da definição.

## Trabalho objetivo

1. Verifique localmente os quatro filtros acima nos relatórios e no código Java apenas se necessário para resolver ambiguidade da mensagem/rota; não repita uma auditoria ampla.
2. Confirme o estado atual do dashboard e faça backup/sincronização segura antes de qualquer alteração local.
3. Insira ou ajuste os widgets A, B e C usando sintaxe Logs real suportada pelo JSON. Disponibilize título, legenda, filtro de service/env e critérios de contagem. Caso falte schema comprovado, **não invente**: documente o impedimento e mantenha o JSON íntegro.
4. Valide localmente a integridade JSON e faça conferência semântica antes/depois (número de widgets, não duplicação, preservação de IND-07 e demais grupos, queries dos três widgets). Se houver meios autorizados de validação local não destrutiva, utilize-os. Ausência de logs recentes **não é bloqueio** para preparar uma definição consistente.
5. Gere relatório **`planos/12-preparacao-widgets-ind01-conta-cartao.md`**, com:
   - situação da implantação nova e lacuna de telemetria ainda pendente;
   - status de sincronização/backup e arquivos modificados;
   - tabela A/B/C com título, fonte, filtros, Count, unidade, interpretação e limitações;
   - widgets existentes preservados e conferência de duplicidade;
   - estado `PREPARADO LOCALMENTE / PUBLICAÇÃO PENDENTE`, ou bloqueio concreto;
   - checklist mínimo após publicação: conferir estrutura visual e filtros; registrar `No Data` quando correto; **somente quando houver um novo log efetivamente posterior ao deploy**, inspecionar `mdc.cdPropostaSeguro` no evento apropriado e a consistência nos outros tipos de evento. Não reexecutar as antigas cinco consultas sem motivação específica.
6. **Não classificar IND-01 como concluído/validado em UAT** nesta fase. Cobertura operacional parcial pode ser reconhecida depois da publicação e verificação dos widgets.

## Resposta final no terminal

Responda objetivamente:
- `JSON local: preparado / bloqueado` e por quê;
- `A/B/C: preparados / parcialmente preparados`, com títulos;
- `Dashboard remoto alterado: NÃO`;
- `Backup e sincronização: sim/não, com evidência`;
- `Ind-07 e outros widgets: preservados / pendente`;
- `Próximo passo: autorização específica do responsável para publicação dos widgets, seguida da validação operacional conforme chegar tráfego`;
- caminho físico verificável do relatório 12.

**Não solicite autorização genérica nem realize publicação silenciosa.**
