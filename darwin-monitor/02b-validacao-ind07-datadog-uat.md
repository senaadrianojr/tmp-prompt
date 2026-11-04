# Darwin Monitor — Prompt 02B: Validar telemetria do IND-07 no Datadog UAT

## Contexto desta execução

Este prompt é **continuação pontual** da investigação **IND-07 — Origem (DXC × Pismo)** da aplicação `springboot-pseg-base-venda-digital`.

Já existe o relatório:
`docs/darwin-monitor/planos/05-investigacao-ind07-origem-venda-digital.md`

Ele apresenta evidências de código em `VendaDigitalService.java` e `LogMapper.java` e propõe um widget de **solicitações de contratação por processadora**, com cobertura parcial do requisito de contratos DXC × Pismo. Entretanto, a seção de Datadog ficou **NÃO EXECUTADA** e a query sugerida **NÃO VALIDADA**.

**Sua missão é exclusivamente corrigir essa lacuna com consultas reais ao Datadog UAT, usando a ferramenta Python já existente de acesso via API legada/cookies, e atualizar o relatório. Não refaça a investigação de código do zero, não passe para outro indicador e não crie widgets.**

## 1. Primeiro: localizar e compreender a ferramenta existente

1. No workspace atual, localize a ferramenta Python já configurada para acesso ao Datadog por **API legada + cookies da sessão autorizada**. Procure primeiro a documentação, instruções de uso, scripts e exemplos de consultas existentes do projeto Darwin Monitor (incluindo as ferramentas de sincronização de autenticação, se documentadas). Não invente caminho, nome de script, endpoint ou formato de resposta.
2. Leia o README/código relevante para entender:
   - Como a ferramenta usa a sessão **já autorizada**;
   - Quais operações de consulta realmente suporta;
   - Como distinguir consultas de **Logs**, **APM Traces** e **APM Metrics**;
   - Como informar organização, ambiente e janela temporal;
   - Como detectar falhas de autenticação, permissões, resultado vazio e erro de sintaxe.
3. Reaproveite a ferramenta existente. **Não desenvolva um novo cliente, não substitua o sistema de autenticação e não altere arquivos da ferramenta.**
4. Use apenas o acesso efetivamente autorizado pelo ambiente e pelas políticas corporativas. **Não contorne restrições**, não busque cookies em locais não previstos pelo mecanismo existente e não faça exploração indiscriminada de endpoints.
5. **Nunca imprima, copie para logs/relatórios, faça commit ou envie para serviços externos cookies, tokens, cabeçalhos de autenticação, IDs de sessão ou outras credenciais.** Não registre URLs internas sensíveis nem dados pessoais de clientes no documento final.
6. Se a ferramenta usa um método HTTP POST **apenas para pesquisar dados**, não o trate automaticamente como alteração: verifique sua semântica no código/documentação e use somente se comprovadamente for uma operação de leitura. São proibidas operações que criem/alterem/excluam dashboards, monitores, filtros, integrações, métricas, configurações ou permissões.

Se não localizar ou não conseguir utilizar a ferramenta, **pare a validação e documente o impedimento real** (sem revelar informações sensíveis). Não afirme que consultou o Datadog.

## 2. Reaproveitar as evidências do IND-07

Leia o relatório existente e consulte novamente **somente os trechos indispensáveis** do código para obter os nomes e valores **literais** de logs e MDC efetivamente emitidos.

Verifique, em particular, se há comprovação no código de:
- Qual evento é gerado por `gravarLog`;
- Como `LogMapper` preenche `processadora` e em que condições o atributo pode faltar;
- Qual valor **real** é usado em `tipo_evento` (não aceite `Contratacao` por suposição);
- Se o serviço no Datadog tem exatamente o nome presumido;
- Se a emissão representa solicitação, tentativa, roteamento ou sucesso local, e se pode ocorrer mais de uma vez para a mesma operação.

Os documentos antigos e a query `sum:trace.log_count by {@processadora}` são **hipóteses históricas, não consultas comprovadas**. Em particular, não trate uma expressão de consulta de métricas como query válida de **Logs** sem demonstrar fonte, sintaxe e retorno.

## 3. Executar uma sequência curta de consultas de leitura em UAT

Use a ferramenta identificada e siga esta ordem:

**A. Confirmação de acesso**
- Comprove que a ferramenta consegue executar uma consulta de **leitura** no Datadog UAT e identifique a fonte.
- Se houver 401/403, resposta de login HTML, timeout ou outra falha, registre o tipo de erro e interrompa tentativas especulativas.
- Não use produção para substituir UAT sem autorização.

**B. Descoberta do serviço e tráfego**
- Consulte o nome **real** do serviço na fonte escolhida, restrito a UAT e janela temporal explícita.
- Comece por um intervalo recente razoável e, se estiver vazio, confira **no máximo mais duas janelas** que a ferramenta suporte e que respeitem a retenção disponível.
- Resultado vazio não comprova que não existem logs no código.

**C. Descoberta dos campos**
- Procure registros correspondentes a eventos comprovados no código, sem inventar textos.
- Verifique se `processadora` e `tipo_evento` realmente chegam aos registros **como campos pesquisáveis/agrupáveis**, ou se aparecem apenas no texto da mensagem.
- Verifique os **valores observados** para a processadora e se há registros com campo ausente ou inesperado. Apresente apenas valores de categorias técnicas não sensíveis, nunca identificadores de contratos/clientes.

