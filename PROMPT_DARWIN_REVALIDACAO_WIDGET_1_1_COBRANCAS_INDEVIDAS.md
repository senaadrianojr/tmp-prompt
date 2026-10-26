# Revalidação técnica — Widget 1.1: Cobranças Indevidas de Seguros Cancelados

## Contexto

Durante a construção dos dashboards do projeto `darwin-monitor`, identificamos possíveis inconsistências na documentação do indicador **Widget 1.1 — Cobranças Indevidas de Seguros Cancelados**, pertencente ao dashboard de Saúde do Cancelamento.

A documentação foi elaborada anteriormente com base na análise do código-fonte das aplicações, considerando a branch `master`, correspondente à produção.

Entretanto, os primeiros testes no Datadog não conseguiram localizar os eventos utilizados pela consulta documentada.

Sua tarefa é **revalidar exclusivamente esse widget**, utilizando o código-fonte real, a documentação existente e as evidências abaixo.

## 1. Evidências obtidas no Datadog

- Fonte de dados utilizada: Logs.
- Visualização: Query Value.
- Agregação: Count of all logs.
- Uma pesquisa sem filtros encontrou aproximadamente 9 milhões de logs no período consultado.
- O filtro `service:sboot-pseg-base-orch-gestao-eventos-pagamento` retornou `No data`.
- A busca por `*:ROUTE_ESTORNO_EFETIVA` não retornou resultados.
- A busca por `*:estorno` retornou resultados.

Isso comprova que existem logs disponíveis no ambiente consultado, mas não confirma que os eventos necessários ao indicador estejam indexados.

Não presuma que a documentação está correta nem que a ausência dos eventos significa inexistência da funcionalidade.

## 2. Investigação obrigatória

### A. Revalidar a regra de negócio

Localize a implementação responsável pelo comportamento descrito no indicador.

Determine:

- O que caracteriza uma cobrança indevida de seguro cancelado.
- Como a aplicação identifica essa situação.
- Qual fluxo é executado quando ela ocorre.
- Se existe distinção entre estorno comum e estorno provocado por cobrança após cancelamento.
- Qual evento corresponde efetivamente à ocorrência que devemos contabilizar.
- Se a implementação permite contar uma ocorrência uma única vez, evitando duplicidades causadas por retries ou múltiplas etapas.

Não considere qualquer estorno como cobrança indevida sem comprovação no código.

### B. Rastrear a implementação

Analise a versão `master` das aplicações envolvidas.

Identifique, quando existirem:

- Classes, métodos e serviços participantes.
- Condições que disparam o processamento.
- Chamadas e integrações envolvidas.
- Pontos de emissão de logs.
- Mensagens efetivamente emitidas.
- Níveis de log (`INFO`, `WARN`, `ERROR`, `DEBUG`).
- Recursos de observabilidade existentes, incluindo métricas, spans, tags e eventos.

Para cada evidência relevante, forneça caminho do arquivo, classe, método e referência de linha.

Não altere o código-fonte e não faça checkout destrutivo ou modificações na branch.

### C. Validar a consulta original

Investigue especificamente:

`service:sboot-pseg-base-orch-gestao-eventos-pagamento`

`ROUTE_ESTORNO_EFETIVA`

`Realiza a efetivação do estorno de seguro`

Determine:

1. Se esses identificadores existem no código.
2. Se a mensagem documentada é realmente emitida.
3. Em quais condições ela é emitida.
4. Se o nível de log permite sua emissão em produção, conforme configurações presentes no repositório.
5. Se o nome do serviço documentado está fundamentado em configurações de observabilidade.
6. Se esses eventos representam precisamente o indicador pretendido.

Diferencie explicitamente o que está comprovado pelo código do que dependeria de validação no Datadog ou no ambiente de execução.

## 3. Reavaliar a estratégia de observabilidade

Determine qual fonte de dados melhor representa o indicador:

- Logs.
- APM Spans.
- Métricas existentes.

Priorize dados já instrumentados.

Caso exista telemetria adequada, proponha a consulta correta para um widget `Query Value`, contendo:

- Fonte de dados.
- Filtros exatos.
- Agregação.
- Janela temporal.
- Configuração aplicável na interface do Datadog.
- Procedimento de validação no Logs Explorer, APM Explorer ou Metrics Explorer.

Não invente nomes de serviços, operações, tags, métricas ou mensagens.

Não substitua a métrica de negócio por contagem genérica de requisições ou de estornos.

Se nenhuma fonte atual permitir medir o indicador com precisão, explique a lacuna e identifique os pontos mínimos onde a aplicação precisaria de instrumentação adicional, **sem implementar alterações**.

## 4. Revisar a documentação existente

Localize no projeto `darwin-monitor` a definição original do Widget 1.1.

Compare a documentação com a implementação real.

Apresente:

- O que foi confirmado.
- O que está incorreto.
- O que permanece incerto.
- A causa provável da falha da consulta.
- A estratégia recomendada para construção do widget.

Não modifique outros indicadores, dashboards ou documentos não relacionados.

## 5. Critérios de confiabilidade

Adote confiança mínima de **85%** para apresentar uma definição como tecnicamente validada.

Classifique o widget como:

- **VALIDADO:** implementação e telemetria comprovadas, com consulta tecnicamente fundamentada.
- **PENDENTE DE VALIDAÇÃO OPERACIONAL:** implementação encontrada, mas a existência ou configuração da telemetria no Datadog não foi comprovada.
- **PENDENTE DE INSTRUMENTAÇÃO:** a aplicação não disponibiliza dados suficientes para representar o indicador com precisão.
- **DOCUMENTAÇÃO INCORRETA:** a definição anterior não corresponde ao comportamento real da aplicação.

Não classifique como validado apenas por encontrar um texto semelhante no código.

## 6. Entregável

Crie um relatório Markdown no diretório apropriado de `darwin-monitor`, utilizando um nome descritivo relacionado ao Widget 1.1.

Organize o relatório em:

1. Resumo executivo.
2. Regra de negócio identificada.
3. Evidências técnicas com referências ao código.
4. Comparação com a documentação original.
5. Análise dos logs, traces e métricas.
6. Classificação e grau de confiança.
7. Consulta Datadog corrigida, caso tecnicamente justificável.
8. Passo a passo para validar e construir o widget.
9. Pendências e recomendações.

Utilize estrutura Markdown compatível com Obsidian e consulta por ferramentas RAG.

**Restrições finais:**

- Não implementar código.
- Não modificar a aplicação.
- Não alterar outros widgets.
- Não assumir que um log no código está indexado no Datadog.
- Não inventar telemetria.
- Não declarar que o indicador está operacional sem evidências suficientes.

Ao finalizar, apresente um resumo objetivo da investigação, a classificação obtida e o caminho do relatório gerado.
