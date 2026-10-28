# DARWIN MONITOR — REDESENHO COMPLETO DO DASHBOARD MVP NO DATADOG
## Novo dashboard organizado por fluxos de negócio — preservando o atual

### PARÂMETROS DA EXECUÇÃO

Preencha ou valide antes de iniciar:
- `DASHBOARD_ATUAL_ID = <ID_DO_DASHBOARD_[MVP]_DARWIN_MONITOR>`
- `NOME_NOVO_DASHBOARD = [RASCUNHO] Darwin Monitor — MVP Reorganizado`
- `DIRETORIO_PROJETO = <CAMINHO_REAL_DO_DARWIN_MONITOR>`
- `DOCUMENTO_MVP = dashboards/02-dashboard-mvp.md` (somente se confirmado no workspace)
- `AMBIENTE_DADOS = <AMBIENTE_AUTORIZADO_PARA_VALIDACAO>`
- `JANELA_VALIDACAO = <INTERVALO_REPRESENTATIVO_COM_DADOS_OU_SEM_EVENTOS_LEGITIMAMENTE>`

Se o ID de origem estiver ausente, solicite-o; NÃO suponha que o dashboard mais parecido seja o correto. Confirme o ambiente e a permissão de criar um dashboard antes de escrever no Datadog.

## OBJETIVO

Atue como **especialista sênior em observabilidade e arquitetura de informação de dashboards Datadog**.

O usuário já criou manualmente o dashboard `[MVP] Darwin Monitor`, mas identificou problemas de organização e de hierarquia. Ele quer **um dashboard realmente novo, construído do zero**, organizado segundo o fluxo das aplicações e a utilidade de cada métrica, **não uma cópia da disposição atual dos widgets**.

**Crie um NOVO dashboard como rascunho comparável ao original. Não reformule nem exclua o dashboard existente.** A intenção é poder avaliar lado a lado o layout antigo e o novo antes de qualquer substituição.

A documentação técnica foi consolidada nas Etapas 01–04. Reutilize-a como fonte de escopo e evidências, mas confira as queries reais do Datadog. Não reabra um discovery amplo nem invente indicadores.

## CONTEXTO VISUAL DO DASHBOARD ATUAL

O dashboard atual contém, entre outros elementos vistos pelo usuário:
- Aba `RECORRENCIA` com grupo `Recorrência de Seguros (Cartão e Débito)`;
- widget de chamadas ao motor externo e seus erros técnicos;
- um quadro chamado `Recorrência Cartão - Taxa de Sucesso Técnico (%)`;
- mensagens ingeridas via GCP Pub/Sub e gravação de ID em repositório central;
- ainda sob Recorrência, elementos de postagem de fatura e pagamento;
- aba `PAGAMENTO E CANCELAM...` com grupo `Saúde do Cancelamento`;
- widget `Cobrança de Seguros Cancelados` que exibe `No Data`;
- widget chamado `Taxa de Falha no Cancelamento (%)` com valor `0`;
- outros grupos de integridade da cobrança e ciclo de vida do pagamento.

**Problemas a resolver:**
1. Métricas operacionais técnicas e indicadores executivos estão visualmente misturados.
2. Etapas diferentes do fluxo (recorrência, postagem, pagamento e cancelamento) aparecem agrupadas sem critério estável.
3. Um painel `0` ao lado de outro `No Data` pode induzir interpretações erradas; investigar a origem sem presumir que sejam contraditórios.
4. Um widget intitulado `Taxa ... (%)` pode exibir contagem ou fórmula incompleta: nunca reaproveitar nome nem valor sem validar numerador, denominador, unidade e período.
5. Visuais de série temporal, valor agregado, eventos e saúde operacional precisam ter funções específicas; não escolher o tipo de gráfico apenas por estética.
6. Existem janelas exibidas como `4h` e sobrescritas individuais como `1d`; padronizar ou explicar, para evitar comparação inválida.

Use essas observações como **pontos de análise, não como inventário exaustivo**. Consulte o dashboard verdadeiro para conhecer todos os widgets, seus IDs, queries e configurações.

## 1. FONTES DE VERDADE E FERRAMENTAS

