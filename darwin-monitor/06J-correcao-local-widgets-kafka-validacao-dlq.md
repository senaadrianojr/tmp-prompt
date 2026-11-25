# PROMPT 06J — Correção Local dos Dois Widgets Kafka e Confirmação do Sinal de DLQ

> **Executor:** Gemini CLI, no workspace corporativo autorizado.
>
> **REPOSITÓRIO PÚBLICO:** este prompt contém apenas instruções metodológicas. Manter exclusivamente no **workspace e relatório privados**: nomes reais de serviços e tópicos, valores de tags, atributos, queries literais, logs, dados de clientes, URLs/IDs corporativos, screenshots, JSONs, relatórios e hashes. Não copiar essas informações para GitHub.
>
> **AUTORIZAÇÃO EXPRESSA DO RESPONSÁVEL:** preparar **localmente** correções cirúrgicas das consultas dos **DOIS widgets de consumo Kafka** investigados na etapa 06I, em **candidatos JSON separados para UAT e PROD**, e registrar o teste humano do fluxo de cancelamento e a consulta complementar de esgotamento/DLQ. Autoriza leitura de fontes, geração de **novos** candidatos privados, hashes/diffs e relatório técnico. **NÃO autoriza importar/publicar em UAT ou PROD, alterar JSONs canônicos/referências oficiais, editar código/instrumentação, configurar Datadog, criar métricas, executar deploy, obter cookies/credenciais, commitar ou realizar push.**
>
> **CONTEXTO PRIVADO A RECUPERAR PELO EXECUTOR:** o relatório técnico da etapa 06I (último relatório conhecido: número 43) identificou dois widgets Kafka sem dados em UAT e PROD, com divergência entre o recurso consultado e a telemetria real, além de um erro de identificação de serviço em UAT em um deles. O operador apresentou testes **manuais na UI de Metrics Explorer em PROD**, observando séries de consumo para os dois tópicos reais pelo atributo de destino Kafka e, para o tópico de pagamento, também pelo identificador da operação de consumo com prefixo adequado, serviço e ambiente selecionados. Uma tentativa com identificador da operação **sem o prefixo** não retornou dados. Não misturar os valores aceitos por atributos diferentes.
>
> **OUTRA EVIDÊNCIA HUMANA:** no Log Explorer de PROD, o operador pesquisou o marcador geral do fluxo de cancelamento com serviço e ambiente filtrados, janela de 1 dia e consulta aos logs Flex habilitada; encontrou **15 logs**, o que confirma telemetria do fluxo naquela janela, mas **não** confirma 15 cancelamentos distintos, sucesso final ou inexistência de DLQ. Resta confirmar separadamente o marcador excepcional de esgotamento de tentativas na janela de 7 dias. Os três widgets condicionais de cancelamento/estorno **não** serão modificados nesta etapa.
>
> **ESTADO DOS DASHBOARDS:** a promoção das duas visualizações P1 anteriores foi concluída em UAT e PROD; ambos possuem **21 widgets de dados**, conforme a reconciliação anterior. Não remover nem modificar nenhum deles além das consultas dos dois widgets Kafka explicitamente identificados neste prompt.

## 1. Objetivo e fontes obrigatórias

Fazer **uma única execução local** com:
1. Identificação precisa da causa da consulta inválida dos dois widgets Kafka e definição das correções UAT e PROD;
2. Construção de um **candidato UAT** e um **candidato PROD**, cada um alterando **somente** os dois widgets Kafka alvo, com provas de integridade;
3. Registro do tráfego de cancelamento já demonstrado e entrega da **query excepcional de DLQ** para validação humana no Datadog;
4. Relatório privado e parada antes de qualquer publicação.

Consultar **seletivamente** o relatório 43 e os relatórios precedentes estritamente pertinentes, canônico UAT sincronizado, export/snapshot PROD pós-reconciliação, código/configuração de tópicos e filtros já examinados, além dos próprios widgets. Preferir os nomes e schemas reais dos JSONs privados em vez de reconstruir consultas de memória. Não refazer discovery nem repetir as análises dos 21 widgets.

**Rastreabilidade por widget:** recuperar nome/título, ID real, índice no grupo, tipo de widget, query inteira, expressões, tags, timespan, fórmula, unidade, legendas, descrições, template variables e fonte do sinal. Os índices locais são apenas pistas e **não substituem** a identificação dos nós reais pelo JSON.

## 2. Sinais Kafka: como interpretar as verificações já realizadas

Há **dois campos diferentes**, cada um com semântica própria. Nos arquivos privados, recuperar seus nomes literais e confirmar os valores exibidos nos testes do responsável:
- atributo de **destino de mensageria** → valor literal do tópico;
- atributo de **nome do recurso/operação APM** → valor literal da operação de consumo, com o prefixo exigido pelo instrumento.

**Não** trocar cegamente `resource_name` por outro atributo e **não** usar o nome simples do tópico como valor do recurso APM. Revisar filtros de **serviço**, **ambiente** e possíveis nomes antigos, inclusive erro de digitação em UAT, separadamente para cada widget.

