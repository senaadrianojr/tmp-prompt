# PROMPT 06K — Saneamento de Avisos, Consolidação Pós-Correção e Handoff Integral de Contexto

> **Executor:** Gemini CLI, no workspace corporativo autorizado. **Esta execução tem DUAS entregas obrigatórias e complementares:** (A) análise dos avisos dos widgets e preparação de candidatos editoriais UAT/PROD; (B) um **HANDOFF COMPLETO E AUTOSSUFICIENTE** para retomar todo o projeto numa sessão nova, incluindo reconstrução da análise inicial, decisões, execução real, pendências e achados anteriormente não documentados.
>
> **REGRA ABSOLUTA DO REPOSITÓRIO PÚBLICO:** este arquivo contém apenas método sanitizado. Não publicar em GitHub documentação, JSONs, logs, código, nomes internos de serviços, consultas literais, IDs/URLs corporativos, dados de propostas, screenshots, relatórios privados ou qualquer informação operacional restrita. O relatório, candidatos e HANDOFF devem ficar **EXCLUSIVAMENTE no workspace corporativo/local**.
>
> **AUTORIZAÇÃO:** leitura seletiva de documentação, JSONs, código e relatórios; análise por Python offline; elaboração de matrizes e redações editoriais; criação de **NOVOS candidatos locais** para os dois ambientes, backups privados e relatórios; atualização do canônico UAT e da referência oficial local PROD **SOMENTE para reconciliar exports pós-publicação cuja equivalência funcional esteja comprovada**; elaboração/atualização controlada de um handoff privado. **NÃO** editar remotamente, importar ou publicar dashboards, mudar queries/fórmulas/sources, alterar código Java/instrumentação/infraestrutura, criar métricas/monitores, fazer deploy ou realizar commits/push.
>
> **GIT:** o workspace usa Git **apenas local**. Autoriza-se consulta de status, diff, histórico e commits já existentes; **nenhum `git add`, `commit`, `push`, `reset`, `restore`, `checkout`, `stash`, rebase ou operação destrutiva**.
>
> **ESTADO INFORMADO PELO RESPONSÁVEL:** as duas visualizações adicionais de correlação por proposta estão homologadas e publicadas em ambos os ambientes. Mais recentemente, dois widgets de telemetria Kafka que estavam sem dados por consultas incompatíveis foram corrigidos, e o responsável **validou os novos sinais em UAT e PROD**. Não confundir essa confirmação humana com export remoto, prova de equivalência JSON ou cobertura de todos os outros widgets. O ciclo anterior de reconciliação foi concluído **antes dessas últimas correções**; verifique se a referência local já foi atualizada após elas.
>
> **NOVA NECESSIDADE:** muitos títulos e descrições ainda mostram avisos genéricos de disponibilidade de instrumentação em PROD, apesar de deploys confirmados e registros reais observados. O responsável autoriza **auditar e preparar a remoção seletiva desses avisos** quando desatualizados, preservando limitações reais de cobertura, eventos raros e natureza parcial dos indicadores. **Não há autorização de publicação das alterações editoriais nesta etapa.**

## PARTE A — Auditar avisos e preparar os candidatos editoriais

### 1. Identificar as fontes certas, sem repetir investigações

Localizar e ler de forma direcionada:
- planilha e **análise/discovery inicial**, decisões originais e critérios de monitoramento;
- relatórios privados recentes da reconciliação, diagnóstico de ausência de dados, correção Kafka e homologações manuais;
- **último export efetivamente publicado** de cada ambiente, preferencialmente **posterior à correção Kafka**;
- canônico UAT, referência PROD e candidatos da correção anterior;
- notas sobre implantação das instrumentações e evidências de telemetria já validadas pelo responsável;
- relatórios técnicos/descrições dos widgets e hashes/estrutura de backups.

Conferir o estado do Git local e possíveis alterações preexistentes antes da escrita. Evitar ler novamente todos os relatórios em sequência por hábito: usar índice/citações existentes e abrir somente as seções que sustentam decisões.

