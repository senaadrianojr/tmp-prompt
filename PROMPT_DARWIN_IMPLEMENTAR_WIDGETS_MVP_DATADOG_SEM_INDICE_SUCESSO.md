# DARWIN MONITOR — IMPLEMENTAÇÃO CONTROLADA DE WIDGETS MVP NO DATADOG

## PREENCHER ANTES DE EXECUTAR

- `DIRETORIO_DOCUMENTACAO_DASHBOARDS = <CAMINHO_REAL_NO_WORKSPACE>`
- `ARQUIVO_DEFINICAO_MVP = <CAMINHO_RELATIVO_DO_ARQUIVO_MVP>` (candidato existente: `dashboards/02-dashboard-mvp.md`; confirme)
- `DATADOG_DASHBOARD_ID = <ID_REAL_DO_DASHBOARD_DE_DESTINO>`
- `AMBIENTE_DADOS = <AMBIENTE A CONSULTAR, ex.: prod>`
- `PERIODO_DE_VALIDACAO = <INTERVALO COM DADOS CONHECIDOS>`

Se qualquer campo essencial não tiver sido preenchido, **não realize alterações no Datadog**. Localize os caminhos da documentação quando possível, mas nunca deduza o dashboard de destino nem altere outro dashboard por semelhança de nome. Solicite o identificador correto.

## MISSÃO

Atue como engenheiro de observabilidade responsável por **implementar e validar os widgets do MVP do Darwin Monitor no dashboard explicitamente indicado**.

A investigação, classificação e consolidação dos indicadores já ocorreram. Não repita as Etapas 01–04 nem amplie o escopo.

Use as especificações já existentes em `DIRETORIO_DOCUMENTACAO_DASHBOARDS`, especialmente o arquivo `ARQUIVO_DEFINICAO_MVP`, mais os documentos canônicos dos indicadores e `README_FERRAMENTAS_AMBIENTE.md`.

**Autorização operacional desta tarefa:** criar somente os widgets novos dos indicadores explicitamente incluídos abaixo, no dashboard de ID `DATADOG_DASHBOARD_ID`, após validar suas fontes e queries, preservando todos os widgets atuais. Não criar alertas, monitores ou modificar código. Não alterar outros dashboards.

## ESCOPO APROVADO — SOMENTE TRÊS INDICADORES

1. **Taxa de Falha** — implementar um widget de *contagem de falhas funcionais observadas*. NÃO rotulá-lo de percentual/taxa enquanto a fórmula completa, os denominadores e os dados não forem comprovados.
2. **Taxa de Falha no Cancelamento** — implementar um widget de *contagem de falhas definitivas de cancelamento* somente se o sinal realmente se referir a cancelamentos após esgotamento de tentativas. NÃO representar contagem como taxa percentual. Se a query proposta contar erros de outro fluxo, não criar o widget; explicar o impedimento.
3. **Taxa de Reprocessamento** — a documentação anterior descreve sinais de DLQ/retries. Implementar, **somente se confirmada a semântica**, um widget rotulado *Mensagens enviadas à DLQ* ou outra métrica operacional efetivamente medida. **DLQ não equivale a número de operações reprocessadas**, e não pode ser apresentada como "Taxa de Reprocessamento" sem numerador e denominador adequados.

**Expressamente fora da execução:**
- **Índice de Sucesso:** NÃO criar nem alterar widget de sucesso nesta tarefa, por decisão do solicitante. A instrumentação `cdPropostaSeguro` está em UAT/QA segundo informação do usuário; não esperar seu deploy para avançar nos demais.
- Parcela Cobrada × Parcela Lançada e Origem (pendentes de PO/Negócio, responsabilidade `bvseg`).
- Todos os demais indicadores da planilha.
- Dashboards definitivos, métricas customizadas futuras, instrumentações novas, APM para a feature em homologação, alertas e monitores.

**Não invente thresholds, percentuais, denominadores nem valores.** Use títulos e descrições que expressem claramente o que a query realmente conta.

## ETAPA A — LEITURA E PLANEJAMENTO SEM ESCRITA

1. Confirme a raiz real de `darwin-monitor/` e leia o documento MVP e os arquivos canônicos:
   - `indicadores/02-recorrencia-taxa-de-falha.md`
   - `indicadores/06-cancelamento-taxa-falha-cancelamento.md`
   - `indicadores/04-recorrencia-taxa-reprocessamento.md`
   - `dashboards/02-dashboard-mvp.md`
   - os demais documentos existentes citados diretamente, se forem necessários.
2. Use as instruções verificadas no `README_FERRAMENTAS_AMBIENTE.md` e o runbook corporativo autorizado para acessar o Datadog. A autenticação pode usar sessão corporativa previamente validada; **não presuma existência de API Key/Application Key**, não insista na API pública se não for o procedimento documentado.
3. Verifique acesso de leitura, identidade do dashboard-alvo e existência dos widgets. Recupere o layout/configuração original e prepare cópia de segurança *em memória* (ou hash/identificador de revisão). Não salve cookies, token ou payloads sensíveis em arquivos.
4. Para cada um dos três indicadores, estabeleça:
   - relação com aplicação/serviço real;
   - padrão exato de evento/log;
   - filtros, índices, labels/tags, time range e agregação;
   - contagem de eventos versus contagem de operações únicas, com deduplicação quando possível;
   - tipo e título coerente do widget;
   - evidência na documentação e, se houver, no código;
   - limitação que deverá aparecer na descrição do widget.
