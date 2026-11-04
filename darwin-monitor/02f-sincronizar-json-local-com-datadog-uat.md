# Darwin Monitor — Prompt 02F: Sincronizar o JSON local com o dashboard UAT atual

## Objetivo único

Sincronize o arquivo local `uat_darwin_monitor_dashboard.json`, localizado na raiz do workspace `darwin-monitor`, para representar **fielmente o estado ATUAL do dashboard [UAT] Darwin Monitor no Datadog**, após alterações manuais realizadas pelo responsável.

**A direção da sincronização é obrigatoriamente: DATADOG UAT → ARQUIVO LOCAL.**

**NÃO execute `update_uat_dashboard.py`. NÃO faça PUT, PATCH, POST de alteração ou qualquer publicação. NÃO tente "corrigir" o dashboard remoto para corresponder ao JSON antigo.**

O estado remoto é a fonte de verdade desta tarefa.

## Contexto que precisa ser respeitado

Após a publicação anterior, o responsável verificou que **dois widgets antigos de Origem, com consultas incorretas, continuaram no dashboard**, enquanto os widgets novos corrigidos também foram adicionados.

Em seguida, o responsável **editou manualmente o dashboard UAT**:

1. Excluiu os dois widgets antigos incorretos.
2. Manteve ou reorganizou as visualizações corrigidas de Origem.
3. Criou um **novo widget com informações do tipo de evento `Cancelamento`**.

O arquivo `uat_darwin_monitor_dashboard.json` local está **desatualizado em relação a essas mudanças**.

**Não assuma quantidades finais, nomes, tipos, IDs ou posições dos widgets.** Descubra-os na definição remota atual. Não recrie widgets removidos só porque aparecem nos documentos antigos.

O dashboard deve continuar útil para monitorar eventos observáveis na fronteira das aplicações durante o projeto Darwin. A investigação IND-07 estabeleceu os filtros reais do Datadog em atributos MDC:

- `@mdc.tipo_evento:Contratacao`
- `@mdc.tipo_evento:Cancelamento`
- `@mdc.processadora:*`
- Agrupamento observado: `@mdc.processadora`

Esses filtros ajudam a reconhecer os widgets, mas **NÃO autorizam redesenhar ou reescrever as queries remotas**. Nesta tarefa, copie o estado atual do Datadog tal como está.

## Etapa 1 — Identificar ferramenta de leitura já existente

1. Localize `uat_darwin_monitor_dashboard.json` e `update_uat_dashboard.py` na raiz real do projeto.
2. Leia o script sem executá-lo. Identifique somente a forma de acesso **já autorizada** ao Datadog via API legada e cookies/CSRF, utilizada nas execuções anteriores.
3. Confirme pelo código ou pelos resultados documentados que o dashboard UAT pode ser consultado por **requisição de leitura**. A execução anterior informou uma leitura após PUT com HTTP 200; **confirme o mecanismo real, sem adivinhar endpoints**.
4. Não exponha nem copie o conteúdo dos cookies, tokens, CSRF ou headers para terminal, chat, logs ou documentação.
5. **Use apenas operações de leitura na API existente**. Não modifique a ferramenta de autenticação nem contorne restrições de acesso.
6. Se a API não fornecer a definição completa do dashboard, pare e solicite a **exportação JSON atual pela interface Datadog**, em vez de reconstruir o dashboard com base em relatórios históricos.

## Etapa 2 — Obter o estado remoto atual

1. Faça uma leitura autenticada e autorizada do dashboard **UAT correto**, confirmando título e identificador com base no script ou na configuração local, sem confundir com DES/PRD ou outros dashboards.
2. Leia a **definição completa e atual**, incluindo grupos, widgets filhos, layout/ordem, queries, `data_source`, `group_by`, `compute`, `storage`/Flex Logs, template variables, notas e propriedades relevantes.
3. Inspecione a estrutura da resposta: a API pode fornecer a definição dentro de um envelope com metadados administrativos. Identifique a parte correta que é o **payload utilizável pelo script de publicação**. Não inclua campos somente-leitura que inviabilizem um futuro PUT; não descarte campos editáveis necessários.
4. Confirme pela leitura remota se constam:
   - os widgets corrigidos de contratação/origem;
   - o novo widget relacionado a cancelamento;
   - a ausência dos dois widgets antigos excluídos;
   - todos os outros grupos e widgets existentes.