**Regra de autenticidade de exports:** arquivo candidato local ≠ export remoto pós-importação. Se o export atualizado de algum ambiente **não estiver disponível ou não for identificável com segurança**, não alegar reconciliação concluída e **não atualizar seu canônico/referência**; preparar o que for possível com a fonte local mais confiável, rotulando como `CANDIDATO PROVISÓRIO — BASE REMOTA NÃO COMPROVADA`. Solicitar, no máximo, **o export específico faltante pela UI oficial**, e não repetir atividades já realizadas.

### 2. Inventário integral dos avisos nos widgets

Percorrer **recursivamente** todos os widgets e grupos **por ambiente**; último total conhecido: 21 widgets de dados por dashboard, mas verificar o número real.

Criar matriz por widget e ambiente:
`grupo | ID/nome real | finalidade e indicador | query/fonte atual | título/descritivo atual | aviso ⚠ existente? | alegação concreta do aviso | evidência de deploy | evidência de sinal em UAT/PROD | limitação funcional | decisão editorial | justificativa/fonte | status da prova`.

**Categorias de decisão obrigatórias (uma por aviso):**
- `REMOVER AVISO DESATUALIZADO`: deployment confirmado e telemetria real da consulta ou sinal pertinente observada no ambiente, ou prova suficiente de que a alegação específica é falsa;
- `REESCREVER COM RESSALVA ESPECÍFICA`: aviso genérico confunde falta de deploy com ausência de tráfego, cobertura parcial, semântica do indicador ou condição excepcional; manter apenas a limitação efetivamente comprovada;
- `MANTER AVISO JUSTIFICADO`: ainda falta comprovação material da capacidade essencial do widget e o aviso corresponde exatamente a ela;
- `SEM ALTERAÇÃO`: não havia aviso ou a descrição existente já é fiel;
- `PENDENTE DE EVIDÊNCIA`: não há prova suficiente para decidir, sem inventar aprovação.

**Não igualar**: código presente em branch, deploy confirmado pelo responsável, atributo indexado, evento observado no Log Explorer, série do widget com dados, disponibilidade contínua e cobertura integral de negócio. Registrar os diferentes níveis de evidência.

**Regras semânticas obrigatórias:**
1. Aviso `aguardando implantação/deploy` deve ser corrigido quando a implantação foi confirmada; falta de tráfego não torna o deploy pendente.
2. Aviso `disponibilidade em PROD a confirmar` deve ser confrontado com dados reais da **fonte/consulta pertinente**; quando comprovado, remover o aviso genérico do título E da descrição, sem apagar ressalvas funcionais.
3. Visualizações de **origem/processadora**, correlação por proposta e Kafka já possuem sinais observados em momentos documentados; aproveitar essas evidências, mas não generalizar para todos os indicadores.
4. Em fluxos de cancelamento, estorno e DLQ, `No Data` pode ser compatível com um evento excepcional sem ocorrência. **Nunca afirmar "saúde plena" ou "zero falhas" somente por `No Data`**. Não trocar `No Data` por zero sem garantia matemática e de fonte.
5. Indicadores originalmente parciais, limites do handoff local, ausência de desfecho externo, DLQ como volume absoluto e dependência DW/BI **mantêm suas ressalvas**. Nenhum ajuste editorial pode insinuar cobertura integral ou taxa de negócio quando não foi comprovada.
6. Os avisos editoriais não são equivalentes a monitores/alertas automatizados do Datadog. Não criar monitores nem alterar limiares.
7. Não usar o aparecimento de dados numa query corrigida como licença para alterar o significado do título (ex.: hits APM de consumo não são automaticamente quantidades de operações financeiras concluídas).
8. O identificador estruturado de proposta é valioso para rastreabilidade e as duas visualizações auxiliares, mas não prova contagem distinta exata de propostas nem desfecho de parceiro.

### 3. Reconciliar localmente as correções Kafka já publicadas — SOMENTE se comprovado

Antes dos candidatos editoriais, verificar se os arquivos de referência locais estão coerentes com o estado **pós-correção Kafka** de UAT e PROD.

