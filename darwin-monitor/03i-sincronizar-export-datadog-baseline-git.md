# Darwin Monitor — Prompt 03I: sincronizar JSON exportado do Datadog e criar baseline Git

## Objetivo único desta execução

**Usar o JSON que o responsável exportou manualmente do dashboard `[UAT] Darwin Monitor` para estabelecer uma fonte local confiável, comparar com `uat_darwin_monitor_dashboard.json` e criar um primeiro baseline versionado.**

A escolha do responsável é **preferir remoto → local**, e NÃO realizar um merge complexo por padrão. Entretanto, nunca descarte alterações locais válidas que não estejam no export sem antes identificá-las e reportá-las.

**Nesta execução NÃO publicar no Datadog, NÃO executar PUT/PATCH/POST/DELETE remoto, NÃO alterar widgets pela interface, NÃO fazer push Git e NÃO editar `update_uat_dashboard.py`.** Esta tarefa é sincronização local e versionamento apenas.

## Contexto suficiente para nova sessão

Projeto Darwin Monitor — Banco BV, ambiente Datadog UAT (organização BV-DUQ):
- Dashboard: `[UAT] Darwin Monitor`; ID: `ymk-jgs-3dd`.
- Workspace lógico: `darwin-monitor/`, contendo `uat_darwin_monitor_dashboard.json`, `update_uat_dashboard.py`, `planos/` e `dashboards/`. Localize o diretório físico correto; não crie estruturas duplicadas como `darwin-monitor/docs/darwin-monitor/`.
- A etapa 03H tentou reconciliar o JSON e refatorar o Python. O Gemini declarou `JSON: PARCIAL`, `VERSIONADO: NÃO VERSIONADO`, `SCRIPT: VALIDADO`, `PUBLICAÇÃO DATADOG: NÃO EXECUTADA`. Houve repetição anormal de mensagens de encerramento; **não confie em declarações de sucesso sem verificar arquivos e hashes**.
- **Após isso, o responsável editou o dashboard manualmente no Datadog e exportou o JSON atual**. Esse arquivo exportado é a **referência preferencial** para o baseline, desde que contenha o dashboard correto, com as alterações efetivamente salvas.
- Não presumir o nome nem o caminho do arquivo exportado; procure entre os arquivos locais autorizados, distinguindo-o do JSON canônico e dos backups. **Se não encontrar ou não tiver acesso, peça somente o caminho ou que ele seja colocado na pasta `darwin-monitor/`. Não invente conteúdo.**
- O script Python supostamente foi refatorado para receber ID do dashboard e arquivo JSON, sem queries/widgets hardcoded. A validação técnica desse script é tarefa separada; **não o execute nem o modifique aqui**.

### Inventário visual mais recente revisado com o responsável

**IND-07 — Origem:** o grupo foi recuperado manualmente com **3 widgets**:
1. `Origem — Eventos de contratação por processadora`;
2. `Origem — Volume de eventos de contratação ao longo do tempo`;
3. `Origem — Volume de eventos de cancelamento ao longo do tempo` (recriado manualmente).

O widget antigo `Origem — Segmentação por canal @canal_origem` estava incorreto e deveria ter sido excluído. Preserve os filtros e a definição **real exportada** dos três widgets. Referências técnicas históricas: `service:springboot-pseg-base-venda-digital env:uat`, `@mdc.processadora`, `@mdc.tipo_evento:Contratacao` e `@mdc.tipo_evento:Cancelamento`. Não inventar widget nem sobrescrever consulta exportada com uma reconstrução genérica. Valores DXC/BANKLY foram observados; ausência de Pismo numa janela não significa defeito.

**IND-01 — Recorrência Conta Cartão:** três widgets novos visíveis e inspecionados pelo responsável:
1. `IND-01 — Processamentos de recorrência iniciados (Pub/Sub)`;
2. `IND-01 — Retornos de sucesso local do orquestrador`;
3. `IND-01 — Falhas de negócio e retentativas (Pub/Sub)` — duas séries separadas, falha de negócio e retry.

