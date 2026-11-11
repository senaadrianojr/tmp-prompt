# PROMPT 05L — Consolidação Final, Sincronização Canônica e Encerramento Técnico do Darwin Monitor em UAT

> **Executor:** Gemini CLI, exclusivamente no workspace corporativo autorizado.
>
> **Repositório público:** este prompt contém apenas instruções metodológicas sanitizadas. Não enviar ao repositório público código, planilhas, relatórios, consultas, identificadores de dashboard/widgets, endpoints, nomes internos de serviços, registros, métricas, capturas, hashes de dados corporativos, payloads, credenciais ou qualquer conteúdo empresarial.
>
> **Diretriz recebida do responsável:** consolidar em **um único ciclo** as três atividades finais: **(1)** conferir e, se necessário, sincronizar **somente o arquivo canônico local** com o dashboard UAT efetivamente validado após a última correção das descrições; **(2)** consolidar a aderência dos sete indicadores selecionados; **(3)** produzir o relatório formal de entrega em UAT.
>
> **Autorização e limites:** esta etapa permite leitura autorizada do dashboard UAT e, condicionada aos controles abaixo, a atualização **local** do arquivo canônico. **Nenhuma escrita remota Datadog, nenhuma publicação em produção, nenhuma alteração de widget, query, métrica, aplicação, testes, scripts existentes, planilhas, repositórios Git ou pipelines.** Não iniciar deploy e não solicitar ao usuário outra rodada de validação de logs já concluída.

## 1. Missão: fechar, não reiniciar o projeto

Esta é uma etapa de **encerramento técnico-documental**, não nova rodada de auditoria, investigação, construção ou redesign. Reutilizar os relatórios técnicos privados e as decisões humanas das etapas anteriores. Fazer verificações **pontuais de coerência e integridade**, não uma varredura indiscriminada de fontes e código.

O dashboard UAT já recebeu:
- publicação e aceite visual de correção de sinal operacional de um fluxo específico;
- padronização editorial dos nomes de grupos e widgets;
- correção posterior de campos de descrição que o operador constatou estarem vazios e que **ele informa já ter publicado e validado em UAT**.

**Ponto importante:** a publicação das descrições ocorreu **depois** de uma sincronização canônica anterior. Logo, **não presumir que o JSON canônico local está atualizado**. Conferir o estado remoto real antes de declarar encerramento.

Não reinterpretar o aceite humano de UAT como validação automática da cobertura integral do requisito original de negócio.

## 2. Atividade 1 — Conferência e sincronização condicional do canônico local

### 2.1. Identificar as fontes efetivas

1. Localizar no workspace privado o canônico atual, o relatório da última sincronização, o relatório e candidato da correção de descrições, e eventual relatório/backup de sua publicação posterior. Usar os paths **reais encontrados**, não inventar nome de arquivo se a execução direta da correção ocorreu fora de uma etapa formal.
2. Obter por **GET autorizado, somente leitura**, o dashboard UAT atualmente ativo, conferindo organização, ambiente, ID e metadados (quando disponíveis). Salvar snapshot novo, datado e com SHA256. Não acessar produção.
3. Verificar que o GET contém as melhorias visuais informadas pelo operador e a configuração do widget operacional já validado; examinar **recursivamente os grupos e widgets aninhados**, não só os escalares.
4. Produzir inventário de **descrições** por grupo e tipo: total de widgets de dados, descrições presentes/vazias, campos suportados e eventuais exceções. Não considerar notas de cabeçalho como widgets obrigatórios. Se a UI ou schema comporta descrição, não aceitar ausência apenas porque o widget é uma série temporal, lista ou outro tipo não escalar.
5. Confrontar o estado atual com a **última versão efetivamente aprovada e publicada** e com a declaração do operador. Se houver alterações remotas inesperadas, widgets removidos/adicionados, nomes divergentes ou descrições faltantes, **NÃO sobrescrever o canônico**. Registrar **NO-GO para sincronização** com a diferença exata e continuar, se possível, apenas a parte documental com a ressalva; não realizar correções remotas.

### 2.2. Backup e decisão de sincronização