- Comparar exports remotos atualizados (se existentes e atribuíveis ao ambiente) ↔ candidatos aprovados da etapa anterior ↔ canônico/referência anterior;
- verificar as duas queries corrigidas e preservar todos os outros widgets;
- separar normalizações do Datadog (documentadas campo a campo) de mudanças funcionais;
- criar backups imutáveis e hashes antes de qualquer sincronização local;
- **apenas com equivalência comprovada**, sincronizar canônico UAT e referência PROD de acordo com as convenções locais; registrar diff e mudanças esperadas; não commitar;
- caso contrário, **não adulterar as fontes**, manter status `RECONCILIAÇÃO PÓS-CORREÇÃO PENDENTE` e indicar o único export necessário;
- as correções de Kafka já foram validadas pelo operador: não refazer seus testes de Metrics Explorer, salvo contradição material nova.

### 4. Preparar candidatos editoriais UAT e PROD — nenhuma query alterada

Criar **dois NOVOS JSONs candidatos privados** com nomes exclusivos/versionados, um por ambiente, cada um baseado na respectiva fonte atual mais confiável e devidamente identificada.

**Alterações permitidas somente nos widgets com decisão documental respaldada:**
- retirar `⚠` do título quando o aviso de disponibilidade genérico estiver superado;
- remover/substituir o trecho correspondente na **descrição** para que título e descrição não se contradigam;
- substituir expressões imprecisas por ressalvas factuais sobre condição de emissão, janela e cobertura (sem prometer zero ou saúde total);
- manter exatamente iguais os 21 widgets quanto a identidade, posição, fonte, queries, formulas, filtros, agrupamentos, layout, grupos, notas estruturais, template variables e permissões;
- **se o widget não tem aviso a corrigir, deixar completamente inalterado**, sem formatação massiva ou reorganização de JSON.

**UAT e PROD não precisam receber a mesma alteração editorial**: avisos específicos de produção só devem existir ou ser removidos onde fizer sentido. Não substituir configuração específica de PROD por um clone UAT.

Se alguma melhoria proposta for de query, de cobertura funcional ou de nova instrumentação, **colocar no backlog**, nunca implementar aqui.

### 5. Validação offline e revisão humana

Executar parse/schema, inventário recursivo e diff semântico para **cada ambiente**, reportando precisamente quais widgets/títulos/descrições mudaram. Preservar outros 21 widgets e toda a estrutura aprovada, sem alterar nem mover widgets.

Critérios:
- nenhum campo fora de `title`/`description` dos widgets aprovados foi alterado;
- avisos removidos têm evidência documentada;
- ressalvas funcionais e operacionais foram preservadas;
- relatórios separam mudança editorial de diagnóstico não resolvido;
- candidatos sem baseline remoto comprovado ficam explicitamente **provisórios**, não "prontos para publicação";
- nenhum resultado de teste remoto inventado;
- `git status` e diffs antes/depois documentados, sem commits.

**PARAR ANTES DA PUBLICAÇÃO.** Entregar os dois candidatos, suas matrizes e diffs para o responsável aprovar a importação manual pela interface oficial. Não acessar a plataforma por cookies, extensão alternativa, DevTools, Selenium, credenciais não autorizadas ou chamadas remotas de escrita.

## PARTE B — HANDOFF COMPLETO PARA CONTEXTO LIMPO (OBRIGATÓRIO)

### 6. Criar um documento de transição autossuficiente

Esta parte é **tão importante quanto a Parte A**. Gerar **um handoff Markdown privado pronto para ser a PRIMEIRA leitura de uma nova sessão Gemini**, no projeto local. Nome sugerido, se livre:

`HANDOFF_DARWIN_MONITOR_2026-11-25.md`

Se já existir, criar um nome versionado **sem sobrescrever**. Apresentar no terminal o caminho correto e um comando simples de continuidade, como `Leia integralmente o HANDOFF e o último relatório técnico antes de sugerir qualquer execução; não altere nada até receber minha autorização.`

**Não produzir um resumo superficial nem apenas colar uma lista de arquivos.** O documento precisa permitir a outro Gemini, com contexto zerado, entender **por que o projeto nasceu, o que foi pedido inicialmente, o que encontramos, o que entregamos, o que evitamos, quais decisões estão em vigor, onde estão os artefatos e como continuar com segurança**, sem depender do histórico da conversa.

### 7. Reconstruir a ANÁLISE INICIAL a partir das FONTES ORIGINAIS

Primeiro localizar os **primeiros relatórios, discovery, planilha e documentos de escopo** do Darwin Monitor. Reabrir os documentos reais, não inferir a análise inicial apenas dos relatórios recentes. Extrair com referências internas:

