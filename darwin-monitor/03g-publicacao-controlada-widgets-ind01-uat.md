# Darwin Monitor — Prompt 03G: atualização e publicação controlada dos widgets do IND-01 em UAT

## AUTORIZAÇÃO EXPLÍCITA DO RESPONSÁVEL — LEIA PRIMEIRO

O responsável confirmou:
1. **O backup do dashboard já foi realizado** durante a execução do Prompt 03F.
2. **A versão mais recente do JSON, segundo o responsável, é a que está nesse backup.**
3. **Está autorizada nesta execução a atualização do JSON local e a publicação no Datadog UAT**, com uma condição inegociável: **somente os widgets operacionais A/B/C do IND-01 devem ter suas definições alteradas/adicionadas. Todos os widgets existentes dos demais indicadores e suas configurações devem ser preservados**.

Esta autorização SUBSTITUI as proibições de publicação dos Prompts 03F e 03F.1 **apenas quanto à alteração local do JSON principal e a UM PUT autorizado neste dashboard específico**. As regras de integridade, semântica e não invenção de dados continuam válidas.

**Não interrompa a execução só porque `update_uat_dashboard.py` usa PUT**. Sabemos que o endpoint do Datadog recebe o dashboard INTEIRO, mesmo para adicionar poucos widgets. Logo, "atualizar somente IND-01" significa garantir, por comparação de JSON antes/depois, que **o único delta intencional está nos widgets solicitados**, com o restante do payload inalterado semanticamente. Não prometa um PATCH parcial inexistente.

## Identidade do alvo e escopo

- Datadog UAT: organização **BV-DUQ**
- Dashboard: **`[UAT] Darwin Monitor`**
- ID: **`ymk-jgs-3dd`**
- Serviço: **`sboot-pseg-base-orch-gestao-eventos-conta-cartao`**
- Ambiente: **`env:uat`**
- Escopo: **IND-01 apenas**
- Raiz lógica do workspace: **`darwin-monitor/`**; nela estão diretamente `planos/`, `dashboards/`, `uat_darwin_monitor_dashboard.json` e `update_uat_dashboard.py`.

Não criar `docs/darwin-monitor/planos` DENTRO de `darwin-monitor`. Descobrir e usar a raiz física realmente autorizada pelo ambiente do Gemini.

## Contexto técnico que não deve ser reaberto

Os seguintes tipos de logs já foram localizados manualmente em UAT (em janelas históricas):

- **A — início do fluxo Pub/Sub**: `"Iniciando fluxo de recorrência assíncrono para proposta"` (mensagem com marcador `[EVENTO-31-PUBSUB]`).
- **B — sucesso local do orquestrador**: `"Retorno com sucesso do orquestrador de recorrência"`.
- **C1 — falha de negócio**: `"[EVENTO-31-PUBSUB][FALHA-NEGOCIO]"`.
- **C2 — retry/NACK**: `"[EVENTO-31-PUBSUB][RETRY-PUBSUB]"`.

Usar fonte **Logs**, filtros **`service:sboot-pseg-base-orch-gestao-eventos-conta-cartao env:uat`** e agregação nativa **Count**, conforme o schema de widgets de Logs efetivamente existente no dashboard JSON. Não usar `sum:log.count`, `sum:trace.log_count` nem strings/atributos não comprovados. A consulta de cada série deve reproduzir os marcadores já validados; não insistir em novas buscas para demonstrar novamente sua existência.

A instrumentação **`cdPropostaSeguro` foi implantada recentemente em UAT, segundo o responsável**, mas ainda **não foi comprovada em logs produzidos DEPOIS desse deploy**. Portanto:
- Não bloquear A/B/C pela falta de logs recentes;
- Não incluir filtro `@mdc.cdPropostaSeguro:*` nos três widgets;
- Não criar percentuais, fórmulas corrigidas por conta própria, `distinct_count`, correlação de propostas nem alertas/monitores;
- Não afirmar que ausência temporária de logs seja falha de instrumentação;
- Manter validação futura do campo pendente até chegar evento da revisão nova.

