# PROMPT 06H — Reconciliação Pós-Publicação UAT/PROD e Encerramento Controlado das Melhorias P1

> **Executor:** Gemini CLI, exclusivamente no workspace corporativo autorizado.
>
> **REPOSITÓRIO PÚBLICO:** este arquivo contém somente metodologia sanitizada. Não escrever ou publicar nele nomes de aplicações/serviços internos, nomes de atributos, consultas e IDs de widgets/dashboards, URLs corporativas, JSONs reais, relatórios privados, logs, dados de clientes, hashes de artefatos internos nem credenciais. Toda a execução e documentação operacional ficam no workspace privado.
>
> **AUTORIZAÇÃO DESTA EXECUÇÃO:** leitura e reconciliação local dos exports JSON atualizados de UAT e PROD, cópias/snapshots privados com hashes, atualização **condicional** do canônico local UAT se a equivalência estiver demonstrada, criação de referência versionada do estado PROD e relatório privado de encerramento. Git **apenas local e para inspeção de histórico/status/diffs**, sem commit automático. **NÃO** publicar, importar, criar ou editar recursos remotos; não alterar código Java, instrumentação, scripts operacionais, dashboards, nem refazer consultas via sessão bloqueada.
>
> **ATUALIZAÇÃO DO RESPONSÁVEL APÓS 06G:** o responsável **já importou manualmente em PROD** o JSON candidato de promoção das duas visualizações P1 e informou que está OK. Também **colocou na raiz do workspace Darwin Monitor os JSONs atualizados, exportados dos dashboards UAT e PROD**. As duas visualizações haviam sido conferidas manualmente com dados em UAT na etapa anterior. Portanto, **NÃO pedir nova publicação nem export por hábito**: localizar e utilizar os dois arquivos realmente existentes na raiz.
>
> **DECISÕES DE GOVERNANÇA:** os três filtros P0 restritivos foram vetados; as duas visualizações P1 (ranking de ocorrências por proposta e stream especializado adicional) foram aprovadas e importadas pelos operadores. Elas **não** alteram o significado das 19 visualizações anteriores. Evoluções P2 e revisões editoriais independentes não estão autorizadas neste ciclo.

## 1. Objetivo e regra de conclusão

Este ciclo deve **comprovar pelos artefatos locais** que as duas publicações manuais foram refletidas nos JSONs exportados de UAT e PROD, reconciliar os arquivos de referência e documentar a entrega P1.

Distinguir obrigatoriamente:
- **afirmação do operador**: importação UAT e PROD foi realizada; UAT exibiu ambos os novos widgets com dados e PROD foi informado como OK;
- **evidência dos exports**: conteúdo/configuração real do dashboard no momento do export manual;
- **validação offline**: estrutura, equivalência semântica, diffs, consultas, grupos, descrições e contagens;
- **telemetria operante em PROD**: NÃO concluir que ambos apresentaram dados só porque o export mostra as queries ou porque o operador disse que a importação ficou OK; registrar fielmente o alcance da declaração.

Somente classificar a promoção como encerrada tecnicamente quando os exports confiáveis e os invariantes definidos neste prompt estiverem demonstrados. Não inventar HTTP 200, GET remoto, testes live ou confirmação visual independente.

## 2. Descoberta controlada e integridade no Git LOCAL

1. Inspecionar **somente a raiz do workspace** em busca dos dois JSONs recentes disponibilizados pelo operador e dos artefatos locais de 06F e 06G; verificar conteúdo, título, ambiente, estrutura e marcas de origem. **Não presumir nomes ou escolher apenas pelo timestamp do arquivo.**
2. Identificar qual export pertence a UAT e qual a PROD, a partir do conteúdo e do contexto privado conhecido. Confirmar, quando presentes, IDs de origem, variáveis, grupos, filtros e metadados. Se o JSON de export não contém identidade suficiente, combinar com nome e declaração do operador, registrando a incerteza.
3. Conferir se são posteriores às publicações manuais correspondentes. A presença dos dois P1 e a comparação estrutural são evidências complementares, mas não substituem uma data comprovada se ela for necessária para atribuição da versão.
4. Localizar o candidato UAT aprovado em 06F, o candidato PROD preparado em 06G, o canônico UAT antigo, o backup PROD-base já preservado e os relatórios privados correspondentes. Não confundir candidato local com export de dashboard remoto.
5. Antes de qualquer escrita: `git status --short`, `git branch --show-current`, `git log -1` e inspeção dos diffs pertinentes. **O workspace usa Git exclusivamente local**; não presumir que árvore limpa significa que não há arquivos ignorados importantes.
6. Preservar os dois exports originais na raiz **sem edição ou movimentação**. Criar cópias/snapshots privados com nomes únicos, hash SHA-256, origem, data de captura conhecida e data da conferência. Não sobrescrever arquivos existentes nem alterar arquivos não relacionados.
7. **Proibido:** `git add`, `commit`, `push`, `reset`, `restore`, `checkout`, `stash`, rebase, limpeza de arquivos ignorados ou qualquer ação destrutiva no Git. Utilizar somente leitura e reportar o estado final.