- demanda original, negócio atendido, aplicações e responsabilidades observáveis;
- quais registros/linhas da planilha foram selecionados, quais rejeitados ou adiados, e por qual motivo;
- definição original de cada indicador: objetivo executivo, interpretação operacional, numerador/denominador **quando realmente definidos**, granularidade e limites;
- distinção entre sucesso do processamento **local** e resultado de negócio externo; situações de cancelamento, estorno, retentativa, DLQ e conciliação;
- que sinais existiam em logs/APM/Kafka no início e quais lacunas foram encontradas;
- instrumentos propostos no discovery (como atributos estruturados de correlação, tags/contadores e eventos adicionais), com motivação original;
- decisões formais sobre indicadores parciais, fórmulas descartadas e dependências fora do perímetro;
- o que constituiria o **dashboard ideal**, o que acabou operacionalmente entregável e qual diferença ainda persiste;
- os erros ou desvios de aceitação que apareceram posteriormente, com consequências e correção do processo.

**Citar no HANDOFF** caminho real, seção/trecho e, quando disponível, data ou commit de cada afirmação substantiva. Se a fonte inicial não estiver localizável, declarar explicitamente `FONTE INICIAL NÃO LOCALIZADA`, listar onde procurou e deixar o ponto `NÃO COMPROVADO`; não preencher com memória do modelo.

### 8. Linha do tempo COMPLETA, fatos, decisões e artefatos

Construir cronologia enxuta mas abrangente, verificando os relatórios efetivos e não apenas a sequência de prompts públicos:

1. descoberta inicial dos indicadores e plano de instrumentação;
2. desenvolvimento/testes dos atributos, correlação, observabilidade e limitações reais;
3. fases de construção e auditoria UAT, reconciliações de queries, descrições e encerramento UAT;
4. primeira promoção da estrutura completa para PROD, backup, autenticação utilizada e evidências de publicação — **não alegar conformidade do método de autenticação sem prova**;
5. situação dos deploys de instrumentação confirmados pelo responsável;
6. descoberta de subutilização da chave de proposta e diagnóstico de aderência;
7. veto aos filtros que ocultariam eventos sem chave, rejeição de fórmulas estatísticas enganosas e desenho/validação das duas P1;
8. construção, importação manual, validação e reconciliação dos P1 em UAT/PROD;
9. diagnóstico de `No Data` nos fluxos condicionais e em Kafka, causas distintas, validações humanas e correção das duas consultas Kafka;
10. estado pós-correção publicado em UAT/PROD, reconciliação local comprovada ou pendente;
11. execução atual 06K: avisos classificados, candidatos locais criados, bloqueios de export e homologação pendente.

Para cada marco relevante: `data aproximada/conferida | relatório/decisão | ação executada | prova | ambiente | estado de homologação | ressalva`. Distinguir **planejado**, **autorizado**, **implementado**, **validado localmente**, **validado visualmente**, **publicado**, **reconciliado** e **pendente**.

Não confundir a existência do arquivo JSON com publicação real. Não declarar finalizado o que depender de novo export ou aprovação do responsável.

### 9. Estado técnico-operacional ATUAL, por indicador e widget

O handoff deve ter, no mínimo:

**A. Matriz por indicador:** todos os indicadores selecionados na planilha, com linha original, finalidade, situação, widgets correspondentes, cobertura operacional versus cobertura de negócio, limitações e ações pendentes.

**B. Inventário de dashboard:** para cada ambiente, identidade e local de canônico/export/backup mais recente, quantidades reais de widgets/grupos, estrutura, template variables, eventuais diferenças editoriais e estado de sincronização. Inventariar widgets prioritários pelo identificador real no relatório privado, não por uma numeração de memória.

**C. Classificação do sinal de cada widget:** fonte (logs/APM/métricas), unidade, evento raro ou de rotina, dados observados ou ausentes (com data/janela), aviso atual, causas demonstradas ou hipóteses. Não chamar `No Data` de `0 eventos` ou de `saudável`.

**D. Instrumentação e seus limites:** propósito original de cada atributo/tag, serviços cobertos, versão/deploy confirmado, possibilidades de propagação, aplicações que não o exigem; onde é usado de modo forense e onde de modo analítico; risco de cardinalidade e de contagem inválida por bucket. Distinguir identificador por proposta de dimensão de baixa cardinalidade em métricas.

