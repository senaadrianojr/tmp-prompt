# Darwin Monitor — Prompt 04G: reconciliar o JSON canônico e preparar a publicação controlada do IND-02

> **Executar em uma NOVA sessão do Gemini CLI, com acesso ao workspace local `darwin-monitor/`.**
>
> **Autorização desta etapa: alterações locais e verificações SOMENTE LEITURA.** O responsável aprovou avançar para o 04G e analisar a proposta do 04F, **mas NÃO autorizou publicar no Datadog, fazer commit, push ou PR**. A meta é deixar um artefato canônico local, auditável e tecnicamente pronto para uma autorização posterior de publicação. **Não executar a publicação nesta sessão, mesmo que todas as validações passem.**

## A. Contexto suficiente para a sessão nova

O projeto **Darwin Monitor** é o dashboard de observabilidade do Banco BV. O monitoramento deve utilizar somente fatos observáveis na fronteira dos serviços do time. O Datadog de destino é **BV-DUQ / UAT**, dashboard **`[UAT] Darwin Monitor`**, ID **`ymk-jgs-3dd`**.

Localize o workspace real **`darwin-monitor/`**; a estrutura conhecida contém `planos/`, `dashboards/`, `uat_darwin_monitor_dashboard.json` (canônico) e `update_uat_dashboard.py` (publicador Python genérico, SEM widgets hardcoded). **Não presumir o cwd, nem criar um `darwin-monitor/` dentro de outro.**

**Contexto dos indicadores:**
- **IND-01 — Recorrência (Conta Cartão):** existem 3 widgets operacionais — `IND-01 — Processamentos de recorrência iniciados (Pub/Sub)`, `IND-01 — Retornos de sucesso local do orquestrador` e `IND-01 — Falhas de negócio e retentativas (Pub/Sub)`. O último separa `[FALHA-NEGOCIO]` de `[RETRY-PUBSUB]`. Um retry não é uma falha terminal de adesão. A correlação por `mdc.cdPropostaSeguro` em Conta Cartão é comprovada **parcialmente** em UAT, sem evidência de todos os desfechos por adesão.
- **IND-02 — Taxa de Falha:** a fórmula de negócio na planilha é **adesões com erro / total de adesões × 100**. O widget legado `Taxa de Falha Funcional de Recorrência (Cartão)` usa populações/logs não reconciliados e foi julgado **não confiável como taxa por adesão**. A decisão de consolidação é **remover esse widget**, preservando o widget já existente de falhas/retentativas do IND-01 como **cobertura operacional parcial**, sem fabricar porcentagem ou renomear falhas de log como adesões únicas. Uma taxa de sucesso complementar só poderá existir após comprovação de eventos terminais e denominador compatível.
- **IND-07 — Origem:** existem **3 widgets validados** (contratação por processadora; contratação ao longo do tempo; cancelamento ao longo do tempo). **Preservar os três.**
- **Eventos Pagamento:** instrumentação `cdPropostaSeguro` recentemente implantada em UAT; **ainda sem tráfego pós-deploy suficiente para confirmar o atributo em novos logs**. Não depender dessa evidência para esta alteração do dashboard.

**Histórico relevante:**
- O dashboard UAT possui outros indicadores, widgets SGAT, APM, agrupamentos, queries e Stream de Recorrência que **devem permanecer intactos**.
- Uma correção MANUAL mais recente no Datadog trocou a coluna `@cdPropostaSeguro` por **`@mdc.cdPropostaSeguro`** no widget **Stream de Recorrência**. Essa correção está no export manual recente, mas **não estava** no canônico antigo.
- O responsável colocou um **export manual do dashboard na raiz `darwin-monitor/`**, com data/hora no nome. No 04F, o Gemini identificou como fonte o export **`[UAT]DarwinMonitor--2026-11-05T19_07_44.json`** (nome exibido no relatório; confirme a grafia real). Não presumir o arquivo nem o timestamp de filesystem: reconfirmar conteúdo e hash.
- O **Prompt 04F foi concluído sem publicação**. Relatório **`planos/20-consolidacao-operacional-ind02-sem-publicacao.md`**: comparou o export com o canônico, identificou a divergência no Stream e propôs apenas **remover o widget legado**. Criou **`dashboards/ind02-candidato-uat.json`**, com o export editável inteiro e **uma única diferença semântica** relatada: exclusão do widget `Taxa de Falha Funcional de Recorrência (Cartão)` (ID informado no relatório, a verificar no JSON). Todos os demais widgets, incluindo 3 IND-01 e 3 IND-07, teriam sido preservados. **Essas são alegações do relatório anterior: verificar novamente por diff REAL antes de modificar o canônico.**
- A ferramenta Python `update_uat_dashboard.py` foi anteriormente refatorada como publicador **genérico**, usando argumentos como `--json`, `--dashboard-id`, `--dry-run`, `--validate` e `--publish`. **Conferir `--help` e código antes de usar qualquer opção; não assumir que todo argumento é necessariamente somente leitura. Não usar `--publish`.**