1. Confirme a raiz real `darwin-monitor/`; NÃO crie `docs/darwin-monitor/` redundantemente.
2. Leia `README.md`, `dashboards/02-dashboard-mvp.md`, `dashboards/03-dashboard-alvo.md`, os documentos canônicos dos indicadores pertinentes, `gaps-e-proximos-passos.md` e o guia `README_FERRAMENTAS_AMBIENTE.md`, **somente quando existirem e forem relevantes**.
3. Use o procedimento corporativo já validado para o Datadog. Caso seja necessária autenticação por sessão, siga o runbook autorizado; não assuma API keys e não force o uso da API pública quando não for o mecanismo documentado. Não compartilhe nem salve cookies, credenciais ou headers de autenticação.
4. Leia **integralmente** o `DASHBOARD_ATUAL_ID` no Datadog e extraia abas, seções/grupos, widgets, título, tipo, consulta, serviço/filtro, template variables, intervalo, ID e resultados de testes.
5. Distinguir três dimensões:
   - **o que o código faz**;
   - **o que a telemetria realmente permite consultar**;
   - **o que a regra de negócio espera medir**.
6. Não assumir que documentos antigos ou widgets existentes têm cálculos corretos. As fontes consultadas e resultados atuais prevalecem sobre rótulos anteriores.
7. Não executar alterações na aplicação, na instrumentação em UAT, nos monitores ou no dashboard de origem.

## 2. ESCOPO DO MVP

**Indicadores de Seguros em foco:**
- Índice de Sucesso: **NÃO construir nova medição funcional nesta tarefa**. A instrumentação `cdPropostaSeguro` ainda está em UAT/QA segundo o usuário. Se houver gráfico de sucesso técnico útil como apoio operacional, só reutilizá-lo com título explícito e fórmula comprovada; não destacá-lo como sucesso funcional.
- Taxa de Falha: pode ter sinais de falha funcional em logs; widget só após confirmar query e o que exatamente conta. Não rotular contagem como taxa.
- Taxa de Falha no Cancelamento: contagem de falhas definitivas pode ser exibida se comprovada, mas **percentual completo permanece pendente de denominador validado**.
- Taxa de Reprocessamento: sinais relativos a DLQ/retries podem ser exibidos como eventos/contagem **com nome tecnicamente correto**, não como taxa de reprocessamento calculada sem fórmula e denominador.

**Fora da implementação atual:** `Parcela Cobrada × Parcela Lançada`, `Origem` e demais indicadores da planilha que dependem de responsabilidade bvseg, base de dados, BI, definição do Negócio ou integrações não disponíveis.

**Outras métricas operacionais já existentes** (ingestão Pub/Sub, chamadas ao motor, registros no repositório, postagem, pagamento etc.) podem ser mantidas no novo dashboard **somente quando a query for válida, o dado for confiável e houver utilidade clara para diagnóstico do fluxo**. Não confundir isso com reativar indicadores executivos fora do escopo.

## 3. PROJETE A ARQUITETURA DO DASHBOARD ANTES DE CRIAR

A organização deve seguir **domínio/fluxo, leitura operacional e relações de causa/efeito**, não somente os nomes atuais das abas.

**Proposta de partida — adapte com base no conteúdo real, sem forçar abas vazias ou redundantes:**

### A. Visão Geral / Saúde do Fluxo (somente se houver dados suficientes)
- panorama curto dos volumes e erros efetivamente comprovados;
- destacar somente métricas úteis para responder "o fluxo está recebendo e processando eventos?";
- mostrar separadamente o que é contagem operacional e o que seria indicador executivo validado;
- **não ocupar a primeira tela com percentuais não comprovados**;
- se não houver dados para panorama confiável, eliminar essa aba e usar o primeiro grupo do fluxo principal como entrada.

### B. Recorrência e Processamento
Organizar do início para o fim do fluxo:
- entrada de mensagens (Pub/Sub) e volume;
- acionamentos ao motor/integrações externas;
- resultados observáveis e falhas funcionais, quando comprovados;
- registros/correlação no repositório;
- retries/reprocessamentos/DLQ em **grupo próprio** identificado como operacional, se houver informação real.

