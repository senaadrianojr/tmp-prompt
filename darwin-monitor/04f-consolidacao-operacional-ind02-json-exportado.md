# Darwin Monitor — Prompt 04F: consolidação operacional do IND-02 sem publicação

**EXECUTE EM UMA JANELA NOVA DO GEMINI CLI.** Este prompt contém o contexto necessário para esta etapa. O objetivo é entregar **uma proposta de alteração do dashboard tecnicamente verificável**, não publicar nem reabrir auditorias.

## 1. Objetivo e limite de autorização

Consolidar o **IND-02 — Taxa de Falha** no Darwin Monitor:
- Identificar o widget legado de percentual de falha **não validado** e propor sua retirada/substituição com cuidado.
- **Reaproveitar** os sinais de falha de negócio e retentativas já presentes nos widgets do IND-01, **sem duplicar gráficos** nem calcular percentuais artificiais.
- Preparar um **JSON candidato separado**, com o delta mínimo, um relatório comparativo e recomendação para posterior aprovação humana.

**NÃO executar PUT/PATCH/POST/DELETE no Datadog, não rodar script de publicação, não modificar diretamente o JSON canônico, não criar commit/push/PR, não alterar Java, Python, monitores ou alertas.** O responsável fará a revisão antes de autorizar qualquer publicação.

## 2. Contexto de negócio e evidência

- Projeto Darwin Monitor, Banco BV. Organização Datadog UAT **BV-DUQ**; dashboard **`[UAT] Darwin Monitor`**, ID **`ymk-jgs-3dd`**.
- Workspace lógico: **`darwin-monitor/`**; dentro dele estão diretamente `planos/`, `dashboards/`, `uat_darwin_monitor_dashboard.json` e `update_uat_dashboard.py`. Localizar o caminho físico real. **Não criar `docs/darwin-monitor/` dentro desta pasta.**
- Fonte funcional: `Monitoramento Cartões 1.xlsx` e **`planos/15-investigacao-ind02-taxa-de-falha.md`** (resultado do Prompt 04A). A intenção do IND-02 é medir **Taxa de Falha**, e a fórmula original **adesões com erro / total de adesões × 100** prevalece sobre a redação eventualmente imprecisa do campo objetivo. A redação não anula o requisito.
- Widget legado: **`Taxa de Falha Funcional de Recorrência (Cartão)`**, Query Value de Logs com fórmula **`(a/b)*100`**. A auditoria identificou população e eventos insuficientemente reconciliados (duplicações/reentregas, possíveis falhas heterogêneas e ausência de estados terminais). O percentual exibido **não está validado como taxa de falha das adesões**, mesmo que seja matematicamente computável. **Não alterar outros widgets percentuais, especialmente SGAT e APM**, nesta tarefa.
- Existem três widgets operacionais do **IND-01**:
  1. `IND-01 — Processamentos de recorrência iniciados (Pub/Sub)`;
  2. `IND-01 — Retornos de sucesso local do orquestrador`;
  3. `IND-01 — Falhas de negócio e retentativas (Pub/Sub)`, com séries separadas **`[FALHA-NEGOCIO]`** e **`[RETRY-PUBSUB]`**, fonte Logs/Count de `sboot-pseg-base-orch-gestao-eventos-conta-cartao`, `env:uat`.
- **O terceiro widget do IND-01 já oferece a cobertura operacional parcial necessária ao IND-02.** Falha de negócio e retry devem permanecer distinguíveis; retry/NACK **não significa falha definitiva da adesão**. Não criar um segundo gráfico igual só para incluir prefixo IND-02.
- `mdc.cdPropostaSeguro` está confirmado nos logs de **Eventos Conta Cartão**, mas a correlação até todos os resultados finais ainda não foi demonstrada. Em **Eventos Pagamento**, instrumentação foi recentemente implantada em UAT e ainda **não há registros novos suficientes para validá-la**. Não bloquear esta consolidação esperando tráfego, nem declarar rastreabilidade total.
- **Taxa de sucesso opcional:** o responsável considera válido um widget complementar apenas se houver numerador/denominador consistentes, resultado terminal observado e semântica explícita. Não criar agora um `100 - taxa de falha` nem uma taxa de sucesso especulativa.
- **IND-07 Origem** possui três widgets corrigidos e revisados manualmente: eventos de contratação por processadora; volume de contratação ao longo do tempo; volume de cancelamento ao longo do tempo. Os demais grupos e widgets existentes **não são escopo de alteração**.

