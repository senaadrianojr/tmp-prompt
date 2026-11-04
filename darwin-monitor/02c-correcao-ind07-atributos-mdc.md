# Darwin Monitor — Prompt 02C: Correção da análise do IND-07 com evidência manual de MDC no Datadog

## Objetivo (continuação pontual; não recomeçar a investigação)

Atualize a análise **IND-07 — Origem (DXC × Pismo)**, da aplicação `springboot-pseg-base-venda-digital`, com **evidências verificadas manualmente no Datadog Log Explorer UAT** pelo responsável pelo projeto.

O relatório a atualizar é:
`docs/darwin-monitor/planos/05-investigacao-ind07-origem-venda-digital.md`

**Não reanalise todos os indicadores, não refaça do zero o levantamento do código, não implemente instrumentação, não modifique widgets ou dashboards.** Esta execução serve para corrigir o entendimento da estrutura dos logs e atualizar a proposta de consulta/widget, sem inventar validações.

## 1. Novas evidências — fornecidas pelo usuário após consulta manual ao Datadog

**Essas descobertas já foram comprovadas manualmente na interface do Datadog e devem ser registradas como "validação manual informada pelo usuário", não como "consulta executada pelo agente":**

- O Log Explorer UAT exibe registros com contexto **`mdc`**, e nele estão os atributos **`processadora`** e **`tipo_evento`**.
- O atributo da processadora está em **`mdc.processadora`**, não na raiz do log. O atributo de evento está em **`mdc.tipo_evento`**, não na raiz.
- A sintaxe **confirmada funcional para filtrar** é:
  - `@mdc.processadora:DXC`
  - `@mdc.tipo_evento:Contratacao`
  - `@mdc.tipo_evento:Cancelamento`
- Ao investigar os valores observáveis de **`mdc.processadora`**, foram encontrados **`DXC`** e **`BANKLY`**. **Não apareceram registros `PISMO` nas consultas/janelas inspecionadas**.
- Segundo o contexto de negócio fornecido pelo responsável, **Pismo atua atualmente como fallback da DXC e o roteamento evoluirá no projeto Darwin**, quando haverá mudança esperada na distribuição dos valores. Assim, **a ausência atual de `PISMO` nos logs consultados NÃO é, isoladamente, um defeito ou lacuna de instrumentação**.
- Existe evidência de que **ao menos alguns logs estão chegando ao Datadog com MDC estruturado**; a evidência inicial visualizada incluía `mdc.processadora: DXC` e `mdc.tipo_evento: Cancelamento`. A consulta por `Contratacao` também retornou registros conforme informado pelo usuário.
- O log apresentado continha `mdc.dd.service`, mas **não assuma que esse caminho é automaticamente igual ao campo reservado `service`** do Datadog. Confirme a disponibilidade do filtro de serviço adequado antes de incluí-lo em uma query final.

**Atenção à precisão:** NÃO afirme que `PISMO` jamais ocorreu em UAT, nem que `BANKLY` é sinônimo de `DXC` ou de `PISMO`. Registre o recorte temporal efetivamente disponível; se o usuário não informou o intervalo, escreva "**janela não informada**" e não invente datas, totais ou prints adicionais. A ausência de `PISMO` é uma **observação localizada**, não uma falha.

## 2. Ponto técnico que a análise anterior não identificou

A análise anterior tentou construir a query sobre campos de nível raiz e apresentou expressão `sum:trace.log_count by {@processadora}`, sem comprovar que ela é apropriada para uma fonte **Logs**.

**Corrija explicitamente**:

1. Filtros de atributos aninhados devem utilizar o caminho estruturado real, com prefixo `@`:
   - `@mdc.processadora:<valor>`
   - `@mdc.tipo_evento:<valor>`
2. A negação é feita com `-` e a existência do atributo com `*`. Exemplos **para diagnóstico**, sujeitos a validação manual no ambiente:
   - `@mdc.processadora:*`
   - `@mdc.processadora:* -@mdc.processadora:DXC`
   - `@mdc.tipo_evento:Contratacao @mdc.processadora:*`
3. Não reutilize `sum:trace.log_count by {@processadora}` como query de Logs, nem invente uma métrica `trace.log_count`. **Na fonte Logs**, use a sintaxe própria de pesquisa para selecionar os eventos; a contagem e o agrupamento devem ser configurados como **Count + Group by `mdc.processadora`** no Log Explorer/widget, **se a UI permitir agregação/facet para esse atributo**.
4. **Filtro de logs funcionando não equivale automaticamente a facet/agrupamento funcionando.** Separe as duas comprovações.

## 3. Como orientar a verificação restante (sem acesso programático)

Como o acesso automatizado ao Datadog por cookies/API legada não foi bem-sucedido, **não repita tentativas programáticas nem deduza que a interface também esteja bloqueada**.