A unidade de A é **ocorrências de processamento iniciadas**, podendo incluir reentregas; não "propostas únicas". B é **retornos de sucesso LOCAL** do orquestrador, sem garantir efetivação final de seguro. C tem **duas séries separadas** para falha de negócio e retentativa, sem equiparar retry a falha definitiva.

## Execução obrigatória — com proteção contra sobrescrita acidental

### 1. Inspeção inicial

- Confirme o backup já criado (o print do 03F indicou um nome semelhante a `uat_darwin_monitor_dashboard.json.bak-20261104-prompt03f`; use **o nome realmente existente**, nunca um caminho presumido).
- Compare byte a byte ou semanticamente o backup com o JSON principal e informe qual versão é a base de trabalho. **Por declaração do responsável, o backup contém a versão mais recente conhecida; use-o como baseline de preservação.**
- Leia o código existente de `update_uat_dashboard.py` e identifique o alvo real da requisição e o payload. Não modificar o script ou sua autenticação.
- Se existir leitura remota **já autorizada e funcional**, obtenha a definição atual do mesmo dashboard por GET e compare com o baseline ANTES de publicar. Se houver alterações remotas novas em widgets fora do IND-01, integre-as à base ou pare a publicação caso não seja possível garantir preservação. **Não substitua alterações de terceiros silenciosamente.** Se GET estiver indisponível, prossiga a partir do backup declarado atual pelo responsável, mas registre o limite: não foi possível provar ausência de drift remoto. **Não ficar tentando endpoints alternativos**.
- Respeite autenticação corporativa existente e jamais exponha cookies, tokens, headers secretos ou IDs de propostas.

### 2. Preparar os três widgets do IND-01

O Prompt 03F.1 pode ou não ter sido executado. Se já existe `dashboards/ind01-widgets-candidatos-uat.json`, verifique e aproveite as definições compatíveis; caso contrário, crie diretamente os três widgets no JSON principal.

Widgets desejados:

| Widget | Título sugerido | Sinal | Agregação / limites |
|---|---|---|---|
| A | Processamentos de recorrência iniciados (Pub/Sub) | Log de início já confirmado | Logs Count, inclui possível reentrega |
| B | Retornos de sucesso local do orquestrador | Log de sucesso local já confirmado | Logs Count, não prova efetivação final |
| C | Falhas de negócio e retentativas (Pub/Sub) | `[FALHA-NEGOCIO]` e `[RETRY-PUBSUB]` | **Duas queries/séries**, Logs Count; não agrupar por `message` completo |

- Inspecione **todos os widgets, incluindo aninhados dentro de grupos**, pois a contagem de cinco widgets/grupos raiz NÃO é o total de widgets individuais.
- Identifique grupos e widgets já existentes do IND-01 por semântica e query, não apenas por títulos e índices. **Se já houver equivalente correto, atualize-o sem duplicar.** Caso não exista, adicione o novo ao grupo coerente, mantendo layout legível e sem deslocar/configurar outros indicadores desnecessariamente.
- Preserve integralmente os widgets corrigidos do **IND-07 Origem DXC/Bankly/Pismo**, incluindo o **widget de Cancelamento criado manualmente** e todos os demais indicadores existentes.
- Usar somente estrutura JSON de visualização de Logs já comprovada; se não houver exemplo suficiente, pesquise em documentação disponível no workspace ou no schema local, sem inventar propriedades ou formatos. Escolher visualização de volumes/trend apropriada e nomes claros, separando C1 e C2.
- Editar **`uat_darwin_monitor_dashboard.json`** com alterações estritamente necessárias e validar sintaxe/estrutura.

### 3. Verificação automática de delta SEMÂNTICO antes de publicar

Gere uma comparação clara entre a base preservada (backup ou GET remoto reconciliado) e o JSON modificado:

1. **Whitelist do delta**: apenas novas definições dos widgets A/B/C ou substituição controlada de equivalentes do IND-01, além dos ajustes mínimos de posição e composição do grupo que os recebe.
2. Para **TODOS os widgets não-IND-01**, comparar conteúdo semântico completo (queries, agregações, estilos, títulos, grupos, layouts, IDs, condições e configurações). **Nenhum deles deve ser alterado ou apagado**.
3. Preservar metadados, template variables, configurações gerais do dashboard, grupo/ordem e elementos que não fazem parte do IND-01. Não modificar widgets antigos de Origem ou outros indicadores; não deixar duplicatas obsoletas.
4. Compare contagem e inventário dos widgets antes/depois, inclusive grupos aninhados. Reporte cada inclusão/substituição de IND-01. Aumento esperado: no máximo três widgets novos, a menos que um equivalente já exista, nesse caso menos; C permanece um widget com duas séries.
5. **Se houver mudança não autorizada fora do IND-01, reverta o JSON e corrija ANTES de publicar.** Se não conseguir preservar os demais, NÃO publique e explique precisamente o impedimento.

O fato de o script realizar PUT completo não é motivo para parar **depois que o payload completo passou por essa proteção**.

### 4. Publicação UAT autorizada

- Com o backup íntegro, delta limitado e JSON validado, execute **uma única atualização** usando o `update_uat_dashboard.py` já existente e autorização corporativa atual, apontando explicitamente para `ymk-jgs-3dd` em **UAT**.
- Faça uma checagem final de alvo para evitar outro ambiente/dashboard. Não publicar em produção. Não alterar o script para implementar outros modos de autenticação nem explorar endpoints não permitidos.
- Capture status HTTP/corpo resumido sem segredos. Se houver falha, **não repetir PUT cegamente**: diagnosticar com as evidências disponíveis e evitar sobrescrever um estado remoto desconhecido.
- Se houver leitura remota autorizada após o PUT, compare a definição persistida com a definição publicada, especialmente A/B/C e preservação dos widgets anteriores. Se não houver GET, não declare comparação remota concluída; solicite ao responsável uma conferência visual focalizada na interface quando necessário.
- **Não reexecutar os cinco testes antigos em Logs**. Verificar widget e seu filtro/legenda/layout quando possível; `No Data` recente é um estado aceitável, não é automaticamente bug.
- Não executar qualquer alteração de Java, deploy do serviço, monitores ou alertas.

### 5. Registro final

Crie ou atualize (se existir) **`planos/12-preparacao-e-publicacao-widgets-ind01-conta-cartao.md`** na raiz correta, documentando:
- baseline e backup efetivamente utilizados, e se houve GET remoto prévio;
- inventário/diff semântico ANTES/DEPOIS, comprovando preservação dos widgets não-IND-01;
- quais A/B/C foram criados/atualizados, com suas consultas, fonte e semântica;
- status da publicação (tentada/sucesso HTTP/fracasso) e validação da persistência remota quando acessível;
- distinção `WIDGET PUBLICADO` vs `TELEMETRIA NOVA AINDA NÃO VALIDADA`;
- validação futura pontual em log **posterior ao deploy** de `@mdc.cdPropostaSeguro` no caminho estruturado real, antes de tentar deduplicação ou índices percentuais;
- nenhuma alegação de que o requisito percentual original esteja concluído.

## Resposta ao responsável — objetiva

Forneça:
1. **Backup/base**: nome do arquivo e confirmação de integridade;
2. **Delta**: lista exata de widgets IND-01 criados/alterados; confirmação de que widgets de outros indicadores ficaram inalterados;
3. **Publicação**: dashboard/ID, HTTP status e situação real; se GET pós-publicação foi executado;
4. **Limitações**: logs recentes indisponíveis e `cdPropostaSeguro` pendente de validação, sem bloquear volumes;
5. **Relatório**: caminho real comprovado.

**Esta é autorização para publicar o IND-01 delimitado, não para recriar todo o dashboard nem alterar outros indicadores.**