## 3. Nova fonte de verdade: export manual recente do Datadog

**O responsável acaba de exportar novamente o dashboard atual do Datadog e colocou o arquivo JSON na RAIZ de `darwin-monitor/`. O arquivo exportado é o que tem a data/hora mais recente entre os exports.**

**Não presuma seu nome.** Antes de editar qualquer coisa:

1. Liste os JSONs na raiz (nome, tamanho, data de modificação e SHA-256), distinguindo **exports com data/hora no nome** do canônico `uat_darwin_monitor_dashboard.json`, de backups e de candidatos.
2. Identifique o export mais recente combinando o **timestamp do nome**, quando legível, e os metadados reais do arquivo. Use a informação do responsável como pista, mas **confirme pelo conteúdo** que é o dashboard UAT `ymk-jgs-3dd` e que contém o estado atual esperado.
3. Valide sintaxe, estrutura (payload direto ou envelope exportado) e inventarie **todos os widgets, incluindo filhos de grupos**, sem confundir quantidade de grupos raiz com total de widgets.
4. Confirme que o export contém **3 widgets de Origem**, **3 novos do IND-01**, o widget legado de taxa de falha e os demais elementos. Confira também a correção manual conhecida do Stream de Recorrência: coluna **`@mdc.cdPropostaSeguro`** em lugar de `@cdPropostaSeguro`. **Se algo divergir, não corrija silenciosamente; reporte.**
5. Compare **export recente vs. JSON canônico local**, agrupando diferenças por widget e propriedades gerais. O export é a referência do estado atual conhecido; **não retornar ao canônico antigo ou a backups só por conveniência**. Se houver modificação local não publicada e potencialmente legítima, registre conflito e NÃO escolha silenciosamente um lado.
6. **Preserve integralmente o export bruto original**, sem reformatar/substituir. Se houver envelope com campos read-only, use somente o payload editável comprovadamente compatível ao preparar o candidato, mantendo semântica intacta.

**Critério de bloqueio:** se não localizar o export, identificar ambiguidade entre arquivos, descobrir dashboard incorreto, ausência inesperada de widgets relevantes ou alterações locais legítimas não conciliadas, pare antes de gerar candidato completo e peça **somente** a informação/decisão que falta. Não usar GET como substituto automático do export que o usuário já disponibilizou.

## 4. Decisão de tratamento do IND-02

A solução **preferencial** a preparar é:

- **Retirar do JSON CANDIDATO** (não do Datadog nem do JSON canônico) **somente** o widget percentual legado `Taxa de Falha Funcional de Recorrência (Cartão)`, **caso sua identidade seja inequívoca** e a auditoria do relatório 15 confirme que é o KPI inválido em questão.
- **Preservar sem qualquer alteração funcional** o widget `IND-01 — Falhas de negócio e retentativas (Pub/Sub)`, explicitando no relatório que ele também serve de **cobertura operacional parcial ao IND-02**, sem alegar atendimento integral à fórmula original.
- **Não incluir novo percentual**, não criar gráfico duplicado, não converter contagens de logs em propostas únicas, não agrupar falhas por `message` inteiro e não transformar `[RETRY-PUBSUB]` em taxa de falha definitiva.
- **Não mexer** em `Taxa de Sucesso Técnico (APM)`, `Índice de Sucesso — Débito em Conta (SGAT)`, widgets de Origem, streams, outros indicadores ou nas variáveis gerais do dashboard.
- Se remover o widget antigo deixar **layout inválido**, fazer somente ajuste estrutural estritamente necessário e explicitar no diff; não redesenhar grupos existentes. Se a exclusão não for inequivocamente segura, **não a faça**: registre o motivo e entregue recomendação sem JSON modificado.