Se existir ambiguidade de identidade dos exports, parar apenas a reconciliação afetada; continuar as verificações independentes, sem interpretar a ambiguidade como falha de publicação.

## 3. UAT: comparar export pós-importação com candidato e reconciliar canônico

Usar como fontes diferentes:
- **export manual UAT** adicionado pelo operador na raiz;
- **candidato local 06F** homologado visualmente;
- **canônico UAT anterior**, preservado e ainda não sincronizado no relatório 06G.

Validar recursivamente o JSON exportado:
- estrutura raiz (nota/cabeçalho/grupos), quantidade e identidade dos widgets de dados e aninhados;
- dois novos P1 com seus tipos, queries, fonte, agrupamento, colunas, títulos, descrições e layout;
- 19 originais iguais ao canônico anterior, exceto por **normalizações comprovadas** introduzidas pelo Datadog;
- variáveis, ambiente e filtros de UAT adequados;
- nenhuma alteração inesperada em dados, contagens ou significado de widgets existentes.

Comparar **export UAT ↔ candidato 06F** e **export UAT ↔ canônico antigo**. Construir diff semântico auditável; ignorar apenas campos administrativos voláteis ou normalizações justificadas individualmente, nunca ocultar alteração de query, index/storage, layout ou fórmula.

**Só se a equivalência necessária estiver comprovada**:
1. fazer backup imutável do canônico anterior;
2. sincronizar **somente o canônico UAT local** para representar o dashboard UAT publicado, no formato esperado pelos validadores/publicador já existentes; não copiar metadados de dashboard remoto que não pertençam ao canônico;
3. verificar que a mudança canônica é exclusivamente a adição dos P1 (com normalizações formalmente justificadas), sem modificar os anteriores;
4. rodar validador local, calcular hashes e registrar o diff Git; **não comitar**.

Se houver divergência material ou arquivo não atribuível com segurança, **não alterar o canônico**; registrar `CANÔNICO UAT NÃO RECONCILIADO` com diferenças explícitas, sem pedir publicação repetida.

## 4. PROD: comprovação do estado publicado, apenas com os exports

Usar:
- **export PROD pós-importação** adicionado pelo operador na raiz;
- **candidato PROD aditivo 06G** preparado anteriormente;
- **baseline PROD anterior à promoção**, já salvo em backup local.

Verificar:
1. identidade de PROD, estrutura raiz, nota, grupos, variáveis e total de widgets de dados; último estado esperado é **19→21**, mas usar contagem real de cada fonte;
2. ambos os P1 estão no grupo correto, com tipos e detalhes da visualização correspondentes aos aprovados e os filtros corretos de PROD;
3. **export pós-importação vs candidato 06G**: equivalência funcional/semântica, explicitando normalizações reais de import/export;
4. **export pós-importação vs baseline PROD anterior**: acréscimo EXCLUSIVO dos dois widgets P1, sem desaparecimento ou alteração dos 19 widgets originais, seus filtros, avisos, títulos, descrições, formulas, layout e metadados de governança;
5. se outro usuário modificou PROD entre snapshot e export, destacar as diferenças reais e **não** atribuí-las automaticamente à importação P1; exigir reconciliação humana apenas para mudanças materiais não explicadas;
6. nenhum resíduo indevido de filtros/ambiente UAT nas queries dos widgets P1; se houver variável de template, avaliar a resolução de acordo com as variáveis do dashboard (não tratar automaticamente `$env` como erro);
7. não declarar resultado operacional das queries em tempo real sem evidência; a declaração do responsável sobre a importação em PROD é evidência humana distinta da análise JSON.