**E. Semântica consolidada:** matriz curta de afirmações que o dashboard PODE e NÃO PODE fazer, especialmente fluxos locais, esgotamento de retries, cancelamento, contabilização de proposals, DLQ, dados Kafka e operações externas. Anotar a equivalência de nomenclaturas de negócio/técnicas quando tiver sido confirmada nas fontes privadas.

### 10. ACHADOS TÁCITOS / NÃO DOCUMENTADOS — registrar sem inventar

A pedido do responsável, fazer uma investigação **direcionada** de conhecimento disperso que o executor observou nas análises e que ainda não está claramente registrado em documentos:

- limitações do modelo de dados, regras de negócio e fonte de verdade;
- diferenças reais entre UAT (testes automatizados frequentes, muitos cenários) e PROD (tráfego e falhas excepcionais);
- casos de consulta sem dados por nome de tópico/recurso incorreto, diferença entre valores de atributos distintos e erros de grafia de filtros;
- índices/storage/tags, caminhos de emissão de logs que perdem contexto, rastreabilidade versus contagem de entidades;
- evidências observadas manualmente pelo operador que constaram só em terminal/conversa;
- pontos de metodologia que falharam: query com JSON válido não significa série operante; falta de verificação do recurso emitido; excesso de avisos genéricos; risco de esconder descartes ao obrigar identificador; risco de confundir ausência de sinal com taxa zero ou saúde;
- divergências entre relatórios anteriores, hipótese que foi refutada depois e decisões substituídas;
- problemas realmente sem dono/sem evidência, dependências de outros times e suas implicações.

**Método obrigatório para essa seção:** para cada achado `fato ou hipótese | fonte de primeira mão (código/arquivo/relatório/teste manual) | onde já aparece | por que precisava ser explicitado | consequência para próxima tarefa | grau de certeza`.

Não alegar acesso à memória de sessões passadas que não esteja documentada. Não criar "decisões" retroativas. Se o achado é uma inferência do Gemini, marcar `INFERÊNCIA — REQUER CONFIRMAÇÃO`; se houver conflito entre relatórios, colocar versões lado a lado e indicar a evidência mais forte, sem apagar o histórico. Registrar informação sensível apenas no handoff **privado**.

### 11. BACKLOG remanescente e prioridades, com gates de autorização

Criar mapa priorizado e rastreável, **sem autorizar execução**:

**Fila imediata (escopo do dashboard entregue):**
- revisão/aprovação dos candidatos editoriais 06K, eventual importação manual posterior por operador autorizado e reconciliação dos exports pós-importação;
- pendência de export/sincronização pós-correção se realmente existir;
- validação específica ainda não encerrada de widgets condicionais de cancelamento/estorno/DLQ, somente se faltar evidência;
- monitoramento de sinais reais e revisão dos avisos conforme dados, sem exigir novas auditorias abrangentes.

**Fila de observabilidade:**
- monitores e alertas automáticos: desenho de limiares, fontes, ownership e distinção de ausência de telemetria versus ausência de ocorrência;
- instrumentação/propagação do identificador nas rotas incompletas: discovery focado, não alteração automática;
- melhorias pontuais em títulos, descrições e qualidade semântica dos widgets existentes;
- eventuais consultas mais estáveis e critérios de teste para evitar regressão em nomes de Kafka, facets, serviço e ambiente.

**Fila de evolução executiva:**
- métricas de negócio dimensionais de baixa cardinalidade (etapa de instrumentação separada);
- avaliação tecnicamente correta de contagem distinta em Logs Analytics versus métricas agregadas por intervalos; registrar como investigação/limitação, não impossibilidade matemática universal;
- indicadores de reprocessamento/falha/sucesso mais fiéis somente se houver eventos e populações sustentáveis;
- reconciliações financeiras e resultados históricos externos via sistema de dados/BI, fora do perímetro atual.

Cada item do backlog: `categoria | motivação original | origem/evidência | dependências | impacto | prioridade proposta | status de autorização | entregável/teste de aceite | próximo passo`. Não transformar hipótese em obrigação nem iniciar outra frente automaticamente.

### 12. Kit de retomada de CONTEXTO LIMPO