## B. Regras absolutas

1. **NÃO executar qualquer escrita remota no Datadog:** sem PUT/PATCH/POST/DELETE, nem chamadas indiretas a funções/scripts de publicação. Sem editar widgets pela interface. Apenas leitura autenticada **se disponível e comprovadamente read-only**; caso contrário, depender do export manual com classificação de frescor explícita.
2. **NÃO** fazer Git commit, push, PR, merge, checkout forçado, reset, stash, clean ou deploy. Não alterar código Java, configurações corporativas, testes, indicadores de outros serviços, monitores, alertas ou Python publicador.
3. **NÃO** remover widgets adicionais, redesenhar layout, ordenar widgets arbitrariamente, mudar queries, log filters, timeframes, cores, títulos, fórmulas, escopo global, template variables, permissões ou metadados funcionais.
4. **NÃO** enviar cookies, tokens, HTML autenticado, export privado ou arquivos corporativos a repositórios externos, inclusive `senaadrianojr/tmp-prompt`. Não expor segredos em log/relatório.
5. Não afirmar que houve publicação ou que o Datadog remoto está atualizado só porque o JSON local passou na validação.
6. Se identificar **ambiguidade real** (vários exports candidatos, payload incompatível, widget legado não identificável de modo único, drift remoto, alterações locais legítimas conflitantes), **interrompa a modificação do canônico** e entregue diagnóstico e instruções específicas ao responsável. Não remendar silenciosamente.

## C. Etapa 1 — baseline verificável e proteção de arquivos

**Antes de escrever, executar:**
- `pwd`, listagem dos JSONs da raiz com nome/timestamp/tamanho, identificação do export bruto mais recente **por nome E conteúdo**, hash SHA-256.
- `git rev-parse --show-toplevel`, `git branch --show-current`, `git rev-parse HEAD`, `git status --short` **quando estiver de fato em um repositório Git**. Se `darwin-monitor` for subpasta, indicar raiz Git e distinguir alterações existentes; não supor branch `master` nem `main`.
- Hash SHA-256 e estrutura do canônico `uat_darwin_monitor_dashboard.json` e do candidato `dashboards/ind02-candidato-uat.json`. Verificar se ambos existem e são JSON válido.
- Ler `planos/20-consolidacao-operacional-ind02-sem-publicacao.md` e, se estritamente necessário, trechos do relatório 15 sobre semântica do KPI. Não reabrir toda a investigação.

**Confirmar:** o export e o canônico referem-se ao mesmo dashboard `ymk-jgs-3dd`. Validar a estrutura do export — pode ser envelope exportado ou payload direto — e identificar somente os campos compatíveis com o JSON editável exigido pelo publicador. Preservar o **export ORIGINAL intacto** e o **candidato ORIGINAL intacto**.

Inventariar de forma recursiva os widgets aninhados em grupos, inclusive identidade (título, ID quando aplicável, tipo, posição, queries) e verificar:
- 3 widgets IND-01;
- 3 widgets IND-07;
- existência/identificação única do widget legado IND-02;
- o Stream de Recorrência e sua coluna `@mdc.cdPropostaSeguro`;
- outros widgets SGAT, APM e grupos sem perda.