1. Calcular hash do **canônico atual** e salvar backup local **imutável** antes de qualquer substituição. Não sobrescrever backups históricos.
2. Comparar semanticamente **canônico local × remoto UAT atual**, abrangendo propriedades de dashboard, títulos/descrições, grupos, ordenação, layouts, IDs, queries, fórmulas, agregações, fontes, template variables, precisão, intervalos, filtros, demais parâmetros e notas. Separar diferenças de representação/API de diferenças funcionais.
3. **Se o canônico já for semanticamente equivalente:** não sobrescrever; registrar **CANÔNICO JÁ SINCRONIZADO**.
4. **Se divergir apenas pelas correções de descrições comprovadamente publicadas ou por diferenças remotas legítimas já aprovadas e documentadas:** preparar cópia local fiel do GET, efetuar checagem estrutural/schema, executar diff e **substituição atômica do arquivo canônico local**. Depois reler e comprovar equivalência semântica integral com o GET salvo. Registrar hashes e antes/depois.
5. **Se houver diferença não explicada, falha no GET, ausência de evidência de aceitação da última publicação ou incompatibilidade estrutural:** manter canônico intacto; criar candidato separado e registrar bloqueio específico. **Não forçar sincronização para poder declarar conclusão.**
6. Não chamar script de atualização que possa realizar PUT remoto; confirmar que qualquer validador é **offline/read-only**. Não editar scripts existentes. Não realizar commit/push.

**A atualização autorizada nesta atividade é só no arquivo canônico local, quando todas as condições estiverem comprovadas.** O dashboard remoto não deve ser modificado.

## 3. Atividade 2 — Matriz definitiva de aderência dos sete indicadores

Fonte de escopo: a planilha-mestre privada de monitoramento e a matriz de aderência já produzida nos relatórios anteriores. **Usar somente sete linhas** da planilha:
- **Verdes:** 2, 11, 13, 14 e 19;
- **Cinzas:** 17 e 18;
- **Excluídas:** 4, 8, 9, 10, 12 e 15. **Não investigá-las nem criar widgets para elas.**

Produzir **uma única matriz principal de sete linhas** com:
`Linha | Indicador/requisito original | Objetivo de negócio | Cobertura operacional no perímetro da equipe | Aderência ao objetivo de negócio | Widgets/combinações responsáveis | Evidência UAT e aceite | Limitações / dependências | Decisão de encerramento`.

**Separar duas dimensões que não são sinônimas:**
- **Cobertura operacional da equipe:** mede etapas que as aplicações de sua responsabilidade de fato executam e conseguem observar. Se a transferência de responsabilidade para outro componente foi **comprovada**, a etapa local pode ser considerada executada com sucesso; não exigir observabilidade de todos os sistemas externos para reconhecê-lo.
- **Aderência ao objetivo original de negócio:** a fórmula, universo, conciliação ou desfecho que a planilha efetivamente exige. Uma soma de widgets úteis não se torna automaticamente um cálculo integral da fórmula de negócio.

**Regras específicas já decididas (não transformar em nova investigação):**
1. **Índice de Sucesso:** conjunto de widgets oferece cobertura operacional relevante/consolidada; não afirmar um índice percentual universal sobre bases incompatíveis. Distinguir sucesso local de sucesso de negócio.
2. **Taxa de Falha:** sinais de falhas disponíveis, mas não inventar percentual sobre total de adesões sem numerador/denominador compatíveis.
3. **Falha no Cancelamento:** esgotamento de tentativas e fluxos separados, validados em UAT; não confundir cancelamento com estorno ou alegar prova de cobrança indevida posterior apenas por log de falha.
4. **Reprocessamento:** volumetria de DLQ não equivale por si só a **taxa de reprocessamento** ou sucesso na reentrega.
5. **Origem:** registrar o recorte operacional comprovado pelos eventos/atributos existentes em UAT. Não afirmar valores/origens que não aparecem como observados, nem cobertura de todos os fluxos quando não comprovada.
6. **Conferência de identificadores:** emissão/consumo de mensagens não comprova necessariamente **divergência ou conciliação** entre identificadores; manter ressalva já documentada sem abrir auditoria ampla.
7. **Diferenças temporais:** sem fonte ou instrumentação adequada, documentar dependência externa e encerrar como não coberto pelo monitoramento atual; não fabricar widget, datas ou cálculos.

**Classificar por evidência, não por conveniência.** Permitir, por dimensão, `INTEGRAL`, `PARCIAL`, `NÃO COBERTO`, `DEPENDÊNCIA EXTERNA` ou `NÃO COMPROVADO`, com justificativa. Se um relatório anterior disser "integral" e a evidência mostrar apenas um recorte, **qualificar claramente** qual dimensão/recorte é integral, sem apagar a declaração histórica nem superestimar o negócio.