Diferenciar cartão e débito somente se os dados permitirem filtro ou segregação confiável. Caso contrário, usar rótulo conjunto e explicar a limitação.

### C. Cancelamento
- solicitações/volume de cancelamento, somente se fonte validada;
- falhas definitivas de cancelamento, com contagem e janela;
- tentativas/retries que ajudem diagnosticar a falha;
- taxa percentual **somente quando numerador, denominador e regra estiverem comprovados**;
- não usar widget em branco como indicador de falha zero.

### D. Postagem e Pagamento
- eventos consumidos do motor de cartões;
- eventos de fatura liquidada e outros marcos do pagamento;
- consultas/integrações do repositório quando adicionarem contexto útil;
- manter agrupamento próprio, separado de Recorrência e Cancelamento;
- não apresentar conciliação financeira, parcelas lançadas ou dados externos como medidos quando não estão disponíveis.

**Decida no máximo a quantidade de abas necessária para uma navegação clara**, preferindo poucas abas úteis com grupos internos coesos. O painel não deve virar um catálogo de micrográficos nem replicar todos os detalhes técnicos do pipeline.

### Hierarquia visual e UX
- Primeira linha de cada grupo: pergunta que o grupo responde, exemplo `Quantos eventos entraram?`, `Quantos falharam definitivamente?`, `Há mensagens na DLQ?`.
- Use séries temporais para tendências, contadores para volumes/totais verificados e notas para pendências. Não exiba números gigantes sem título, unidade, contexto e janela.
- Agrupar métricas do mesmo processo lado a lado; mostrar entrada, processamento, falha e resultado de forma comparável.
- Títulos em português, curtos, consistentes e inequívocos.
- Diferenciar estados `OPERACIONAL VALIDADO`, `PARCIAL`, `AINDA NÃO CONFIGURADO` no conteúdo/descrição, sem excesso de cores ou banners.
- Cores apenas quando tiverem semântica consistente (falha, sucesso, aviso); não usar vermelho em métrica de sucesso apenas por estética.
- Não misturar `count`, `rate`, `sum`, `distinct` ou `%` como se fossem a mesma unidade.
- Respeitar largura legível em notebook, sem exigir rolagem horizontal excessiva.
- Preferir períodos consistentes e controles globais; exceções por widget exigem justificativa técnica.
- Se filtros de ambiente/serviço/cartão/débito já existirem, preserve a lógica somente quando suportada pelas tags e serviços reais.

## 4. INSPEÇÃO E VALIDAÇÃO DOS WIDGETS

Para **cada widget de origem**, decida:
- `REUTILIZAR COM MESMA QUERY`;
- `RECRIAR COM QUERY CORRIGIDA` (somente com evidência);
- `MOVER PARA GRUPO MAIS ADEQUADO`;
- `NÃO INCLUIR NO NOVO DASHBOARD` (por redundância, cálculo enganoso, ausência de fonte ou fora de escopo).

Examine a **configuração real** antes de decidir. Não reutilize cegamente o JSON do dashboard atual.

Valide, com consultas autorizadas de leitura e intervalo representativo:
- existência e semântica do sinal, serviço, aplicação e ambiente;
- filtros, campos, condições de agregação, unidade e temporalidade;
- comportamento da consulta: dados, zero legítimo, sem dados, erro ou acesso insuficiente;
- impactos de retries, contagens duplicadas e eventos intermediários;
- confiabilidade da fórmula e de suas dependências.

**ATENÇÃO:**
- `0` = zero observado **somente quando a fonte e consulta estiverem corretamente validadas**.
- `No Data` = não houve série retornada na consulta/janela; isso não equivale automaticamente a zero real.
- Se houver duas fontes diferentes para um "percentual", compare sem presumir que se contradizem.
- Contagens de DLQ não são, por padrão, taxa de reprocessamento.
- HTTP 200 não equivale a sucesso funcional do seguro.
- Uma query sintaticamente aceita não é necessariamente uma métrica de negócio correta.

Se não conseguir validar determinada query, **não criar um KPI enganoso**. Em vez disso, omita o widget e registre claramente o gap na resposta final; evite preenchimento cosmético com placeholders que se passam por indicadores.

## 5. CRIAÇÃO SEGURA DO NOVO DASHBOARD