Se houver múltiplas instâncias com mesmo título, validar ID/localização/queries; **não remover por substring**.

## D. Etapa 2 — reconciliação semântica ANTES da atualização canônica

Produzir duas comparações distintas:

**D1. Export recente × canônico atual**
- Mostrar **todas** as diferenças semânticas, incluindo widgets filhos, layout, templates, metadados e ordens.
- O relatório 20 aponta como principal divergência o Stream, mas **não assumir que essa é a única**; se houver outras, classificá-las `correção legítima remota`, `mudança local legítima` ou `indeterminada`, com evidência.
- O export recente é a melhor fotografia local conhecida do remoto; não confundi-lo automaticamente com estado remoto **agora**.

**D2. Export recente × candidato 04F**
- Exigir um delta semântico **exclusivamente** de remoção do widget legado identificado de modo único; somente ajustes estruturalmente inevitáveis e documentados seriam admissíveis.
- Se surgirem outras mudanças não autorizadas, marcar `BLOQUEADO`, **não atualizar o canônico** até corrigir a origem do candidato.

**Verificação de frescor remoto (somente leitura):**
- Se houver método de **GET autenticado, autorizado, que devolva uma definição JSON integral e confiável** do dashboard, comparar com o export; registrar momento da verificação e evidência sem revelar credenciais.
- **NÃO usar HTML de página, presença visual de títulos ou resposta parcial como prova de equivalência JSON.** No ambiente do projeto, acesso Datadog é limitado (cookies + automação/HTML, sem garantia de API de leitura). Se não for possível obter definição íntegra, registrar `ESTADO REMOTO ATUAL NÃO VERIFICADO`.
- Se estado remoto atual **divergir** do export ou houver edição concorrente, não reconciliar a partir de fotografia velha sem decisão humana; pedir novo export completo ou resolução explícita do conflito.
- A **ausência** de verificação remota atual não impede gerar uma **reconciliação local condicionada ao export**, mas **bloqueia autorização automática de publicação**. Revalidar imediatamente antes da futura escrita.

## E. Etapa 3 — criar canônico reconciliado local, com guarda de segurança

**Somente se D1/D2 estiverem plenamente reconciliados** e não houver conflito:
1. Fazer **backup imutado** do canônico anterior em `dashboards/backups/` com nome inequívoco (por exemplo, timestamp + hash do conteúdo), ou se o fluxo do workspace já possuir convenção, utilizá-la. Não sobrescrever backups.
2. Derivar a **versão canônica reconciliada** diretamente do payload editável **mais recente e aprovado** (export + exclusão única do legado). Não reaplicar sobre um canônico desatualizado, pois isso poderia desfazer a correção manual do Stream.
3. Conferir equivalência semântica entre o canônico reconciliado e o candidato do 04F. A única diferença frente ao export deve ser a exclusão do widget legado. Frente ao canônico antigo, devem estar visíveis a **reconciliação do Stream** e a **retirada do widget** e quaisquer outras alterações legítimas do export identificadas e expressamente autorizadas. **Se houver outra divergência não justificada, NÃO substituir o canônico.**
4. Atualizar **somente o arquivo local `uat_darwin_monitor_dashboard.json`**, preservando esquema exigido pelo publicador. Gravar de modo seguro, sem truncamento em caso de erro.
5. **Manter sem alteração** `dashboards/ind02-candidato-uat.json` e o export bruto. Caso seja necessário mudar campos técnicos/metadata por incompatibilidade de formato, explique previamente no relatório e só prossiga se a alteração for não funcional, necessária e testável; caso contrário, bloquear e solicitar decisão humana.

**Caso o export bruto tenha campos read-only**, jamais copiar cegamente um envelope de export como payload de publicação. Compatibilizar usando as regras verificadas no publicador e comparar equivalência semântica da configuração editável, não bytes de metadata não publicável.

## F. Etapa 4 — pré-publicação SEGURA, sem side effects

