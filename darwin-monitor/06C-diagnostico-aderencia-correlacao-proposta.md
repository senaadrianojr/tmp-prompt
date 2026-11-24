# PROMPT 06C — Diagnóstico de Aderência da Instrumentação de Correlação por Proposta

> **Executor:** Gemini CLI no workspace corporativo autorizado.
>
> **Repositório público de prompts:** este arquivo contém somente instruções metodológicas sanitizadas. Não publicar neste repositório código, nomes de serviços ou campos internos, queries literais, logs, planilhas, dados de clientes, IDs de dashboards/widgets, JSONs, relatórios, métricas, URLs privadas ou capturas. Todos os resultados e artefatos permanecem exclusivamente no workspace corporativo.
>
> **Autorização:** investigação pontual em arquivos e fontes locais autorizadas, leitura remota quando legitimamente disponível e produção de UM relatório privado de diagnóstico e planejamento. **NÃO alterar widgets, queries, dashboards UAT/PROD, JSON canônico, aplicações, instrumentações, testes, scripts, pipelines ou Git. NÃO publicar nem realizar deploy.**
>
> **Contexto:** o dashboard já foi elaborado para sete linhas de indicadores e possui, conforme último inventário, 19 widgets de dados. Foi criada e implantada uma **instrumentação de identificador estruturado de proposta** em dois serviços de eventos. O responsável observou que apenas um widget de stream/rastreamento parece utilizá-la diretamente, exibindo o identificador como coluna de logs. Isso não demonstra, por si só, que a instrumentação foi aproveitada nos indicadores que motivaram sua criação. O objetivo é reconstruir a necessidade original, verificar essa hipótese e planejar correções **sem implementá-las**.

## 1. Pergunta central e postura investigativa

**Por que a equipe precisou instrumentar a chave estruturada de proposta e onde ela deveria ser usada nos indicadores, se o dashboard entregue aparentemente só a utiliza em uma coluna de um stream de logs?**

Não assumir que a instrumentação foi inútil ou subutilizada antes de conferir os artefatos. Tampouco afirmar que a simples exibição em coluna cumpre os benefícios planejados de correlação, agrupamento, deduplicação e contabilização de propostas distintas.

Diferenciar:
1. **Rastreabilidade individual:** procurar uma proposta em logs;
2. **Correlação:** vincular eventos, tentativas, etapas e resultados à mesma proposta;
3. **Uso analítico em indicador:** agregar, agrupar, contar propostas distintas e construir fórmulas com população correta;
4. **Limitações:** o identificador comum, isoladamente, não cria eventos de conclusão nem assegura numeradores e denominadores corretos.

Documentar exatamente quais finalidades foram **originalmente aprovadas**, quais são oportunidades novas e quais não são tecnicamente viáveis com os dados existentes.

## 2. Recuperação seletiva das decisões originais

Localizar e ler os artefatos privados pertinentes, sem reexecutar o discovery do zero:
- Relatórios iniciais de investigação e desenho dos indicadores;
- Auditoria que motivou a instrumentação e registros de viabilidade;
- Planejamento, decisões, justificativas e critérios de aceite da alteração de código;
- Relatórios de implementação/testes da correlação por proposta nos serviços de eventos;
- Relatórios de construção, reconciliação e fechamento do dashboard UAT;
- Relatórios e artefatos mais recentes da publicação do dashboard PROD;
- Planilha mestre, somente os sete registros selecionados;
- JSONs atuais de UAT e PROD, se disponíveis como export local confiável;
- Histórico de Git, apenas se indispensável para explicar uma decisão ou mudança de escopo.

Identificar a grafia técnica real do campo estruturado **somente nos arquivos corporativos privados**. Não inferir o path de facet sem confirmar. Registre no relatório privado as fontes, trechos/seções, data, condição original e eventual decisão posterior que tenha alterado o plano.

Não confundir ausência de evidência documental com prova de que uma decisão nunca existiu.

## 3. Reconstruir o desenho ideal planejado

Para cada um dos sete indicadores, identificar:
- requisito executivo original e unidade de contabilização desejada;
- objetivo operacional dentro da responsabilidade das aplicações da equipe;
- como o dashboard deveria responder à pergunta do indicador;
- quais dependências de eventos, conclusão, falha, retry e deduplicação foram previstas;
- se a chave de proposta foi prevista como **obrigatória**, **recomendada**, **apenas auxiliar**, ou **não pertinente**;
- que parte desse desenho foi deliberadamente classificada como parcial/inviável, e por qual motivo.

Não reclassificar um requisito como totalmente atendível só porque o campo passou a existir. Separar sucesso de uma etapa local comprovada de desfecho de negócio externo; a ausência de monitoramento fora do ecossistema não invalida uma transferência local concluída.

## 4. Inventário dos widgets publicados: conferir, não presumir

Inventariar **todos os widgets de dados do dashboard consolidado** de maneira recursiva, inclusive dentro dos grupos (usar a contagem real, sem fixar 19 caso a versão disponível tenha mudado).

Para cada widget, produzir a matriz:
`Indicador | Widget/ID | Objetivo aprovado | Fonte/Query atual | Unidade efetivamente contada | Uso do identificador estruturado | Dependência originalmente prevista | Benefício possível | Lacuna | Evidência`.

Inspecionar **cada ocorrência efetiva** do campo, não apenas texto em descrições:
- `search.query`, filtros e template variables;
- group by/facet e agregações;
- formulas e computações;
- colunas de log stream;
- potencial correlação em diferentes widgets e fluxos.