**O início do handoff deve conter um bloco de até ~15 linhas** com:
- missão e escopo;
- estado exato comprovado;
- única tarefa imediatamente seguinte;
- o que está vetado/não autorizado;
- local dos dois dashboards/fontes/candidatos/relatórios;
- quais arquivos ler primeiro e em que ordem;
- o que perguntar ao responsável **somente se bloquear a execução**;
- frase de controle: `Leia este handoff integralmente e os relatórios indicados, apresente seu entendimento e aguarde autorização antes de qualquer escrita/publicação.`

Adicionar **índice de evidências** com caminhos válidos, relatório/decisão e finalidade, incluindo o que já foi superado. Se possível, incluir comandos seguros `pwd`, `find` restrito a arquivos de projeto, `git status`, `git diff`, e procedimentos de validação offline já existentes — nenhum comando que cause alteração remota ou Git destrutivo.

Ao final, registrar:
- `ÚLTIMO ESTADO VALIDADO`;
- `O QUE AINDA NÃO PODE SER AFIRMADO`;
- `PRÓXIMA DECISÃO A SER TOMADA PELO RESPONSÁVEL`;
- `ARQUIVOS EXATOS QUE DEVEM SER ABERTOS NA NOVA SESSÃO`;
- `INFORMAÇÕES QUE PRECISAM SER PRESERVADAS PARA EVITAR RETRABALHO`.

**Critério de aceitação do handoff:** novo Gemini consegue entender o planejamento inicial, evolução e problemas reais, nomes/papéis dos artefatos, estado por ambiente, motivos das decisões, pendências e próxima operação autorizável **sem pedir novamente um recap ao operador**. Não basta indexar documentos.

## PARTE C — Entregas, prova de integridade e PARADA

### 13. Arquivos obrigatórios no workspace privado

**A. Relatório técnico 06K:** em `planos/`, com próximo número livre, nome sugerido `45-saneamento-editorial-aviso-e-consolidacao-pos-kafka.md`. Incluir matriz 21 widgets por ambiente, evidências e decisões por aviso, status de exports pós-correção, diff semântico dos candidatos, caminhos/hashes, riscos, estado Git e ação humana pendente.

**B. Dois JSONs candidatos:** um UAT e um PROD, em `dashboards/`, com nomes exclusivos; **somente mudanças de títulos/descrições justificadas**. Caso uma base não seja comprovada, rotular explicitamente `PROVISÓRIO`.

**C. HANDOFF integral:** Markdown privado em raiz ou local de entrada claramente identificado, preferencialmente `HANDOFF_DARWIN_MONITOR_2026-11-25.md` (ou versão única se já ocupado), contendo TODAS as seções da Parte B, com referências aos documentos reais e backlog auditável.

**D. Referências locais pós-Kafka:** somente se a reconciliação com exports corretos estiver comprovada e segura; snapshots/backups próprios, sem modificar os exports originais.

### 14. Verificação final e resposta ao operador

Antes de terminar, verificar:
- nenhum dashboard UAT/PROD foi modificado pelo Gemini;
- nenhum widget/filtro/query/fórmula recebeu mudança em candidatos editoriais;
- nenhum código, instrumento de deploy, monitor ou configuração global foi alterado;
- snapshots e exports foram preservados;
- `git status` informa com precisão os arquivos criados/modificados, **zero commits e zero push**;
- handoff aponta para documentos que **realmente existem** e não contém afirmações sem suporte;
- as diferenças e pendências de cada ambiente estão claras.

Apresentar ao operador, de forma concisa:
1. quantidade de avisos por decisão e lista dos widgets afetados;
2. estado UAT/PROD de reconciliação pós-Kafka e se os candidatos estão ou não prontos para aprovação/publicação;
3. relatório e candidatos com caminhos e hashes;
4. nome e caminho do **HANDOFF**, número de seções, fontes consultadas e temas novos registrados;
5. próxima ação manual **somente se estritamente necessária**;
6. frase curta para começar uma nova sessão com Gemini lendo o handoff.

**PARADA OBRIGATÓRIA:** não importar/publicar, não executar alterações operacionais, não comitar, não iniciar monitores ou novas instrumentações. Entregar os arquivos e **aguardar revisão e autorização humana** para a etapa posterior.