# Darwin Monitor — Prompt 03C: Consolidação do IND-01 e definição fundamentada dos widgets

## Missão: concluir a análise, não publicar

Continue **exclusivamente o IND-01 — Índice de Sucesso**, na aplicação de **Eventos Conta Cartão**. A investigação do código e a validação manual dos filtros básicos de Logs já foram realizadas. Sua tarefa é **consolidar as evidências, analisar a semântica e possíveis duplicações e definir até três widgets operacionais defensáveis**.

**Não execute atualizações no Datadog, não modifique o JSON do dashboard, não rode `update_uat_dashboard.py`, não altere código ou instrumentação Java, não crie monitores e não avance ao IND-02.**

Objetivo geral do Darwin Monitor: estabelecer uma **linha de base de observabilidade das operações sob responsabilidade do time** durante o desmembramento do monólito e a migração para Pismo. Não confunda processamento local com efetivação de negócio em serviços externos de outros times.

## 0. REGRA OBRIGATÓRIA DE DIRETÓRIOS

A raiz do projeto **já é `darwin-monitor/`**. Identifique o diretório real de trabalho antes de abrir ou gravar documentos. O local correto para relatórios **é `planos/` diretamente dentro da raiz**.

- Localize e leia o relatório `08-validacao-telemetria-ind01-conta-cartao.md` em `planos/`.
- Localize e leia a investigação `07-investigacao-ind01-indice-sucesso-conta-cartao.md`, mesmo que tenha sido gerada na estrutura antiga.
- Leia a linha do IND-01 no inventário existente e, se necessário, confira a planilha original `Monitoramento Cartões 1` na aba **Monitoramento**.
- Produza **somente** `planos/09-consolidacao-ind01-e-widgets-conta-cartao.md` na raiz `darwin-monitor/`. Se já existir, use versão incremental e informe o caminho.

**É proibido criar `docs/darwin-monitor/planos/` dentro de `darwin-monitor/` ou criar outro `darwin-monitor/` aninhado.** Não mova ou apague arquivos antigos nesta tarefa.

## 1. Evidências manuais já confirmadas pelo responsável no Log Explorer UAT

O responsável executou pessoalmente consultas no Datadog Log Explorer, com filtros do serviço e do ambiente UAT, e **obteve resultados positivos para todos os testes abaixo**:

| Teste | Evento | Resultado informado |
|---|---|---|
| A | `service:sboot-pseg-base-orch-gestao-eventos-conta-cartao env:uat` | Registros do serviço encontrados |
| B | Texto `Iniciando fluxo de recorrência assíncrono para proposta` | Registros encontrados, contendo o marcador `[EVENTO-31-PUBSUB]` |
| C | Texto `Retorno com sucesso do orquestrador de recorrência` | Registros encontrados **em períodos mais antigos** (UAT tem tráfego irregular) |
| D | Marcador `[EVENTO-31-PUBSUB][FALHA-NEGOCIO]` | Registros encontrados |
| E | Marcador `[EVENTO-31-PUBSUB][RETRY-PUBSUB]` | Registros encontrados |

**Trate essas confirmações como evidência manual do usuário, jamais como consulta executada por você.** Elas comprovam existência de registros correspondentes no recorte pesquisado, **não** contagens exatas, deduplicação, equivalência de janelas, integridade do funil, fórmula percentual, valores de atributos ou disponibilidade de facet para agrupamento.

**Não invente quantidade de registros, janela de tempo, screenshots, tags `mdc` ou percentual.** O teste C exigiu ampliar/alterar o período para achar logs antigos, logo **não há base até agora para dividir os resultados B e C**. Caso os relatórios anteriores afirmem "query de sucesso validada", corrija: foi validada **a busca textual e a existência do log**, não a taxa.

**Use o nome real de serviço acima, verificado manualmente**. Para consultas futuras, a interface do usuário utilizou **Include Flex Logs**, mas confirme o mesmo comportamento para qualquer widget antes de propor publicação.

