# Gemini — Auditoria de um indicador de recorrência: código, Darwin, Datadog, dashboards e monitores

**INDICADOR_ALVO:** `<NOME_DO_INDICADOR>`  
**Exemplo para primeira execução:** `Índice de Sucesso`

## Objetivo e limites

Investigue **somente um indicador por execução**. Avalie o que a aplicação realmente implementa, o que a planilha solicita, o que já foi documentado no Darwin e o que o Datadog realmente permite medir hoje. Produza definições para widgets de dashboard e possíveis monitores/alertas, sempre com evidências. Não construa nem modifique recursos no Datadog. Não altere código de aplicações.

## Hierarquia das fontes

1. **Código da aplicação na branch de produção `master`**: fonte de verdade do comportamento implementado. Registre repositório, commit, classe, método e linhas. Se a branch atual for outra, faça inspeção somente leitura; não altere o checkout.
2. **Planilha original de indicadores**: fonte da definição de negócio desejada (objetivo, fórmula, numerador, denominador, unidade e observações). Não adapte a fórmula à disponibilidade técnica.
3. **Datadog real**: fonte de verdade da telemetria efetivamente disponível e consultável no ambiente/período examinado.
4. **Documentação `darwin-monitor/` e documentação das aplicações**: materiais auxiliares sujeitos a erro. Toda conclusão relevante precisa ser confrontada com a implementação e, quando aplicável, com o Datadog.

Localize planilha, código e documentos no workspace; não suponha nomes e caminhos além das referências explicitamente conhecidas.

**Células amarelas:** indicam avaliações prévias do time sobre inviabilidade, gaps, falta de fonte adequada ou dependência de BI. Verifique o XLSX e eventuais legendas/comentários para preservar a classificação real. Não descarte esses indicadores: determine até onde o código permite investigar, o que já é observável e exatamente por que a métrica completa pode continuar inviável. Se não conseguir ler a cor/estilo da planilha, registre essa limitação.

## Etapa 0 — Datadog real, por acesso já autorizado

Leia o `GEMINI_PLATFORM_ACCESS_RUNBOOK.md` e a documentação operacional do projeto `gemini-auth-sync` / `bv-cookie-sync` já existente no ambiente. Utilize **o procedimento de acesso aprovado e disponível**, inclusive a sessão sincronizada quando o runbook a suportar, para executar apenas consultas de leitura no Datadog.

Antes de alegar incapacidade, verifique as ferramentas e permissões realmente disponíveis e teste o procedimento documentado, respeitando as políticas do ambiente. Não presuma que ferramentas sem navegador sejam incapazes de executar consultas, nem que cookies de navegador substituam credenciais da API oficial: determine a modalidade que o runbook de fato suporta.

Não exponha, registre, transfira ou inclua em arquivos de resultado quaisquer tokens, cookies, chaves, cabeçalhos de autenticação ou payloads sensíveis. Não invente nem contorne mecanismos de acesso.

Classifique o acesso como:
- `DATADOG_REAL_ACCESS=CONFIRMADO` — leitura autenticada real comprovada;
- `DATADOG_REAL_ACCESS=PARCIAL` — leitura possível, porém fontes específicas indisponíveis;
- `DATADOG_REAL_ACCESS=BLOQUEADO` — impedimento real e diagnosticado.

Em caso de bloqueio, prossiga somente com análise estática **sem declarar widgets ou monitores operacionalmente validados**. Registre etapa, erro sanitizado e consequência.

## Etapa 1 — Entender um indicador

Localize `INDICADOR_ALVO` na planilha e registre aba/célula, nome, objetivo, fórmula, unidade, área responsável, frequência e anotações. Compare com `darwin-monitor/indicadores/`, definições em `darwin-monitor/dashboards/` e a documentação das aplicações.

Defina o evento de negócio, unidade de contagem, numerador, denominador, filtros, janela, regras de exclusão, vínculo entre sistemas e critério de unicidade. Distinga os fluxos de cartão, débito ou outros somente quando efetivamente relacionados. Não confunda requisição, proposta, recorrência, parcela, mensagem e pagamento.

## Etapa 2 — Auditar código, não apenas procurar strings

Em `master`, rastreie as condições de negócio e a cadeia de execução: controllers, services, rotas Camel, processors, integrações, filas, retries, DLQ, persistência e respostas HTTP.

Para cada evento candidato, determine se é:
- chamada real a logger (nível/configuração e condição);
- **conteúdo de resposta HTTP** (incluindo, quando aplicável, `ResponseProcessor.montaResponse` e `Exchange`);
- mensagem enviada/consumida de fila;
- span/atributo de tracing;
- métrica instrumentada;
- registro persistido;
- mera string, DTO, constante ou comentário.

**Uma mensagem retornada em HTTP não é automaticamente um log.** Mesmo um `log.info` no código não prova coleta/indexação no Datadog. `HTTP 200` não prova sucesso funcional. Valide o significado do resultado, incluindo mensagens que combinam condições distintas (por exemplo, duplicidade **ou** indisponibilidade). Avalie efeitos de retry/redelivery, múltiplos spans e necessidade de correlação/deduplicação.

Registre `repositório@commit → arquivo:linha → classe/método → condição → sinal emitido → significado funcional`.

## Etapa 3 — Comprovar a observabilidade