1. Validar JSON sintaticamente e checar schema compatível com o publisher real. Se o script possuir opção `--validate` ou `--dry-run`, **inspecionar código e confirmar que não escreve remotamente** antes de executar; se o dry-run usar chamada remota ou tiver risco, não executá-lo.
2. Executar diff semântico **export × canônico reconciliado**; whitelisting: **apenas remoção do widget legado**. Executar **canônico reconciliado × candidato**; devem ser funcionalmente equivalentes. Verificar ordem/estrutura e todas as queries/variáveis/IDs de widgets não removidos; atenção aos grupos e filhos.
3. Verificar **Stream = `@mdc.cdPropostaSeguro`**, 3 widgets do IND-01, 3 do IND-07, ausência **apenas** do KPI de falha legado e preservação dos demais.
4. Gerar uma proposta de publicação **sem executar**: qual dashboard ID, qual caminho absoluto do payload, qual mecanismo genérico validado seria usado e quais checks devem ser feitos antes/depois.
5. Definir **condição para publicação futura**: responsável aprova explicitamente, leitura/export remoto recente coincide com baseline ou drift foi reconciliado, backup e diff revisados, nenhum segundo editor alterou o dashboard. Se fonte remota não puder ser lida integralmente, solicitar **novo export manual imediatamente antes da publicação** e conferir a versão; não publicar com base apenas na data antiga.
6. Plano de pós-publicação (apenas documental): GET/export do dashboard, comparar diferenças com canônico, confirmar permanência de todos os widgets e correção do Stream; se houver divergência, **não fazer rollback remoto automático** sem avaliar impactos de edição concorrente.

**Não rodar `--publish`, não copiar/colar token, não acionar interface Datadog de edição, não fazer teste de escrita.**

## G. Entregável e classificação

Criar **`darwin-monitor/planos/21-reconciliacao-canonico-prepublicacao-ind02.md`** (usar localização física correta), incluindo:

1. Export, canônico antigo, candidato e canônico novo: caminhos, hashes SHA-256, tipos de payload e estado Git.
2. Comparação `export × canônico antigo`, destacando Stream e qualquer outra divergência descoberta.
3. Comparação `export × candidato` e `export × canônico novo` demonstrando a alteração única de IND-02.
4. Inventário antes/depois de grupos/widgets, 3 IND-01 e 3 IND-07 preservados, SGAT/APM/Stream preservados, identidade do único widget retirado.
5. Backup realizado; arquivos efetivamente escritos; resultado de JSON validation/dry-run **realmente executado**.
6. Estado remoto atual: `VERIFICADO INTEGRALMENTE` ou `NÃO VERIFICADO` (ou `DIVERGENTE`) — explicar exatamente qual ferramenta ou arquivo fundamentou a classificação.
7. **Classificação**:
   - `CANÔNICO LOCAL RECONCILIADO — AGUARDANDO AUTORIZAÇÃO E VALIDAÇÃO REMOTA FINAL` se local está comprovado, mas remoto atual não foi verificado;
   - `CANÔNICO LOCAL RECONCILIADO — PRONTO PARA APROVAÇÃO DE PUBLICAÇÃO` se local e remoto atual foram comparados integralmente e coincidem;
   - `BLOQUEADO — CONFLITO OU DELTA INDEVIDO` se existe qualquer incompatibilidade/ambiguidade não resolvida.
8. Procedimento exato para etapa posterior de **publicação controlada**, somente após aprovação explícita do responsável.

**Resposta final curta, em uma única conclusão:**
- Qual export e hash usados;
- Diferenças export × canônico anterior;
- Candidato igual ao canônico final? SIM/NÃO/IMPEDIDO;
- Identidade do widget removido, Stream corrigido e contagens preservadas;
- Backup/arquivo local canônico atualizado? SIM/NÃO + motivo;
- Estado remoto atual validado ou não;
- Status da preparação e pendências para autorização;
- Caminho do relatório 21;
- **DATADOG REMOTO / COMMIT / PUSH / PR / DEPLOY: NÃO EXECUTADOS**.

**PARE** ao concluir. **Não iniciar IND-03**, não escrever Java, não modificar Python, não tentar publicar e não dizer que a mudança já está aplicada ao Datadog.