5. **Verifique se já existe um widget equivalente.** Se houver, não duplique e não altere sem autorização específica; reporte a equivalência.
6. Não suponha que a presença de uma string no código comprova indexação da mensagem em produção.

## ETAPA B — VALIDAR QUERY E DADO REAL

Antes de criar qualquer widget:

- Consulte a query real no ambiente e período definidos usando ferramenta corporativa de acesso já verificada.
- Confirme se os campos e os filtros existem no índice apropriado e se a consulta retornou dados reais ou zero eventos, ou falhou.
- Diferencie: `QUERY VALIDADA COM DADOS`, `QUERY EXECUTADA COM ZERO RESULTADOS`, `CONFIGURAÇÃO VALIDADA, EXECUÇÃO NÃO COMPROVADA`, `QUERY INVÁLIDA` e `DADO INDISPONÍVEL`.
- Um `No Data` não é evidência automática de ausência definitiva de dados. Verifique janela, serviço, ambiente, filtro, retenção e permissões **sem ampliar indefinidamente a busca**.
- Não construa um widget enganoso quando a consulta não comprovar que mede o fenômeno desejado.
- Se a query tiver zero dados mas for sintaticamente válida e o usuário tiver explicitamente indicado um cenário legítimo de baixa frequência, ainda assim **não crie automaticamente**: informe o achado e peça confirmação para representar uma série sem ocorrências.
- Se uma query for inválida, registrar causa e não salvar widget.

Priorize **um único widget piloto**, validando a gravação e leitura posterior antes de prosseguir para os seguintes. Se o piloto falhar, pare a escrita e reporte o erro.

## ETAPA C — CRIAÇÃO CONTROLADA NO DASHBOARD INFORMADO

Para cada indicador com fonte/query comprovada:

1. Consulte novamente a versão atual do `DATADOG_DASHBOARD_ID` antes de salvar.
2. Verifique eventuais mudanças desde a leitura inicial para evitar sobrescrever trabalho de outros usuários.
3. **Acrescente** exclusivamente o novo widget, mantendo todos os widgets, layout, filtros, template variables, descrições, configurações e títulos já existentes.
4. Se o método de escrita exigir enviar o objeto inteiro do dashboard, preserve a versão atual integralmente e aplique **apenas o acréscimo planejado**; não substitua o dashboard por uma versão de documentação antiga.
5. Não excluir, renomear, reordenar ou modificar widgets preexistentes. Não atualizar outro dashboard. Não criar dashboard novo.
6. Depois da alteração, faça uma consulta de leitura e confirme:
   - dashboard correto;
   - novo widget efetivamente presente;
   - configuração e query corretas;
   - consulta renderiza o resultado esperado no intervalo testado, ou qualquer problema é explicitamente descrito;
   - quantidade e identidade dos widgets anteriores preservadas.
7. Verifique idempotência: execução repetida do mesmo prompt **não deve gerar widgets duplicados**.
8. Em caso de falha de persistência, atualização concorrente ou alteração inesperada, pare. Não tente exclusão/recriação massiva. Se sua operação tiver modificado indevidamente outro item, não faça rollback destrutivo automático; reporte a diferença exata e peça autorização.

**Limite de criação:** no máximo três novos widgets nesta execução, um por indicador. Se houver mais de um gráfico imprescindível, não amplie por conta própria.

## ETAPA D — VALIDAÇÃO FUNCIONAL E RELATÓRIO

Verifique:
- semântica exata da métrica mostrada;
- diferença entre contagem de logs, propostas distintas e taxa;
- filtros, janela temporal, timezone, séries e legendas;
- valores visíveis e consistentes com a query verificada;
- ausência de duplicação de widgets;
- preservação dos componentes existentes.

Se a janela testada não mostrar eventos, registrar isso sem declarar a métrica operacionalmente comprovada.

**Não declare "indicadores executivos completos" ou "taxas calculadas"** quando só houver contagens.

## DOCUMENTAÇÃO

Esta execução tem como prioridade implementar/validar widgets, não criar outra rodada de documentos.

- Atualize **somente se necessário** o arquivo canônico preexistente do MVP com o ID de cada novo widget, status e limitações, **após comprovação de criação**.
- Não reescreva relatórios das Etapas 01–04, não crie relatório novo, não arquive/mova arquivos e não altere código ou instrumentos em UAT/produção.
- Nunca registre cookies, credenciais, cabeçalhos de autenticação, identificadores de clientes ou payloads sensíveis.

## RESPOSTA FINAL OBRIGATÓRIA

Apresente uma tabela com:
- Indicador / rótulo efetivo do widget;
- status: `CRIADO E VALIDADO`, `CRIADO — VALIDAÇÃO PARCIAL`, `JÁ EXISTIA` ou `NÃO CRIADO`;
- dashboard ID e widget ID (somente IDs de configuração não sensíveis);
- query/filtros e período efetivamente validados, sem dados sensíveis;
- resultado resumido real (há dados / zero / erro / não verificado);
- limitações e próximo passo.

Inclua também:
1. Link do dashboard atualizado, somente se a URL real estiver disponível.
2. Quantidade de widgets antes/depois e confirmação de que os demais foram preservados.
3. Confirmação explícita de que o **Índice de Sucesso não foi alterado**.
4. Quais denominadores continuam pendentes para transformar contagens em taxas.
5. Quais arquivos documentais, se algum, foram atualizados.
6. Eventuais bloqueios de autenticação, qualidade dos dados ou semântica dos logs, sem fingir sucesso.

**Não iniciar monitores/alertas, não promover `cdPropostaSeguro` de UAT para produção, não modificar instrumentação ou outras aplicações e não expandir o escopo.**