**D. Validação da consulta do widget**
- Somente depois de confirmar fonte, filtros e atributos, monte a consulta **no formato adequado ao tipo de widget e à fonte de dados**.
- Execute-a no Datadog via ferramenta; registre retorno, período, fonte, agrupamento, eventuais erros e limitações.
- Caso o campo não seja facetado/agregável no ambiente, **não declare a query validada para agrupamento**.
- Não confunda `Logs Explorer`, consultas de série temporal de logs, métricas e traces.
- Se houver logs mas não houver ocorrências DXC e Pismo na janela observada, não afirme que ambos foram testados; indique quais valores tiveram evidência.

**Limite anti-loop:** não tente infinitas variações da query. Faça uma consulta ampla sobre serviço/ambiente e até **três refinamentos justificados por dados concretos**; se o problema persistir, classifique a causa e encerre. Cada refinamento deve explicar qual observação nova motivou a mudança.

## 4. Critérios de resultado — sem falsos positivos

Distinga explicitamente quatro situações:

1. **Instrumentação no código identificada:** existe chamada de log/campo no código.
2. **Telemetria encontrada no UAT:** existem eventos observados em consulta real.
3. **Consulta tecnicamente executada:** a sintaxe/fonte/agrupamento foram aceitos, com resultado documentado.
4. **Indicador operacional validado:** além de executar, há evidência de que os eventos contados representam o fenômeno anunciado no nome do widget (com ressalvas de retentativas/duplicidade e cobertura).

Uma query que executa e retorna zero **não é automaticamente inválida**, mas tampouco comprova a existência dos eventos esperados. Uma query que retorna números pode estar semanticamente errada.

Se **não houver logs disponíveis** em UAT, investigue somente se APM Traces/Metrics oferece evidência útil **para um widget alternativo honestamente nomeado**, sem supor que `processadora` esteja disponível como dimensão de métrica. Se não houver suporte, registre a limitação.

Se a consulta não puder ser validada, proponha o próximo teste controlado necessário — **sem exigir geração de tráfego antes de tentar consultar os dados já disponíveis**.

## 5. Preservar atendimento parcial

O objetivo da planilha é `Origem do Contrato / Contratos DXC × Pismo`, mas um evento de solicitação não é necessariamente contrato efetivado.

- Se a telemetria comprovar apenas tentativas/solicitações por processadora, **mantenha o widget parcial** e nomeie-o conforme o evento efetivamente observado.
- Se houver confirmação de sucesso **local**, registre esse evento como tal; não infira sucesso final em DXC/Pismo sem resposta confiável.
- Se a informação de origem estiver ausente nos logs, especifique a **instrumentação mínima proposta no ponto de negócio adequado**, sem implementá-la.
- Se a fonte/atributos permitirem apenas monitoria de erros locais ou volume total, descreva essa alternativa sem chamar de KPI completo.
- Não descarte o indicador só porque não é possível aferir a métrica contratual integral com logs.

## 6. Atualizar o arquivo existente (não criar outro relatório principal)

Atualize `docs/darwin-monitor/planos/05-investigacao-ind07-origem-venda-digital.md`, **preservando as evidências de código já documentadas** e revisando conclusões que não forem sustentadas.

A atualização deve conter:

- **Ferramenta utilizada:** nome/caminho, documentação lida e capacidade de consulta comprovada (sem segredos).
- **Datadog UAT:** fonte consultada, serviço/ambiente confirmados, intervalos, filtros, consulta(s) executadas e resultados **sanitizados**. Evite amostras completas de logs e IDs de clientes.
- **Campos observados:** nome, tipo de disponibilidade (texto, atributo pesquisável, agrupável/facet) e valores técnicos encontrados, quando existirem.
- **Query do widget:** sintaxe exata **somente se executada/comprovada**, incluindo de onde veio a query, fonte e agregação. Se não executada, marque **NÃO VALIDADA** e não a apresente como solução pronta.
- **Cobertura:** INTEGRAL / PARCIAL / AINDA NÃO VALIDÁVEL / BLOQUEADA, com justificativa técnica.
- **Correção de afirmações anteriores:** ajuste trechos como "a telemetria necessária já é emitida" para distinguir emissão prevista no código de ingestão efetivamente observada no Datadog.
- **Próxima ação objetiva** e menor caminho para validar/usar o widget.

**Não atualize `03-dashboard-alvo.md` nem o dashboard real nesta execução. Não altere código.**

## 7. Resposta final curta

Ao concluir, informe apenas:

1. Ferramenta utilizada (sem detalhes sensíveis);
2. Se conseguiu ler dados reais em UAT (**SIM/NÃO**);
3. Se `processadora` e `tipo_evento` existem na fonte (**SIM/NÃO/NÃO CONFIRMADO**);
4. Query para o widget validada (**SIM/NÃO**, e por quê);
5. Cobertura do IND-07 e caminho do relatório atualizado;
6. Uma próxima ação concreta.

**Pare após atualizar o IND-07. Não inicie outro indicador nem faça mudanças no Datadog.**
