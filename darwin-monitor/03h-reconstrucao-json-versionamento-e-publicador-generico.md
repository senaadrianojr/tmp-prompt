# Darwin Monitor — Prompt 03H: reconstrução controlada do JSON, versionamento e publicador genérico

> **EXECUTAR EM UMA NOVA SESSÃO DO GEMINI CLI.** Esta instrução é autossuficiente. **Nesta execução, NÃO publicar, restaurar ou modificar nada no Datadog.** O objetivo imediato é entregar um JSON completo, reconciliado e versionado, com o script de publicação genérico e testado offline. Uma publicação posterior dependerá de autorização específica do responsável e de uma comparação segura com o estado remoto.

## Contexto e incidente

Estamos construindo o dashboard **`[UAT] Darwin Monitor`**, organização BV-DUQ, ID **`ymk-jgs-3dd`**, na migração Darwin/BV. O **IND-07 (Origem DXC/BANKLY/Pismo)** estava correto, com cobertura parcial, após ajustes manuais no Datadog; o **IND-01** exige três widgets operacionais de recorrência. Em uma tentativa de publicação dos widgets do IND-01, o Gemini aparentemente afetou widgets do IND-07, apesar das instruções de preservação. O estado remoto ATUAL pode estar danificado. **Não considerar o último JSON local ou o remoto atual como necessariamente correto.**

O responsável também verificou que o `update_uat_dashboard.py` passou a conter definições de widgets/queries hardcoded. **Isso está conceitualmente errado:** o Python deve ser um transportador genérico que recebe o JSON versionado e o envia para o dashboard selecionado; ele **não** deve decidir, montar, acrescentar, excluir, renomear ou modificar widgets.

### Referências do workspace

Raiz lógica: **`darwin-monitor/`** (descobrir a raiz física real, não duplicar diretórios). Dentro dela:

- `uat_darwin_monitor_dashboard.json`: **artefato canônico** com toda a definição editável do dashboard UAT.
- `update_uat_dashboard.py`: ferramenta de leitura/validação/envio a ser inspecionada e, se necessário, corrigida **somente para remover lógica de widgets hardcoded**.
- `planos/`, `dashboards/`: relatórios e evidências locais.
- Backup relatado antes do Prompt 03G: nome semelhante a `uat_darwin_monitor_dashboard.json.bak-20261104-prompt03f`. **Descobrir o caminho real**; não presumir conteúdo ou existência.
- Relatórios e prompts históricos: 02D, 02E, **02F** (sincronização Datadog → local após alterações manuais), 03C, 03F, 03G, 03G.1; documentos `planos/09-...`, `planos/10-...`, `planos/12-...` se efetivamente existentes. São pistas/evidências, não licença para inventar widgets.

**Não salvar JSON corporativo, cookies, tokens ou informações internas no repositório público `senaadrianojr/tmp-prompt`**. Esse repositório guarda SOMENTE prompts. Versionar o JSON em repositório local/privado aprovado para o workspace. Não alterar Java, instrumentação, monitores ou alertas.

## Objetivo funcional — exatamente quais widgets precisamos recuperar/preparar

**IND-07 — Origem, serviço `springboot-pseg-base-venda-digital`, env:uat**

- Recuperar do estado comprovadamente correto **os widgets de origem por processadora**, sem repetir widgets antigos incorretos ou duplicá-los. Preservar campos, visualização, hierarquia, layout, cores, agregações, queries e semântica originais comprovados; não reimaginar o design.
- Evidência funcional histórica: `@mdc.tipo_evento:Contratacao`, `@mdc.processadora:*`, agregação `Count` por `@mdc.processadora`; valores observados **DXC** e **BANKLY**, com Pismo não observado no período. Não apagar BANKLY nem inventar valores/séries.
- Existia também um widget de `@mdc.tipo_evento:Cancelamento` criado manualmente. Recuperá-lo **somente se sua definição válida estiver no snapshot confiável**; se não estiver, marcar pendente de recriação manual pelo responsável, **sem improvisar**. Ele não representa o IND-03 completo.
- O IND-07 é uma medida de eventos locais de contratação/origem, não de efetivação final de apólice.

**IND-01 — Conta Cartão, serviço `sboot-pseg-base-orch-gestao-eventos-conta-cartao`, env:uat**

Preparar/preservar **três widgets operacionais** de Logs/Count, usando sintaxe e schema de widgets de Logs realmente existentes na configuração:

- **A — Processamentos de recorrência iniciados (Pub/Sub)**: `"Iniciando fluxo de recorrência assíncrono para proposta"`.
- **B — Retornos de sucesso local do orquestrador**: `"Retorno com sucesso do orquestrador de recorrência"`. Não é sucesso final do seguro.
- **C — Falhas de negócio e retentativas (Pub/Sub)**: um widget com **duas séries separadas**: `"[EVENTO-31-PUBSUB][FALHA-NEGOCIO]"` e `"[EVENTO-31-PUBSUB][RETRY-PUBSUB]"`. Retry/NACK não é falha definitiva.