5. Trate os itens como **expectativas narradas pelo responsável**, a confrontar com o remoto. Se houver divergência, não a corrija silenciosamente: reporte exatamente o que a resposta remota mostra.

**Importante:** não use `03-dashboard-alvo.md`, `05-investigacao-ind07-origem-venda-digital.md` nem o JSON local antigo como fonte de verdade para o estado atual. São referências históricas.

## Etapa 3 — Backup local e substituição controlada

1. Faça backup do **arquivo local anterior** antes de alterá-lo, com nome inequívoco (por exemplo, `uat_darwin_monitor_dashboard.pre-sync-2026-11-04.json`, ou versão segura conforme a data real de execução).
2. Preserve também uma cópia **local e protegida** da resposta remota original, caso o mecanismo corporativo permita, para auditoria e recuperação. Não publique arquivos internos em repositório público.
3. Normalize a resposta apenas no estritamente necessário para manter a estrutura de payload esperada por `update_uat_dashboard.py`. É permitido formatar/identar o JSON, mas **não reordenar widgets nem alterar conteúdo funcional**.
4. Substitua o conteúdo de `uat_darwin_monitor_dashboard.json` pelo estado obtido **diretamente da definição remota atual**.
5. Não edite `update_uat_dashboard.py` nesta tarefa. Se seu formato esperado não for compatível com o JSON de leitura, documente a diferença e **pare sem gravar uma versão potencialmente inaplicável**, em vez de improvisar campos ou modificar a rotina de publicação.

## Etapa 4 — Validar a equivalência

Execute validações locais somente de leitura:

- JSON sintaticamente válido.
- Mesma quantidade de grupos raiz e widgets (inclusive widgets aninhados), mesma hierarquia e mesma ordem da definição remota.
- Mesmos títulos, tipos, IDs quando disponíveis, filtros e queries, agrupamentos, armazenamento/Flex Logs, variáveis de template e layout.
- Conferência específica da preservação do novo widget de cancelamento e da ausência dos dois widgets removidos.
- Compare **semanticamente** o payload local resultante com a definição editável retornada pelo Datadog, ignorando apenas diferenças inevitáveis de metadados somente-leitura ou formatação. Não aceite divergência em propriedades funcionais.
- Confirme que o arquivo atualizado ainda é compatível com o formato que o script local espera para futuras publicações, **sem executar o script**.

Se houver alguma divergência semântica, não declare sincronização concluída.

## Etapa 5 — Relatório curto e rastreável

Crie um registro local conciso em `docs/darwin-monitor/planos/06-sincronizacao-dashboard-uat.md`, ou adicione uma entrada equivalente a um log existente de sincronizações, com:

1. Data/hora da leitura e ambiente UAT.
2. Origem da definição remota (mecanismo autorizado; sem segredos).
3. Caminhos do backup e do JSON local atualizado.
4. Comparativo antes/depois: quantidade de grupos/widgets e mudanças relevantes (inclusão do widget de cancelamento, remoção de widgets antigos, outras mudanças remotas reais).
5. Resultado da validação semântica local × remoto.
6. Status da operação: **SINCRONIZADO / NÃO SINCRONIZADO**, e eventual impedimento.

Não copie logs de clientes, credenciais nem payloads internos para esse relatório.

## Resposta final

Informe, de modo objetivo:

- Leitura do dashboard remoto: **SIM/NÃO**.
- JSON local substituído pelo remoto: **SIM/NÃO**.
- Quantidade de grupos/widgets remotos e locais.
- Widget de cancelamento preservado: **SIM/NÃO/NÃO CONFIRMADO**.
- Widgets antigos removidos continuam ausentes: **SIM/NÃO/NÃO CONFIRMADO**.
- Equivalência semântica: **VALIDADA/NÃO VALIDADA**.
- Caminho do backup e do relatório.
- Confirmação explícita: **nenhuma atualização foi enviada ao Datadog**.

**Encerre a tarefa após sincronizar. Não publique o dashboard, não inicie outro indicador, não modifique código Java e não recrie os widgets antigos.**