As evidências manuais atuais devem ser classificadas honestamente:
- **Widget Kafka de pagamento:** consulta corrigida pelo nome completo do recurso/operação, serviço e ambiente de PROD foi observada com dados; valor e atributo comprovados pelo operador nessa combinação.
- **Widget Kafka de postagem:** foi observada atividade para o tópico real pelo campo de destino da mensageria; a equivalência exata na combinação **nome de recurso/operação + serviço + ambiente** ainda **não deve ser alegada como comprovada** caso não haja resultado específico desse teste. Essa lacuna **não impede preparar um candidato local**, mas deve impedir classificar a configuração dessa query como operacionalmente validada.
- **UAT:** as observações de PROD não comprovam, por si, a emissão de séries na janela UAT. A correção local de UAT precisa manter serviço, `env`, valores de tags e demais filtros corretos e ser homologada depois na interface.

Se algum filtro/tag de recurso que se pretende usar não estiver demonstrado, produzir a melhor **proposta de query fundamentada** com estado `PENDENTE DE VALIDAÇÃO DE SÉRIE`; nunca fabricar confirmação ou trocar o nome dos tópicos por tentativa. Se um atributo alternativo estiver positivamente comprovado e for compatível com o widget, pode documentá-lo como **alternativa para aprovação humana**, mas não aplicar estratégia alternativa silenciosamente.

## 3. Candidato UAT: alterar somente os dois Kafka

A partir do **canônico UAT atual e reconciliado**, criar **NOVO arquivo candidato em diretório privado de candidatos**. Não modificar o canônico.

Em cada um dos dois widgets:
- corrigir valor da operação/tópico efetivamente emitido, atributo de consulta compatível e filtro de serviço conforme evidência documental/telemetria;
- preservar `env` de UAT e/ou variável de template que tenha sido comprovadamente válida, sem copiar `prd` por acidente;
- manter a métrica, tipo, agregação, rollup, unidade, legenda, disposição e demais propriedades existentes, salvo mudança estritamente necessária para corrigir a query e **justificada**;
- corrigir o erro conhecido de grafia do serviço de UAT no widget aplicável, recuperando a grafia real dos artefatos privados;
- não afirmar que uma query alterada e validada offline já retorna dados em UAT;
- não adicionar/remover widget nem transformar métrica APM em comprovante de negócio terminal.

**Sem alterar** notas, descrições ou status dos outros widgets. Se uma descrição do próprio Kafka for factualmente enganosa, propor redação à parte e deixar fora do diff desta correção, a menos que o responsável aprove explicitamente a ampliação.

## 4. Candidato PROD: correção igualmente cirúrgica

Partir do **snapshot/export PROD reconciliado e atual** (etapa 06H), não de clone UAT. Confirmar identidade, versão e baseline antes da geração.

Preparar novo arquivo candidato PROD que contenha exclusivamente os dois ajustes de query desses mesmos widgets:
- selecionar valores corretos de recurso/operação, serviço e ambiente PROD;
- preservar filtros e metadados de governança, avisos, títulos, descrições, outros 19 widgets, grupos, notas, layout e template variables;
- detectar se a correção já está aplicada no export atual para evitar sobrescrita, duplicação ou criação de diferença artificial;
- registrar como **candidato local pronto para revisão** apenas se o diff e a integridade estiverem limpos, distinguindo claramente qualquer widget cuja combinação real de filtros ainda precise de teste no Metrics Explorer.

Se não houver baseline PROD confiável e atual, marcar candidato `PROVISÓRIO`, nunca `PRONTO PARA PUBLICAÇÃO`.

**Importação manual pela interface oficial só poderá ocorrer em etapa posterior, após revisão e autorização do responsável.** Não assumir que executar este prompt equivale a essa autorização.

## 5. Validação do widget de DLQ: dois conceitos distintos

**A. Tráfego regular já observado:** registrar como **evidência declarada pelo operador** que a pesquisa manual do marcador geral do cancelamento em PROD retornou 15 logs em 1 dia, com serviço/ambiente filtrados e Flex Logs habilitado. Isso prova **emissão/ingestão de logs daquele fluxo**, não 15 cancelamentos únicos e não sucesso fim a fim. **Não pedir para repetir esse teste.**

**B. Evento excepcional ainda pendente:** reconstruir a **query literal exata** para o marcador de **esgotamento do número máximo de tentativas** que o relatório 43 recomendou. Incluir no **relatório privado**:
- Datadog **Log Explorer**, ambiente PROD, serviço exato, `env` correto, janela **últimos 7 dias** e **Include Flex Logs** ativado;
- filtro textual exato de esgotamento, confirmando a grafia no código/relatório, sem inventar a mensagem;
- o que o operador deve registrar: `número de hits, período, timestamps (se houver), eventual amostra sem dados sensíveis, existência de outros logs do fluxo`.