Se a análise confirmar o estado, criar **snapshot/referência versionada local PROD pós-P1**, com hash, em pasta privada de backups/referências compatível com a organização atual do workspace; não sobrescrever backups, originais de export ou candidatos anteriores. **Não inventar obrigação de substituir um "canônico PROD" se o projeto não mantém esse artefato oficialmente.** Caso exista canônico PROD formal, propor sua atualização ou sincronizá-lo somente se a convenção real autorizar e se o diff for estritamente equivalente; documentar a decisão.

## 5. Critérios objetivos e validação offline

Rodar ferramentas locais já existentes, sempre sem chamadas autenticadas remotas e sem publicação:
- parse JSON e validador/schema de dashboard compatível com a versão local;
- inventário recursivo por grupo, títulos e tipos, presença exata dos dois widgets P1;
- diff semântico original → publicado UAT e original PROD → export PROD, explicitando campo a campo alterações;
- hashes SHA-256 dos quatro arquivos relevantes (dois exports e dois candidatos), do canônico antes/depois e snapshots;
- verificação da imutabilidade dos 19 widgets anteriores em cada ambiente, incluindo descrições e avisos de PROD;
- relatório `git status` antes/depois e lista precisa dos novos arquivos e dos arquivos modificados de forma autorizada.

**Não confundir diferenças esperadas de UAT/PROD com regressão.** O PROD já possuía adaptações e avisos próprios; devem continuar preservados.

Se houver divergência material, reportar `DIVERGÊNCIA` e não declarar a promoção encerrada, mesmo com importação confirmada pelo operador. Não alterar dashboards para "corrigir" durante esta etapa.

## 6. Entregas privadas e encerramento

Produzir um único relatório Markdown em `planos/`, usando próximo número livre, sugestão `42-reconciliacao-final-uat-prod-encerramento-p1.md` (não sobrescrever arquivo existente), contendo:

1. resumo executivo e linha do tempo das confirmações humanas UAT/PROD;
2. inventário e identificação dos dois exports na raiz do workspace;
3. fontes, hashes, backups e integridade do Git local;
4. comparação UAT candidato 06F → export UAT → canônico sincronizado ou bloqueado;
5. comparação PROD baseline → candidato 06G → export PROD;
6. matriz dos widgets por grupo e prova do `+2` sem alteração dos 19 anteriores;
7. diferenças legítimas de ambiente, normalizações e quaisquer divergências materiais;
8. estado operacional confirmado pelo operador versus verificado apenas por JSON;
9. estado final e ações estritamente necessárias;
10. backlog breve separado, sem execução: saneamento de avisos/editorial em PROD **somente mediante evidência**, verificação de cobertura do identificador nos fluxos incompletos, métricas de negócio de baixa cardinalidade (etapa futura) e reconciliações externas/BI quando pertinentes;
11. declaração de **zero ações remotas, zero alteração Java, zero commit ou push e nenhuma alteração em outros dashboards**.

Registrar estado final de forma inequívoca:
- `P1 HOMOLOGADOS E PROMOVIDOS — CANÔNICOS/REFERÊNCIAS LOCAIS RECONCILIADOS`, se ambas as exportações e diffs conferirem e as referências locais estiverem coerentes;
- `P1 PUBLICADOS PELO OPERADOR — RECONCILIAÇÃO LOCAL PARCIAL`, se a importação foi confirmada mas falta evidência para sincronização;
- `DIVERGÊNCIA MATERIAL — ENCERRAMENTO SUSPENSO`, se algum elemento existente mudou ou algum dos P1 está ausente/incorreto no export.

No terminal, informar em poucas linhas: resultado para cada ambiente, total de widgets, hashes/caminhos, status da reconciliação, estado Git local, se falta alguma ação manual do responsável e qual **única frente subsequente recomendada** após o encerramento.

**PARAR.** Não gerar novas publicações ou importações e não iniciar melhorias posteriores neste ciclo. Não solicitar ao operador novo export se os dois arquivos que ele deixou na raiz permitirem concluir a reconciliação.