## 2. Não repetir a auditoria integral: inspeção pontual do fluxo real

No repositório atual da aplicação, comece por `SolicitacaoRecorrenciaViaPubSub.java` (pista fornecida por integrante do time). Localize os **loggers reais** correspondentes aos eventos B–E e seus métodos, decisões e condições de emissão. Siga **somente os colaboradores diretos necessários** para responder:

1. O log B indica **consumo de uma mensagem**, início de método ou solicitação nova? Pode ser emitido novamente para a mesma proposta numa retentativa/reentrega?
2. O log de "chamando orquestrador" existe? Está antes da chamada e, portanto, **não** comprova envio bem-sucedido?
3. O log C é emitido depois de quê? Resposta HTTP 2xx, retorno do método, ACK, estado de negócio local ou evento externo? O que ele **não** comprova?
4. As falhas D e E são **mutuamente exclusivas ou podem coexistir** para uma mesma mensagem/tentativa? Uma falha de negócio pode ser seguida de retry ou vice-versa?
5. Há rotas alternativas, exceções ou saídas silenciosas que escapam desses logs?
6. Quais campos já existentes podem servir para correlacionar eventos na investigação (sem publicar identificadores de cliente e sem criar métricas com alta cardinalidade)?
7. Existe evidência de que a versão investigada estava implantada no UAT quando os registros ocorreram? Se não, deixe essa ressalva explícita.

Registre **repositório, branch/commit se disponíveis, classe, método e condição verificada**, sem confiar em números de linha de documentos antigos. Não proponha novas mensagens de log se os eventos atuais já bastarem para cobertura parcial; só registre gaps concretos.

## 3. Reconciliação da fórmula original com a observabilidade real

Transcreva da planilha a fórmula e o objetivo **literalmente**, sem substituí-los. O histórico da análise registra:

- **Fórmula original:** `Recorrências recebidas ÷ recorrências enviadas (%)`.
- **Objetivo** ligado à integridade da integração BV–Bankly.

A fórmula pode ser ambígua ou inesperada como "índice de sucesso", mas **não a inverta automaticamente nem a declare inválida sem explicar os eventos correspondentes**. Mantenha separadas:

**A. Requisito original:** o que a planilha pede e o que falta para verificá-lo em todas as suas dimensões.

**B. Cobertura operacional parcial:** o que os registros da aplicação conseguem realmente monitorar, por exemplo:
- eventos de início/recebimento de recorrência;
- retornos de sucesso local do orquestrador;
- falhas funcionais e eventos de retry.

Não assuma que `INFO` significa sucesso final, que `HTTP 200` significa adesão efetivada, nem que retry ou DLQ equivale a falha definitiva de negócio.

**C. Eventual taxa operacional alternativa:** somente se você demonstrar que numerador e denominador se referem à **mesma população de tentativas**, com critérios de contagem compatíveis, janela temporal, tratamento de reentregas e denominador zero. Se isso não puder ser comprovado, **não forneça um percentual candidato como se fosse válido**. Prefira volumes separados e registre qual validação permitiria futuramente uma taxa.

## 4. Definição dos widgets (sem criação ainda)

Proponha **até três widgets**, priorizados por valor para a monitoria Darwin, por exemplo:
- Volume de eventos de início de processamento de recorrência;
- Volume de retornos de sucesso local do orquestrador;
- Ocorrências de falha de negócio e retry, separadas de forma a não sugerir que são a mesma categoria.

Esses são **candidatos**, não prescrições. Escolha somente eventos que o código e os logs suportem e justifique a quantidade mínima de widgets. Se falha e retry forem agrupados num só widget, indique categorias separadas e possíveis sobreposições.