As quatro mensagens já foram encontradas manualmente no Log Explorer em janelas anteriores; **não gastar tempo tentando comprovar novamente sua existência**. Os widgets de volume podem incluir reentregas e tentativas múltiplas. A instrumentação `cdPropostaSeguro` foi implantada recentemente em UAT, mas sua presença em **logs pós-deploy** ainda aguarda validação: não exigir `@mdc.cdPropostaSeguro:*`, não inventar `distinct_count`, join, KPI percentual ou métrica `sum:log.count`. A fórmula percentual original do requisito permanece não validada.

**Outros indicadores, grupos e widgets**: preservar integralmente (inclusive potenciais widgets do IND-02, como "Taxa de Falha Funcional de Recorrência (Cartão)"). Não excluir nem sobrescrever algo por similaridade vaga de título.

## Execução nesta nova sessão — etapas obrigatórias

### 1. Inventariar, sem escrita remota

1. Ler o `update_uat_dashboard.py` **sem executá-lo para publicar**. Identificar quais partes são transporte/autenticação, quais são dados do dashboard e quais são mutações hardcoded de widget.
2. Identificar o JSON local atual, todos os backups pertinentes, eventual exportação/snapshot do estado pós-02F, versões Git disponíveis e relatórios sobre IND-07/IND-01.
3. Se houver **GET de dashboard já autorizado e funcional**, ler somente a definição atual do ID `ymk-jgs-3dd`. Não extrapolar esse acesso para Logs, não descobrir endpoints alternativos, não contornar autenticação/restrições. Se GET não funcionar, informar e continuar com arquivos locais efetivos.
4. Criar um inventário **por widget individual, incluindo os aninhados**, com identificador quando houver, título, tipo, grupo, consulta, hash da definição e **fonte exata** (Git/backup/snapshot remoto/relatório). Não confundir cinco grupos raiz com cinco widgets.
5. Identificar o **último estado comprovadamente correto do IND-07** e o **último estado comprovadamente correto de cada widget do IND-01**. Não usar um backup inteiro indiscriminadamente se parte dele já estiver incorreta.

### 2. Reconstruir UM JSON canônico de dashboard

1. Construir/reconciliar **`darwin-monitor/uat_darwin_monitor_dashboard.json`** com payload completo e publicável, usando uma base integral confiável e aplicando **somente deltas justificados** de IND-07 e IND-01. As definições corretas prevalecem sobre o estado remoto danificado; alterações externas legítimas prevalecem sobre uma base antiga quando não conflitam.
2. Preservar hierarquia, ordem, layout, grupos, template variables, notas, opções de visualização, filtros, `data_source`, storage/Flex Logs e demais propriedades editáveis. Não transportar metadados read-only para o payload de PUT se incompatíveis.
3. Identificar equivalência de widget por **identidade + conteúdo/semântica**, não apenas por título ou posição em array. Evitar duplicatas. **Nenhuma alteração em widget externo aos IND-01/IND-07**, a menos que seja indispensável para preservar uma edição remota comprovada; documentar precisamente.
4. Não criar nenhuma definição de widget dentro do Python, nem em lista ou dicionário embutido. A única fonte de payload do dashboard para publicação será o arquivo JSON.
5. Se não for possível reconstituir algum widget essencial sem inventar consulta, posição ou propriedade, marcar **BLOQUEADO/PENDENTE**, preservar o restante e não declarar JSON completo ou publicável.

### 3. Versionar de verdade com Git (não confundir backup com versionamento)

1. Verificar `git status`, repositório e branch atuais. **Não usar `git add .`** nem incluir credenciais, cookies, tokens, dumps com dados de clientes ou configuração interna em repositório público.
2. Utilizar o repositório **local/privado corporativamente permitido** onde o JSON pertence. Caso ainda não haja um caminho seguro de versionamento, informar explicitamente o impedimento; não inventar commit nem empurrar dados para `tmp-prompt`.
3. Registrar uma referência recuperável do estado anterior, caso já versionado, e efetuar **commit específico** da versão reconciliada do `uat_darwin_monitor_dashboard.json`, com mensagem descritiva. Se o script também for corrigido, versioná-lo em commit separado ou claramente identificado.
4. Exibir branch, **commit SHA real**, caminho do arquivo e `git diff` resumido. Para mudanças futuras: **um novo commit** após cada merge aceito do remoto ou alteração aprovada do JSON. Backups avulsos podem auxiliar recuperação, mas **não substituem histórico Git**.
5. NÃO fazer push para repositório remoto sem permissão explícita e verificação de que o destino é privado/aprovado.

### 4. Corrigir o Python para ser publicador genérico — sem PUT nesta execução