Oriente o responsável a realizar apenas os testes manuais restantes:

1. Filtrar `@mdc.tipo_evento:Contratacao @mdc.processadora:*` na janela UAT que contenha eventos.
2. Verificar se os logs selecionados são realmente do serviço `venda-digital`, usando o **campo de serviço efetivamente disponível na UI**.
3. Abrir um log de contratação e conferir os valores reais de `mdc.tipo_evento` e `mdc.processadora`, e se a emissão está associada à solicitação/tentativa ou a outra etapa. Não mostrar identificadores de clientes.
4. Selecionar **Count** e tentar **Group by** em `mdc.processadora` na visualização de Logs. Se for necessária facet ou permissão adicional, registrar essa pendência, sem alterar configurações compartilhadas.
5. Confirmar se o agrupamento retorna `DXC` e/ou `BANKLY` para eventos de **Contratacao** especificamente; **não presuma que os valores vistos em todos os tipos de evento ocorrem também em Contratacao**.
6. Para `PISMO`, aceitar volume zero/ausência observada como situação possivelmente esperada na etapa atual. O widget deve passar a mostrar `PISMO` quando houver eventos com esse valor, desde que o filtro/agrupamento seja compatível e o novo tráfego esteja chegando à mesma fronteira monitorada.

**Não declare consulta agregada validada até o usuário confirmar o teste de Count/Group by.** Por enquanto está validada manualmente a **pesquisa por atributos**, não a construção final do widget.

## 4. Semântica e cobertura do IND-07

Preserve o entendimento de que um log de evento `Contratacao` representa o que a aplicação efetivamente registra naquele ponto. **Não converta automaticamente número de logs em número de contratos efetivados.**

- Nome sugerido do widget parcial: **"Eventos de contratação observados por processadora"**, a confirmar pelo ponto de emissão no código.
- Fonte: **Logs**; filtro base candidato `@mdc.tipo_evento:Contratacao @mdc.processadora:*` (restringir ao serviço/ambiente verificado, sem inventar nome/caminho).
- Agregação candidata: **Count**, agrupada por atributo **`mdc.processadora`**, se houver suporte na UI.
- Limites: retries, possíveis duplicações, fronteira observada do monólito, distinção entre evento local e contrato concluído.
- Valores: **DXC / BANKLY observados em logs no geral; PISMO não observado nas consultas realizadas**; não afirme cobertura simultânea desses valores em logs de contratação sem o recorte confirmado.
- **BANKLY:** preserve como categoria observada, sem renomear, fundir ou descartá-la para forçar a comparação DXC × Pismo. Investigue o significado operacional em momento apropriado, apenas a partir de evidência.

O indicador original **não deve ser anulado** por ser parcialmente atendido: mantenha o widget operacional e a lacuna referente à comprovação contratual integral claramente identificadas.

## 5. Atualização obrigatória do relatório

Atualize **somente**:
`docs/darwin-monitor/planos/05-investigacao-ind07-origem-venda-digital.md`

Modificações esperadas:

- Seção **Evidência Datadog UAT**: altere "NÃO EXECUTADO" para uma descrição precisa: **"acesso programático não validado pelo agente; usuário confirmou manualmente, na interface, logs com MDC e filtros funcionais"**. Diferencie observação manual e teste executado pelo agente.
- Documente os **caminhos exatos** `@mdc.processadora` e `@mdc.tipo_evento`, com os filtros já comprovados.
- Registre **DXC e BANKLY** como valores observados **nos registros pesquisados**, e **PISMO não observado no recorte manual**.
- Corrija qualquer afirmação de "ausência de logs no Datadog" ou "necessidade de reimplementar MDC/Logback": os registros confirmam ao menos uma cadeia funcional de emissão/ingestão do MDC.
- Remova a query inadequada `sum:trace.log_count by {@processadora}` como sugestão de implementação. Substitua por filtro de Logs comprovado + instruções de Count/Group by **pendentes de confirmação**, sem inventar sintaxe agregadora.
- Mantenha o **status PARCIAL** para a cobertura do requisito de negócio e separe um status específico para **pesquisa validada manualmente** versus **agregação do widget ainda pendente**.
- Revise a "próxima ação concreta": **validar manualmente Count/Group by para eventos Contratacao na UI**. Não proponha criar novos logs ou gerar tráfego de Pismo como pré-requisito para aceitar o widget parcial hoje.
- Preserve as outras evidências relevantes do relatório; não crie outro relatório principal nem altere `03-dashboard-alvo.md`.

## 6. Encerramento

Ao final, responda de modo conciso:

- Documento atualizado;
- Filtros corretos confirmados;
- Valores observados e ressalva sobre Pismo;
- Estado da pesquisa por atributos;
- Estado do agrupamento Count/Group by;
- Única próxima ação manual.

**Não construa o widget nesta execução; não avance para outro indicador.**