Para cada widget defina, em tabela curta:
- Nome **honesto e operacional** (não "Índice de Sucesso" se o KPI ainda não estiver comprovado);
- Objetivo durante a migração;
- Ponto/condição no código e a qual dos testes manuais B–E corresponde;
- Fonte **Logs** (ou outra **comprovada**), filtros do serviço/ambiente e texto literal já confirmado;
- **Count** ou outra agregação realmente justificada, e a dimensão, se existir evidência;
- Cautela com retries, duplicações, janelas e limites de interpretação;
- Estado **BUSCA MANUAL VALIDADA / AGREGAÇÃO PENDENTE / PRONTO PARA TESTE DE WIDGET**;
- Passo mínimo de validação adicional.

**Não use `sum:trace.log_count` ou `sum:log.count` como se fosse métrica confirmada.** Para widgets alimentados por Logs, a pesquisa textual no Log Explorer e a operação `Count` são conceitos distintos. Não invente propriedade JSON, `data_source`, facet, configuração Flex Tier ou sintaxe agregada; esses detalhes serão verificados **na etapa posterior de publicação**, usando um widget existente como referência estrutural.

Se quiser sugerir Timeseries, Top List, Query Value ou tabela, explique a utilidade e não prometa que o modelo JSON atual já a suporta.

## 5. Testes adicionais permitidos e limites anti-loop

Esta execução **não exige chamar novamente o Datadog**, pois as cinco pesquisas textuais já foram confirmadas manualmente. Se o acesso de consulta existir por ferramenta corporativa **já autorizada**, é permitido coletar **somente evidências de leitura relevantes e sanitizadas**, sem alterar ferramenta, autenticação ou dashboard. A ausência de leitura programática não bloqueia a consolidação.

Para qualquer teste futuro com o usuário:
- Faça **um pedido mínimo** por vez; evite repetir A–E.
- Priorize primeiro **Count com mesma janela temporal e mesma fonte/serviço** nos eventos que você deseja comparar, se necessário.
- Não confunda ausência de log C na janela recente com ausência de instrumentação: há evidência de C em período mais antigo.
- Se dados insuficientes impedirem percentual, mantenha widgets parciais e documente o limite. **Não recomece um loop de queries alternativas**.

## 6. Entregável em arquivo novo, exclusivamente em planos/

Crie `planos/09-consolidacao-ind01-e-widgets-conta-cartao.md` com:

1. **Requisito original e escopo de cobertura**, incluindo a fórmula literal.
2. **Matriz de evidências A–E**, separando código, busca manual confirmada, agregação e semântica.
3. **Fluxo e pontos de emissão**, com análise de reentregas, retries, falhas e sucesso local.
4. **Decisão sobre a taxa percentual**: VALIDÁVEL / AINDA NÃO VALIDÁVEL, com justificativa objetiva.
5. **Proposta de até três widgets operacionais** com especificação precisa e limitações, **sem payload de publicação**.
6. **Gaps reais**, apenas quando sustentados no código/observação.
7. **Cobertura final provisória do IND-01**: INTEGRAL / PARCIAL / AINDA NÃO VALIDÁVEL / BLOQUEADO. Diferencie cobertura de negócio da validação de cada widget.
8. **Próxima ação única**, de menor custo, para encaminhar os widgets à etapa de configuração e validação no dashboard.

Relate o caminho absoluto ou relativo à raiz identificado; não altere `07-...` nem `08-...` para preservar o histórico da investigação. Não copie logs de cliente, IDs de propostas, cookies nem URLs internas.

## 7. Resposta final ao usuário

Responda de forma curta:
- arquivo criado em `planos/`;
- evidências confirmadas (cinco buscas manuais);
- status de cobertura;
- se uma taxa percentual foi considerada comprovável ou não;
- widgets recomendados;
- **uma** próxima ação concreta.

**Encerre. NÃO atualize `uat_darwin_monitor_dashboard.json`, NÃO execute `update_uat_dashboard.py` e NÃO publique widgets.**