As consultas são de **Logs/Count**, serviço `sboot-pseg-base-orch-gestao-eventos-conta-cartao`, `env:uat`. Eventos previamente validados: `Iniciando fluxo de recorrência assíncrono para proposta`, `Retorno com sucesso do orquestrador de recorrência`, `[EVENTO-31-PUBSUB][FALHA-NEGOCIO]` e `[EVENTO-31-PUBSUB][RETRY-PUBSUB]`. Não revalidar logs por rotina nem exigir `@mdc.cdPropostaSeguro:*`; a instrumentação recente ainda aguarda confirmação em logs pós-deploy.

**Widgets legados preservados até investigação própria**: `Taxa de Falha Funcional de Recorrência (Cartão)`, `Índice de Sucesso — Débito em Conta (SGAT)`, `Recorrência Cartão — Taxa de Sucesso Técnico nas Chamadas HTTP (APM)`, `Stream de Recorrência em Tempo Real (Validação @cdPropostaSeguro)`. **Não remover, reescrever ou corrigir fórmulas deles nesta tarefa.** Os demais grupos/widgets de Saúde da Ingestão e Fluxos também devem ser preservados conforme a definição exportada.

Os títulos são pistas para reconhecimento, não uma lista rígida com permissão de excluir qualquer widget cujo nome difira. O inventário deve ser extraído do JSON real, **inclusive grupos aninhados**.

## Etapa 1 — Localizar e validar as fontes, somente leitura

1. Localize o JSON **exportado manualmente do Datadog**, o JSON **canônico local**, backups e, se existir, histórico Git. Registre caminhos exatos, datas de modificação reais, tamanhos e SHA-256 calculados. Se nomes parecidos existirem, não escolher por intuição.
2. Leia o conteúdo do export e confirme que é um JSON válido e refere-se ao dashboard UAT esperado. Identifique se é **payload direto editável** ou **export com envelope/metadados adicionais**. Se o arquivo não contiver identificação inequívoca do dashboard, peça confirmação apenas desse ponto antes de substituir o canônico.
3. Faça o mesmo para o JSON canônico. Não execute o script Python nem um comando de publicação; nenhuma chamada remota é necessária porque o export já foi fornecido. Não faça chamadas de rede de descoberta, API de Logs ou contornos de autenticação.
4. Preserve o arquivo exportado **original, imutável**. Antes de qualquer substituição local, mantenha uma cópia recuperável do JSON canônico atual, com hash e nome distinto; caso já haja controle Git, prefira também registrar o conteúdo anterior de forma rastreável, sem descartar alterações.

## Etapa 2 — Comparação semântica completa

Compare **export vs. JSON local** de forma estruturada, e não apenas `diff` textual:
- Dashboard inteiro: título, layout, template variables, tags, settings e demais propriedades editáveis.
- Todos os grupos e **todos os widgets aninhados**, com ordem/posição, ID (se houver), tipo, título, query, fonte (Logs/APM), `compute`, `group_by`, `rollup`, storage/Flex Logs, fórmula, visualização, estilo e parâmetros relevantes.
- Detecte widgets removidos, adicionados, modificados, duplicados e rearranjados; registre claramente em qual fonte estão. A mera existência de 5 grupos raiz não significa 5 widgets.
- Classifique cada divergência como **(a) alteração remota desejada**, **(b) diferença técnica de envelope/metadado somente-leitura**, **(c) possível mudança local válida ainda não publicada**, ou **(d) conflito/incerteza**.
- Confira se os 3 widgets de Origem e os 3 widgets novos do IND-01 estão realmente presentes e preservados no export. O cancelamento manual agora deve aparecer no JSON exportado; se não aparecer, **não o invente** e reporte a divergência.
- Não confunda widgets legados de outros indicadores com duplicados do IND-01 por estarem no mesmo grupo ou terem a palavra “recorrência”.

## Etapa 3 — Decisão determinística antes de sincronizar

**Caso A — Export íntegro e nenhuma alteração local válida pendente**:
1. Trate o JSON exportado como baseline.
2. Converta somente envelope/metadados read-only se o payload editável usado pelo Datadog exigir, sem alterar semanticamente nenhum widget.
3. Atualize **somente** `darwin-monitor/uat_darwin_monitor_dashboard.json` de modo seguro (escrita em arquivo temporário + validação + substituição atômica quando possível).
4. Verifique equivalência semântica export → canônico resultante e confira novamente os inventários.