**A autorização é para criar um dashboard NOVO. Não foi dada autorização para modificar, substituir ou excluir o antigo.**

1. Antes de escrever, confirme que a sessão Datadog é a autorizada e que `DASHBOARD_ATUAL_ID` identifica o dashboard correto. Identifique ID, título e quantidade de widgets do original.
2. Monte o projeto de estrutura em memória: abas, seções, posição e tipo de cada widget escolhido. Se a plataforma não suportar alguma organização, adapte aos recursos efetivos.
3. Crie um **novo** dashboard com o nome `NOME_NOVO_DASHBOARD` e descrição `Versão experimental reorganizada; dashboard original preservado. Indicadores parciais sujeitos à validação.`.
4. Verifique antes se já existe um rascunho **desta mesma execução**, para evitar duplicação em retries. Se houver conflito de nome e a identidade do rascunho não for comprovada, interrompa a escrita e solicite orientação; não sobrescreva nem crie clones repetidos.
5. Adicione **somente** os widgets com fontes e consultas verificadas e agrupe pelos fluxos. Não adicionar alertas ou monitores.
6. Preserve no novo dashboard filtros relevantes do antigo **somente se seus campos e semântica estiverem confirmados**.
7. Grave o novo dashboard por método permitido no runbook. Se o ambiente não oferecer ferramenta de escrita válida, NÃO simule sucesso: forneça plano/definição para revisão e explicite o bloqueio.
8. Após salvar, recupere o novo dashboard por leitura e confira ID, título, tabs/seções, widgets, queries, layout e filtros. Execute consulta de validação por widget quando possível.
9. Leia o dashboard original novamente e confirme que permaneceu **inalterado** (mesmo ID, widgets, conteúdo e configuração) em consequência desta operação. Se outra pessoa tiver alterado o original, indique a mudança externa sem atribuí-la ao agente.
10. **Não publique o rascunho como versão oficial, não substitua URLs em READMEs e não altere documentação canônica** antes de aprovação do usuário.

**Não faça rollback destrutivo ou exclusão automática se algo der errado.** Pare, informe quais recursos foram criados e solicite orientação.

## 6. CRITÉRIOS DE ACEITE

O novo dashboard será considerado entregue apenas se:
- for um objeto NOVO no Datadog, com outro ID, e o original permanecer intacto;
- tiver agrupamento coerente com o ciclo de negócio observado;
- cada widget exibir métrica operacional claramente identificada e unidade correta;
- não houver percentuais/taxas enganadores;
- widgets com fontes ambíguas não forem usados como KPIs;
- nenhum indicador fora do escopo ou dependente da instrumentação em UAT for apresentado como já disponível em produção;
- filtros e períodos não comprometerem a interpretação;
- layout e títulos forem claros e navegáveis;
- houver consulta de leitura confirmando persistência da estrutura;
- a conclusão separar o que foi `CRIADO E VALIDADO`, `CRIADO COM LIMITAÇÃO` e `NÃO INCLUÍDO`.

## 7. ENTREGA FINAL OBRIGATÓRIA

Apresente em português:

1. **Novo dashboard**: nome, ID e link real de acesso; informe se foi criado ou se houve impedimento.
2. **Dashboard original**: ID e confirmação objetiva de que não foi alterado.
3. **Mapa da organização**: abas, grupos e widgets, explicando em poucas frases por que essas informações ficaram juntas.
4. **Matriz de migração**: widget original -> novo grupo -> reutilizado/corrigido/não incluído + motivo.
5. **Validação**: query/fonte de cada novo widget, período, status dos dados, limites do cálculo; **sem dados pessoais ou credenciais**.
6. **Métricas não aproveitadas**: motivo e o que faltaria para medição correta.
7. **Indicador de Sucesso**: confirmação de que nenhuma nova medição funcional foi construída nesta rodada.
8. **Próximos passos**: pontos que dependem da promoção de `cdPropostaSeguro` a produção, e possíveis monitores/alertas **para projeto futuro**, sem criá-los agora.

**Pare após criar e validar o novo dashboard. Não apague, renomeie, modifique nem substitua o `[MVP] Darwin Monitor` original sem autorização explícita.**