Manter o mecanismo de autenticação corporativa já autorizado; **não mostrar segredos, reescrever autenticação, procurar endpoints alternativos ou contornar bloqueios**. Inspecionar o script e fazer apenas refatoração local necessária para:

- Ler **exclusivamente o arquivo JSON fornecido** como payload de dashboard; aceitar caminho via argumento (por exemplo `--json`) e ID do dashboard via argumento/configuração (por exemplo `--dashboard-id`). Sem queries, nomes de serviços, títulos, listas ou objetos de widgets hardcoded em Python.
- Separar os comandos/modos **validar/dry-run** e **publicar**; publicar somente com opção explícita futuramente. A execução padrão deve ser segura (validação, **sem escrita remota**).
- Verificar sintaxe e formato do JSON e alvo/ambiente. Rejeitar payload vazio, campos incompatíveis e alvo divergente.
- Antes de QUALQUER PUT futuro, obter estado remoto atual por leitura já autorizada e executar **pré-checagem de divergência (drift)** contra a base reconciliada; se houve edição remota após preparar o JSON, **não sobrescrever**. Reportar conflito e exigir merge. Se leitura remota não estiver disponível, não alegar que o drift foi verificado e não publicar automaticamente.
- Validar com **testes locais sem rede e sem envio** (mock de transporte, parsing JSON, validação do alvo, veto a hardcoded, comportamento dry-run). Não fazer uma chamada real só para testar.
- Não transformar o script em gerador de widgets nem editar o JSON durante o envio. O script pode usar os campos técnicos do protocolo necessários à autenticação e ao transporte; **nenhum dado funcional do dashboard codificado em Python**.

### 5. Comparação automatizada e bloqueios

Antes de qualquer publicação futura, exigir relatório automático de delta que compare **toda a árvore**, inclusive widgets aninhados:

- inventário de todos os widgets (antes/depois), IDs se presentes, consultas, estilos, grupos e ordem;
- novos/alterados/restaurados/removidos do IND-07 e IND-01, item a item e com justificativa;
- **zero alteração injustificada** em todos os demais indicadores e metadados gerais;
- divergência remota recente = **bloqueio**, não sobrescrita forçada;
- inconsistência de JSON, duplicatas não justificadas, perda do widget de Cancelamento já conhecido, alterações fora do escopo ou ausência de versionamento obrigatório = **bloqueio**;
- HTTP 200 sozinho nunca substitui comparação de conteúdo remoto persistido.

Não simular aprovação do responsável. Mesmo que o 03G/03G.1 autorizasse uma publicação anterior, **isso NÃO autoriza um novo PUT nesta execução de recuperação**.

### 6. Próxima etapa, DEPOIS desta entrega (não executar agora)

1. Responsável revisa JSON, diff, histórico Git e resultado dos testes. Só então autoriza **uma publicação específica** do JSON consolidado para `ymk-jgs-3dd`.
2. Após essa publicação e sua verificação, o responsável poderá recriar/ajustar **manualmente** no Datadog o widget de `Cancelamento` do grupo Origem, caso necessário.
3. **Somente depois que o responsável informar que terminou a edição manual**, fazer nova sincronização **Datadog → JSON local**, idealmente com **merge de três vias**: base do último JSON publicado/versionado + remoto atual (edição manual) + local atual (trabalho não publicado). Respeitar atualizações em ambos os lados, identificar conflitos e **nunca substituir cegamente** o JSON ou o remoto.
4. Validar o merge, executar novo **commit Git do JSON** com SHA real, e **não publicar de volta** ao Datadog se a única mudança foi importar sua alteração manual.

## Entregáveis e resposta final da execução

- `darwin-monitor/uat_darwin_monitor_dashboard.json`: arquivo canônico completo, **ou declaração explícita do que impede completá-lo**.
- `darwin-monitor/update_uat_dashboard.py`: somente transporte genérico/validador, se a refatoração for possível e segura; comprovar ausência de definições hardcoded de widget.
- `darwin-monitor/planos/13-reconciliacao-dashboard-json-versionado.md`: fontes realmente inspecionadas, mapa IND-07 e IND-01 por widget, estado do Cancelamento, inventário geral, comparação antes/depois, conflitos, testes offline e próximos passos.
- Git: branch e commit SHA verdadeiros para JSON e script, **ou motivo exato da impossibilidade**.
- Status inequívoco: **JSON COMPLETO / PARCIAL / BLOQUEADO**, **VERSIONADO / NÃO VERSIONADO**, **SCRIPT VALIDADO / NÃO VALIDADO**, **PUBLICAÇÃO DATADOG: NÃO EXECUTADA**.

**Não afirmar recuperação, versionamento, teste ou publicação que não tenha sido executado e comprovado. Não pedir autorização que não seja necessária para trabalho local; interromper apenas na falta de uma fonte essencial, de caminho seguro de versionamento ou diante de conflito que exija escolha humana.**