Registrar o alcance até a fronteira de responsabilidade das **três aplicações** do ecossistema monitorado, sem ampliar para aplicações de terceiros ou outros times. Nenhum novo widget, filtro ou monitoramento externo é necessário para formalizar uma limitação bem fundamentada.

## 4. Atividade 3 — Relatório formal de encerramento UAT

Criar **um novo documento Markdown privado** em `darwin-monitor/planos/`, com prefixo `33-`, por exemplo:
`33-encerramento-tecnico-uat-darwin-monitor.md`.

Evitar relatório enciclopédico. O documento deve ser compreensível para quem não acompanhou a execução, com referências por caminho aos relatórios históricos em vez de reproduzir todo o conteúdo.

**Estrutura obrigatória:**
1. **Resumo executivo:** o que foi entregue e aprovado em UAT, período, escopo de aplicações, status real de encerramento e limitações.
2. **Inventário do dashboard:** origem do último GET, identificação privada, data, quantidade real de grupos/widgets, melhorias editoriais e estado das descrições.
3. **Sincronização canônica:** versão anterior, snapshot atual, hashes, diff, backup, resultado da atualização local (ou impedimento) e validação final de equivalência.
4. **Matriz dos sete indicadores** conforme seção 3, com classificação independente de cobertura operacional e objetivo de negócio.
5. **Rastreabilidade de aceite:** validar somente o que o operador declarou como validado; distinguir validação visual humana, GET pós-publicação, dry-run, instrumentação já disponível e itens não verificados. Não inventar screenshots, contagens, datas de deploy, sucesso de terceiros ou evidências.
6. **Exclusões e dependências:** linhas fora do escopo, limites de fontes externas e instrumentação futura sem transformar tudo em bloqueio da entrega UAT.
7. **Sustentação/continuidade:** quais situações justificariam revisar widgets (mudanças no contrato, nova origem, nova instrumentação, ampliação formal do perímetro, ausência de sinal), quem deverá validar e qual gate exige autorização.
8. **Produção — etapa separada:** listar apenas os critérios para avaliação futura de elegibilidade dos widgets conforme dados efetivamente disponíveis em produção; **não pesquisar nem publicar em produção nesta etapa**. Distinguir telemetria já disponível de instrumentações ainda não comprovadas nesse ambiente.
9. **Segurança e integridade:** lista de arquivos realmente alterados/criados, confirmação de ausência de PUT remoto, alterações de código ou Git; localizar os backups.
10. **Termo de fechamento recomendado:** `PRONTO PARA ACEITE FINAL DE UAT`, `ENCERRADO NO ESCOPO UAT` **somente se o operador já tiver emitido aceitação final inequívoca**, ou `ENCERRAMENTO CONDICIONADO` com o motivo concreto. A conclusão técnica do Gemini não substitui a decisão final do responsável.

**Critério de pronto:** dashboard UAT permanece funcional e sem alterações nesta etapa; documentação reflete o que está realmente publicado; canônico equivalente ao remoto atual ou pendência transparente; os sete indicadores recebem decisões finitas e fundamentadas; nenhum requisito de negócio parcial é declarado integral por extrapolação.

## 5. Verificações finais e parada

Antes de concluir:
- Validar a sintaxe do JSON (se atualizado), a contagem e identidade real dos widgets, inclusive aninhados.
- Confirmar que **todas as descrições esperadas** estão presentes no GET, indicando qualquer exceção objetiva, não inferida.
- Testar que o canônico local é semanticamente equivalente ao snapshot UAT atual quando declarar sincronização concluída.
- Conferir que nenhuma query, fórmula, métrica, layout, título ou descrição foi **alterada remotamente por esta execução**.
- Garantir que nenhuma linha excluída entrou no escopo e que não houve nova auditoria geral.
- Não fazer commits, pushes, PRs, mudanças de código, alterações de scripts, PUTs, deploys ou rollback.
- Se alguma verificação falhar, reportar **NO-GO parcial** com causa concreta; não mascarar pendência para declarar sucesso.

**Resumo final no terminal:** (a) status real de canônico, (b) quantidade de grupos/widgets e descrições, (c) situação dos sete indicadores por dimensão, (d) pendências impeditivas e não impeditivas, (e) caminho do Relatório 33, snapshot e backup, (f) declaração de zero escrita remota, (g) recomendação para aceite humano final.

**PARE após entregar o relatório.** Não executar promoção para produção, não iniciar novos ciclos de widgets e não continuar automaticamente para outras etapas.