**Caso B — Existem alterações locais potencialmente válidas que não constam no export**:
1. Não descarte, não misture silenciosamente e **não sobrescreva** o canônico.
2. Gere uma lista pequena das diferenças, com widget/grupo e fonte, indicando exatamente qual decisão precisa do responsável.
3. Proponha merge **somente se for realmente necessário**. A decisão preferencial continua sendo remoto → local.

**Caso C — Export incompleto, desatualizado, de dashboard incorreto ou incompatível**: não substituir o canônico nem declarar baseline pronto; identificar um único bloqueio objetivo.

**Importante:** esta execução é de **importação da versão exportada**, não de edição ou reconstrução de widgets, e não deve fazer o JSON local parecer “mais correto” que o próprio export com base em relatórios históricos.

## Etapa 4 — Criar versionamento Git local, com segurança

Após concluir **Caso A**, sem conflitos:
1. Verifique se `darwin-monitor/` já está dentro de repositório Git, inclusive em pasta ancestral. Reuse o repositório **local/privado autorizado**, sem criar repositório aninhado desnecessário.
2. Se não houver Git e a política corporativa permitir, pode iniciar **um repositório Git local** para o workspace; **não é necessário GitHub remoto para commitar**. Se isso for proibido, documente e pare essa etapa sem alegar versionamento realizado.
3. Verifique `git status` e o staging area; não incluir arquivos de configuração, segredos, backups, exportações confidenciais, cookies, tokens ou alterações não relacionadas. **Nunca `git add .`**. Não criar commits com arquivos extras já staged; não forçar autoria/configuração Git desconhecida.
4. Registre em commit identificável o JSON canônico com mensagem como `chore(darwin-monitor): establish UAT dashboard baseline from exported Datadog JSON`. Se houver versão anterior rastreável, preserve possibilidade de comparação/rollback local.
5. Forneça **branch, SHA real do commit, nome do arquivo rastreado** e `git status` final. Não executar push nem publicar em repositório público. Não versionar o JSON corporativo em `senaadrianojr/tmp-prompt`, que é somente repositório de prompts.
6. Backups avulsos não substituem o Git. Daqui em diante, cada alteração aceita do JSON ou importação de alteração manual deverá gerar um novo commit.

## Etapa 5 — Evidência final e parada

Crie `darwin-monitor/planos/14-baseline-json-remoto-para-local.md` contendo:
- fonte exportada, caminho, data e SHA-256;
- arquivo local anterior e hash;
- resultado da comparação completa (contagem por grupo e widgets totais, inclusive aninhados; mudanças por widget);
- classificação das divergências e decisão A/B/C;
- indicação explícita da preservação dos 3 widgets Origem, 3 widgets novos IND-01, 4 widgets legados e demais grupos **conforme presença real no export**;
- se houve transformação técnica de envelope e por quê;
- caminho/hash final e resultado da equivalência semântica;
- status Git e commit SHA comprovado;
- pendências pontuais, se existirem.

Na resposta final apresente, em até 10 linhas:
- **Export Datadog identificado:** SIM/NÃO;
- **Comparação concluída:** SIM/NÃO;
- **Alterações locais válidas pendentes:** SIM/NÃO/INCERTO;
- **Local sincronizado com export:** SIM/NÃO;
- **Widgets Origem / IND-01:** contagens verificadas;
- **Versionamento Git:** SIM/NÃO e commit SHA real se houver;
- **Python alterado ou executado:** NÃO;
- **Publicação Datadog:** NÃO EXECUTADA;
- **Relatório:** caminho comprovado;
- **Único bloqueio/decisão humana:** se aplicável.

**Pare ao finalizar. Não inicie publicação, correção de indicadores, refatoração de Python, nova auditoria de logs nem uma longa sequência de tarefas adicionais. Não repita indefinidamente mensagens de conclusão; emita uma única resposta final comprovável.**