Via Datadog real, quando autorizado e acessível, consulte fontes relevantes: APM Metrics, APM Spans/Traces, Logs e métricas customizadas existentes.

Para cada sinal registre datasource, ambiente, service, operation, resource, tags/facets, janela de teste, configuração da consulta e resultado qualitativo. Nunca invente campos, nomes ou queries. Diferencie:
- `RETORNOU DADOS REAIS`;
- `QUERY EXECUTÁVEL, ZERO OCORRÊNCIAS NA JANELA`;
- `SEM LOGS/DADOS PARA OS FILTROS TESTADOS`;
- `NÃO PESQUISÁVEL OU NÃO INDEXADO (SE COMPROVADO)`;
- `FONTE/ACESSO INDISPONÍVEL`;
- `NÃO TESTADO`.

`No Data` não comprova ausência absoluta: investigue os filtros, serviço, ambiente, período, permissões e fontes sem inferência indevida. Não confunda um request/span com um evento único de negócio.

## Etapa 4 — Quais dashboards/widgets são válidos?

Audite os widgets já definidos no Darwin para este indicador e informe se devem ser mantidos, corrigidos, renomeados ou rejeitados. Indique em qual dashboard existente o indicador se encaixa; evite duplicar dashboards.

Classifique cada widget:
- `VALIDADO_FUNCIONALMENTE` — medição fiel à planilha e consulta real comprovada;
- `MVP_TECNICO_VALIDADO` — proxy útil com telemetria real, **sem equivalência automática ao indicador**;
- `CANDIDATO_NAO_VALIDADO` — hipótese ainda sem comprovação;
- `BLOQUEADO_POR_GAP` — fonte/correlação/instrumentação/regra/BI pendente;
- `INCORRETO` — definição anterior não representa o código ou o indicador.

Para widgets validados, descreva o modo de montagem na UI do Datadog: nome, tipo visual, datasource, filtros testados, agregação, fórmula, unidade, janela e limitações. Não forneça query especulativa como se fosse pronta.

## Etapa 5 — Quais Datadog Monitors são viáveis hoje?

Trate **monitor de alerta** separadamente de **widget de dashboard**. Para cada candidato relacionado diretamente ao indicador, documente:
- evento de falha/anomalia a detectar e seu significado funcional;
- datasource e evidência concreta;
- tipo de monitor e configuração de consulta **somente se testada**;
- janela de avaliação, agregação, grouping e requisitos operacionais;
- limiar e justificativa por baseline/regra fornecida: sem evidência, registrar `THRESHOLD_PENDENTE` e não inventar números;
- riscos de falsos positivos, retries e ausência normal de tráfego;
- dependência de notificação Teams (não configurar nem enviar alertas).

Status:
- `MONITOR_PRONTO_PARA_CONFIGURAR` — fonte, consulta, janela e limiar comprovados;
- `MONITOR_POSSIVEL_COM_PENDENCIAS` — fonte confirmada, mas falta critério/limiar;
- `MONITOR_CANDIDATO_NAO_VALIDADO`;
- `MONITOR_BLOQUEADO`;
- `MONITOR_NAO_RECOMENDADO`.

Não invente monitores só para preencher a lista. Se nenhum monitor puder ser recomendado, diga explicitamente.

## Etapa 6 — Relatório Markdown por indicador

Use o padrão já existente em `darwin-monitor/` se for apropriado; caso contrário crie `darwin-monitor/analises-por-indicador/<slug-indicador>.md`. Um indicador por arquivo. Não altere outros indicadores, dashboards ou documentação da aplicação.

O relatório deve conter:

1. Resumo executivo, origem e estado do indicador na planilha, status do acesso ao Datadog;
2. Referências de planilha, código (branch/commit), docs Darwin e docs das aplicações;
3. Regra desejada × regra implementada;
4. Mapa do fluxo com classes/métodos/linhas;
5. Matriz `evento → evidência no código → tipo de sinal (log/resposta/fila/span/métrica) → evidência Datadog → possibilidade de contagem → gap`;
6. Auditoria de widgets existentes e sugestões de correção;
7. Widgets viáveis hoje vs. MVP técnico vs. bloqueados;
8. Monitores viáveis hoje vs. pendentes vs. bloqueados;
9. Dependências de instrumentação, correlação, BI e validação humana, sem implementar;
10. Próximo passo de menor esforço para evoluir o indicador.

Usar Markdown legível, compatível com Obsidian e RAG, com referências relativas e sem informações sensíveis.

## Critérios de aceitação

- Um único indicador analisado;
- Implementação real investigada, sem confiar cegamente nas documentações;
- Evidência comprovada para cada conclusão;
- Separação explícita entre mensagem HTTP e log;
- Acesso ao Datadog testado pelo procedimento aprovado, ou bloqueio concretamente identificado;
- Queries/testes reais distinguíveis de hipóteses;
- Fórmula do negócio preservada;
- Widgets e monitores avaliados independentemente;
- Nenhum número de threshold inventado;
- Nenhum código, dashboard ou monitor modificado.

Ao encerrar, reporte: `INDICADOR_ALVO`, `DATADOG_REAL_ACCESS`, número de widgets validados/MVP/bloqueados, monitores prontos/pendentes/bloqueados, principais gaps e caminho do relatório.

**Não passe ao próximo indicador sem uma nova instrução.**
