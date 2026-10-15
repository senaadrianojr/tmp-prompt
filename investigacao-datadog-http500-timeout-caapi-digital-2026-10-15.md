# Investigação de HTTP 500 / Timeout — Datadog

Investigue a causa raiz dos erros HTTP 500 com possível timeout na aplicação `caapi-digital`, utilizando os dados de APM do Datadog.

## Autenticação

Utilize o mecanismo existente de autenticação por cookies, cujas credenciais estão armazenadas em:

`Tools/cookie-sync/gemini-auth-sync/credentials/`

Identifique o mecanismo disponível para consumir essas credenciais. Não exponha, imprima ou registre cookies, tokens ou outros dados sensíveis. Não altere credenciais.

## Filtros da investigação

- Datadog: `bancobv-prod.datadoghq.com`
- Serviço: `caapi-digital`
- Resource: `GET /v1/banco-digital/seguros/vitrine`
- Operation: `servlet.request`
- HTTP Status: `500`
- Span Status: `error`
- Data: `15/10/2026`
- Horário inicial: `16:21–16:22 (UTC-3)`
- Duração observada: aproximadamente `15 segundos`

## Objetivos

1. Consultar os traces e spans relacionados às falhas.
2. Identificar a origem do erro HTTP 500 e verificar se existe timeout, identificando sua configuração ou origem quando possível.
3. Analisar o waterfall das requisições para localizar o componente responsável pela maior latência.
4. Identificar dependências externas, serviços downstream, chamadas HTTP ou operações que possam estar causando o problema.
5. Verificar exceções, mensagens de erro e logs correlacionados, quando disponíveis.
6. Identificar padrões de recorrência e determinar se a falha ocorre em outras janelas de tempo.
7. Consultar o código-fonte local somente quando necessário para confirmar hipóteses.

## Resultado esperado

Produza um diagnóstico técnico contendo:

- Causa raiz identificada ou hipóteses fundamentadas.
- Serviço, método ou dependência responsável, quando identificável.
- Evidências obtidas no Datadog (trace IDs, spans, duração e erros).
- Recomendações de correção, priorizadas por impacto.
- Lacunas de observabilidade que impeçam conclusões definitivas.

## Restrições

- Investigação exclusivamente de leitura.
- Não modificar código, infraestrutura, monitores ou configurações.
- Não executar requisições de teste contra produção.
- Não divulgar credenciais ou dados sensíveis.
- Não presumir causa raiz sem evidências.
- Caso a autenticação falhe, informe o problema sem tentar contornar controles de acesso.

Inicie pela análise dos traces com duração próxima de 15 segundos.