Classificar o uso real como:
- **Analítico para o indicador**;
- **Filtro/correlação pontual**;
- **Somente exibição em stream**;
- **Não utilizado**.

Se houver widgets sem relação com o identificador, declarar **NÃO NECESSÁRIO**, com explicação. Nunca propor uso artificial do campo só para aumentar a contagem de widgets.

## 5. Indicadores prioritários

Investigar com maior atenção os quatro requisitos abaixo, sem presumir viabilidade nem implementação obrigatória:

- **Índice de Sucesso:** propostas distintas e relação com processamentos iniciados, resultados locais, falhas e retentativas; verificar compatibilidade temporal e de população.
- **Taxa de Falha:** numerador de propostas com falha e denominador adequado; separar falha transitória de desfecho final e evitar percentuais enganosos.
- **Falha no Cancelamento:** propostas/cancelamentos únicos, tentativas e esgotamentos, distinguindo cancelamento de estorno e resultado funcional de tratamento técnico de erro.
- **Reprocessamento:** associação entre várias tentativas e a mesma proposta, sucesso local quando observável e distinção entre volumetria de DLQ e efetiva taxa de reprocessamento.

Nos demais indicadores, verificar a pertinência real da correlação por proposta sem forçar mudança de escopo, de origem/processadora nem das limitações de parcelas e datas.

## 6. Identificar por que o plano e a implementação divergiram

Investigar a cronologia e classificar, com prova documental quando possível:
- widgets criados antes do deploy da instrumentação, sem revisão posterior;
- instrumentação tratada somente como visualização de logs;
- decisão explícita de manter cobertura operacional parcial;
- insuficiência dos eventos de início/conclusão apesar do novo campo;
- limitação real do Datadog ou da cardinalidade;
- dependência de outra fonte e impossibilidade de reconciliação;
- erro ou omissão de implementação, de revisão ou de critérios de aceite;
- outra causa fundamentada.

Para cada hipótese, emitir **CONFIRMADA**, **REFUTADA** ou **NÃO DETERMINADA**, citando evidências. Não atribuir intenções a pessoas ou ferramentas sem prova. Não usar o resultado do dashboard como única evidência da decisão original.

## 7. Propostas concretas e plano de aplicação futura

Para cada oportunidade fundamentada, entregar:
- indicador/widget afetado;
- decisão original que a sustenta (ou identificar como evolução nova);
- comportamento atual versus comportamento desejado;
- função precisa do identificador estruturado;
- eventos, estados, atributos e relações disponíveis ou faltantes;
- alteração candidata de query/agrupamento/descrição/fórmula ou necessidade justificada de outro widget;
- risco de duplicação, cardinalidade, ambiguidade de desfechos e janelas de tempo;
- requisitos mínimos para provar viabilidade em UAT;
- priorização, complexidade relativa, dependências e critérios de aceite;
- plano seguro posterior para UAT, revisão humana e só então PROD.

Prioridades:
- **P0:** lacuna comprovada frente a requisito ou decisão já aprovada;
- **P1:** melhoria valiosa e verificável usando sinais já disponíveis;
- **P2:** evolução opcional ou dependente de nova instrumentação/fonte.

Não criar widgets adicionais sem necessidade. Não sugerir `count distinct` como solução garantida sem conferir suporte real da fonte, deduplicação e consistência dos eventos. Se não há prova dos sinais finais, não fabricar taxa; propor primeiro a validação necessária.

## 8. Entregável privado

Criar **um único relatório Markdown** em `darwin-monitor/planos/`, com próximo prefixo livre. Nome sugerido `36-diagnostico-aderencia-instrumentacao-correlacao-proposta.md` (se já existir, usar um caminho novo e registrar).

Estrutura mínima:
1. Resumo executivo;
2. Motivo original documentado da instrumentação;
3. Cronologia e decisões com referências;
4. Desenho previsto dos sete indicadores;
5. Inventário completo de widgets e uso real do campo;
6. Matriz planejado × implementado × lacunas;
7. Causas confirmadas, descartadas e inconclusivas;
8. Benefícios já entregues e benefícios previstos ainda não realizados;
9. Oportunidades tecnicamente fundamentadas por indicador;
10. Plano de correção P0/P1/P2 com dependências, custos relativos e critérios de aceite;
11. Pendências e evidências indisponíveis;
12. Decisão explícita: a instrumentação cumpriu **integralmente / parcialmente / não cumpriu / evidência insuficiente** a finalidade original, com justificativa.

Citar caminhos/trechos dos documentos privados e distinguir fatos observados, declarações anteriores do executor e inferências. Se o JSON PROD não estiver disponível ou confiável, registrar limitação e não afirmar que só um widget usa o campo sem comprovar.

## 9. Limites e parada obrigatória

**Exclusivamente diagnóstico e planejamento.** Não editar dashboard ou código, não alterar instrumentações, não publicar, não sincronizar canônico, não executar deploy, não refazer auditorias inteiras e não contornar limitações de autenticação/cookies. Não pedir ao operador para repetir UAT já validada; listar apenas futuras provas estritamente necessárias.

Entregar no terminal: (a) conclusão fundamentada sobre necessidade original, (b) número real de widgets que usam a instrumentação e como, (c) lacunas P0/P1/P2, (d) causas efetivamente demonstradas, (e) caminho do relatório, (f) confirmação de zero alterações remotas/locais operacionais.

**PARE após o relatório. Aguarde aprovação humana do plano antes de qualquer implementação.**