**Sem acesso autorizado aos logs via API, não executar essa consulta por automação e não atribuir um resultado a ela.** Entregar a query pronta para colar e estado `AGUARDANDO TESTE HUMANO`. Caso exista resultado dessa consulta já fornecido pelo operador nos arquivos do workspace, reutilizá-lo com referência verificável, sem pedir novamente.

**Interpretação obrigatória:** zero resultados no marcador excepcional significam apenas **nenhuma ocorrência observável dessa mensagem na fonte/janela/filtros utilizados**; NÃO demonstram automaticamente saúde plena, ausência universal de descarte, inexistência de falhas nem conclusão bem-sucedida de cancelamentos. Se houver hits, não tratar NACK/DLQ como falha resolvida de cancelamento.

**O widget de DLQ NÃO terá a query alterada**, independentemente do resultado, nesta etapa; eventual correção ou contextualização será uma decisão separada.

## 6. Git local, diffs e guardrails

O workspace utiliza **Git exclusivamente local**. Antes e depois, inspecionar `git status --short`, hashes e diffs pertinentes; **não executar** add/commit/push/reset/restore/checkout/stash. Não modificar o canônico UAT nem o snapshot oficial de PROD, nem candidatos anteriores; não tocar em aplicações Java.

Validar em Python (ou validador local offline comprovadamente sem publicação):
1. parse/schema dos dois JSONs;
2. inventário recursivo: **21 widgets de dados antes/depois por ambiente**, salvo divergência histórica demonstrada; nada de `+2` nesta correção;
3. diff semântico base → candidato com **exatamente dois widgets Kafka modificados**, limitados às queries/filtros explicitamente justificados; os outros **19 widgets idênticos** em cada ambiente;
4. nenhum campo UAT carregado indevidamente para PROD e vice-versa;
5. os filtros de serviço/ambiente e valores de recurso estão realmente suportados por evidência;
6. queries de origem, campos Kafka e agregação não distorcem a semântica de `hits de consumo APM` versus `quantidade de faturas/baixas concluídas`;
7. relatório de divergências materiais e pendências de validação humana para séries não demonstradas;
8. hashes SHA-256 das fontes, candidatos e resultados de diff, mantendo arquivos originais intocados;
9. resultado de dry-run claramente offline, sem fingir `GET`, `PUT` ou publicação;
10. nenhuma alteração no widget de DLQ ou nos outros widgets.

Se algum validador tentar acessar recursos remotos ou exigir cookies, não executá-lo nessa modalidade. Não contornar controles administrativos.

## 7. Relatório privado e parada

Gerar **um relatório Markdown privado** no próximo número livre da pasta de planejamento (sugestão `44-correcao-candidatos-kafka-e-validacao-dlq.md`, sem sobrescrever arquivo), contendo:

1. resumo executivo, contexto das evidências humanas e decisões mantidas;
2. tabela dos dois widgets Kafka por ambiente: query original → consulta candidata (texto exato somente no relatório privado), erro identificado, evidência, ressalvas;
3. matriz de diferença entre nome do tópico e nome do recurso APM, incluindo a semântica dos dois atributos;
4. candidatos UAT/PROD, caminhos exclusivos e SHA-256;
5. resultado dos validadores e diff de exatamente dois widgets por ambiente, 21 preservados;
6. distinção de `QUERY PROPOSTA`, `QUERY VALIDADA NO METRICS EXPLORER PROD`, `VALIDAÇÃO UAT PENDENTE` e `DASHBOARD NÃO PUBLICADO`;
7. confirmação documental de tráfego de cancelamento em PROD e consulta exata do marcador de esgotamento/DLQ para **7 dias**;
8. interpretação segura do resultado quando o operador executar, sem transformar ausência de log em `saúde plena`;
9. sequência mínima para futura importação e validação, **não autorizadas nesta execução**;
10. garantias de integridade dos 19 widgets não alvo, canônicos, snapshots, código e Git local;
11. pendências reais de prova: **W4.4 por recurso completo/serviço/ambiente**, se não comprovado; eventual atividade em UAT; resultado do teste excepcional do DLQ.

**Estado final** por item: `CANDIDATO LOCAL VALIDADO E PRONTO PARA REVISÃO`, `CANDIDATO PROVISÓRIO — TESTE HUMANO PENDENTE` ou `BLOQUEADO — FONTE/SCHEMA INCOMPATÍVEL`. Não confundir candidato preparado com dashboard consertado.

No terminal, resumir duas correções candidatas, inventário/validação e **o teste manual único ainda necessário para DLQ**, indicando a query no relatório privado; se W4.4 não foi validado por recurso completo + serviço + ambiente, apontar também essa única verificação pendente. Não pedir repetição dos testes que o responsável já concluiu.

**PARADA OBRIGATÓRIA:** entregar arquivos locais e relatório. **NÃO importar nem publicar** os dashboards, não editar os widgets remotamente, não alterar a query da DLQ, não executar commits/push. Aguardar o responsável validar as evidências e autorizar qualquer publicação subsequente.