A substituição acima é **proposta para aprovação**, não autorização para aplicar no Datadog. Caso você considere tecnicamente mais prudente descontinuar/renomear em vez de remover, documente a alternativa, mas não altere widgets adicionais sem aprovação.

## 5. Artefato de revisão e validação automática

Gere **`dashboards/ind02-candidato-uat.json`** como **cópia completa do payload editável do export recente**, com **apenas a alteração permitida acima**. O arquivo candidato será analisado antes de ser incorporado ao canônico e, mais tarde, publicado.

**Não substituir `uat_darwin_monitor_dashboard.json` nesta execução. Não rodar `update_uat_dashboard.py`.**

Validações obrigatórias:

1. JSON válido e estrutura de dashboard compatível com o formato conhecido, sem inventar campos do Datadog.
2. **Diff semântico recursivo** entre baseline (payload editável do export recente) e candidato, incluindo subwidgets, layout, queries, metadados gerais e ordem.
3. Inventário antes/depois, total de grupos/widget filhos, e **whitelist** de mudanças: exclusivamente retirada do widget legado validado e eventual ajuste mínimo de layout do grupo. **Qualquer alteração em outra definição bloqueia o candidato**.
4. Preservação exata dos widgets IND-07, dos três IND-01, de SGAT, APM, Stream e de todos os demais grupos; nenhuma nova duplicata.
5. Comparação **export × canônico local** apresentada separadamente do diff **export × candidato**. Não confundir diferenças preexistentes de sincronização com mudanças propostas para o IND-02.
6. **Não executar publicação para testar**. Se não houver validador local seguro, somente checar JSON e diff comprováveis e marcar a validação remota como pendente, sem inventar HTTP 200.

## 6. Relatório e resposta de encerramento

Crie **`darwin-monitor/planos/20-consolidacao-operacional-ind02-sem-publicacao.md`**, contendo:

- Identificação verificada do export recente (caminho, timestamp, SHA-256, dashboard) e distinção do canônico;
- Inventário do dashboard exportado e resultado da comparação export × canônico;
- Identificação inequívoca do widget legado por título/queries/grupo/posição; evidência de por que sua taxa não é validada;
- Estratégia de **cobertura operacional parcial do IND-02 reaproveitando IND-01**, preservando o requisito original da taxa de falha;
- Alteração **exata** no candidato, inventário antes/depois e diff semântico; widgets que ficaram intocados;
- Status do candidato: `PREPARADO PARA REVISÃO` ou `BLOQUEADO`, sem declarar publicação;
- Pendência funcional de taxa por adesão; taxa de sucesso opcional futura se houver dados;
- Pendência de observabilidade pós-deploy em Eventos Pagamento sem interromper a evolução do dashboard;
- Próximo passo após aprovação humana: **primeiro reconciliar o canônico com o export mais atual**, registrar commit Git e comparar o candidato com uma leitura remota atual no momento da publicação; somente então autorizar um PUT controlado. Se houver drift remoto, bloquear e reconciliar.

**Resposta final única e objetiva (máximo 12 linhas):**
1. Export escolhido, timestamp e SHA;
2. Resultado export × canônico;
3. Quantos widgets do IND-07/IND-01 verificados;
4. Widget percentual legado identificado e decisão;
5. Candidato criado ou bloqueado e seu caminho;
6. Delta semântico e preservação dos demais widgets;
7. Relatório criado;
8. `Datadog remoto: NÃO ALTERADO`;
9. `JSON canônico/Python/Java/Git: NÃO ALTERADOS`;
10. Decisão que depende do responsável, se houver.

**Não pedir autorização repetida para realizar a análise local; não finalizar com mera intenção de executar. Não iniciar IND-03, não reabrir auditorias A–E do IND-01, não entrar em looping de mensagens de conclusão.**
