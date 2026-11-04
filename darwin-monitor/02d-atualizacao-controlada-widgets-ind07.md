# Darwin Monitor — Prompt 02D: Correção controlada dos widgets de Origem (IND-07)

## Missão e limites

Você atuará **somente no IND-07 — Origem (DXC × Pismo)**, da aplicação **springboot-pseg-base-venda-digital**, no dashboard **[UAT] Darwin Monitor**.

Há dois artefatos existentes na **raiz da pasta do projeto Darwin Monitor**, usados anteriormente para publicar o dashboard:
- `uat_darwin_monitor_dashboard.json` — definição de todo o dashboard.
- `update_uat_dashboard.py` — script Python que utiliza a sessão corporativa já autorizada para atualizar o dashboard.

**Objetivo:** corrigir **somente os widgets existentes 1.1 e 1.2**, dedicados à Origem, para usarem telemetria de logs comprovada no Datadog UAT. Não crie um dashboard novo, não regenere os demais widgets e não altere aplicações Java ou instrumentações.

**Atenção:** o script pode usar HTTP PUT para enviar **todo** o JSON, mesmo quando apenas dois widgets mudam. Trate a publicação como operação de risco. A autorização para esta execução limita-se a esses dois widgets e às ações estritamente necessárias para publicá-los com segurança.

## 1. Evidências já confirmadas manualmente pelo responsável

Estas informações foram obtidas **na interface Datadog Log Explorer UAT** e não devem ser apresentadas como testes executados por você:

- Foram encontrados logs da aplicação com campos aninhados no objeto `mdc`.
- Sintaxe real e funcional dos filtros:
  - `@mdc.tipo_evento:Contratacao`
  - `@mdc.processadora:DXC`
  - `@mdc.processadora:*`
- Serviço observado: `springboot-pseg-base-venda-digital`; ambiente: `uat`.
- **Flex Logs habilitado** durante a consulta.
- A pesquisa de contratação com existência de processadora e o agrupamento **Count by `@mdc.processadora`** funcionaram na interface.
- Em **um recorte manual de uma hora**, o Top List mostrou **12 logs**, distribuídos em **BANKLY: 10; DXC: 2**.
- **PISMO não apareceu nesse recorte**, o que é compatível com o momento informado do projeto: Pismo atua atualmente como fallback da DXC e sua participação deverá mudar com a migração.
- **BANKLY** é valor distinto observado e **não deve ser convertido para DXC nem PISMO**, sem comprovação de regra de negócio.
- 12 registros de logs **não são necessariamente 12 contratos efetivados ou 12 contratos únicos**.

Filtro-base comprovado na UI para investigar as contratações:

```text
service:springboot-pseg-base-venda-digital env:uat @mdc.tipo_evento:Contratacao @mdc.processadora:*
```

Agregação observada na UI: **Logs → Count → Group by `@mdc.processadora`**, com Flex Logs ativo.

**Não escreva valores BANKLY/DXC/PISMO fixos na query nem use os números 10/2 como dados estáticos no dashboard.** O widget deverá refletir dinamicamente todos os valores encontrados, inclusive PISMO quando houver tráfego.

## 2. Fontes a consultar (sem refazer toda a investigação)

Leia apenas o necessário:

1. `docs/darwin-monitor/planos/04-inventario-filtrado-indicadores.md` — linha IND-07.
2. `docs/darwin-monitor/planos/05-investigacao-ind07-origem-venda-digital.md` — análise e evidências manuais.
3. `docs/darwin-monitor/dashboards/03-dashboard-alvo.md` — histórico da especificação; **não é a definição de produção do dashboard**.
4. JSON e script Python acima, localizando seus **caminhos reais**.
5. Se estritamente necessário, trechos atuais de `VendaDigitalService.java` e `LogMapper.java`, apenas para conferir a semântica do evento.

Não use relatórios históricos como fonte para nomes de atributos ou formato da query. A fonte de verdade para os filtros é o Log Explorer validado manualmente.

## 3. Preflight obrigatório — antes de qualquer escrita

Faça, em ordem:

1. **Leia integralmente o script Python**, identifique qual JSON ele carrega, qual endpoint ele chama, qual método HTTP usa, se atualiza o dashboard completo e como verifica falha/sucesso. **Não execute o script ainda.**
2. **Leia o JSON completo**, inventarie títulos, tipos e definições de widgets; encontre os widgets **1.1 e 1.2 pelo título e posição**, não por índice de array presumido.
3. Verifique quantas correspondências existem para os dois widgets. Se houver nomes duplicados, estrutura inesperada ou dúvida sobre qual widget alterar, **interrompa** e relate a ambiguidade.
4. Identifique o(s) **widget(s) de Logs existentes** no JSON que já utilizem o formato aceito para `data_source: logs`, consultas por string, agrupamento e `storage`/Flex Tier; use-os como referência **estrutural apenas**, sem copiar filtros antigos nem afirmar que funcionam.
5. Quando possível por **mecanismo de leitura autorizado**, obtenha a definição **atual** do dashboard do Datadog e compare com a cópia local (incluindo ordem, quantidade de widgets, metadados e template variables).
6. Se não for possível obter o estado remoto atual de modo confiável, **não execute PUT**: a cópia local pode estar desatualizada e a atualização completa apagaria mudanças feitas por outra pessoa. Informe o impedimento e peça a definição atual por exportação oficial ou outro mecanismo autorizado.
7. Crie **backup local** do JSON original **e da definição remota**, antes de qualquer edição. Não publique backups, cookies, payloads internos ou dados corporativos em repositório público.
8. Verifique se a definição remota difere do JSON local. Se houver diferenças fora dos widgets 1.1 e 1.2, **não sobrescreva o remoto com o JSON local antigo**; reconcilie apenas se for possível preservar todas as alterações remotas com comprovação, ou interrompa e exponha as diferenças relevantes.

**Segurança:** nunca leia cookies fora do mecanismo corporativo já configurado, exponha o conteúdo de credenciais ou copie tokens/headers para mensagens, relatório, commit ou stdout. Não modifique autenticação, credenciais, permissões ou políticas de Datadog. Se houver erro de acesso, **não contorne controles corporativos**.

## 4. Definição dos dois widgets-alvo

### Widget 1.1 — distribuição de eventos de contratação por processadora

- Preserve o tipo **Top List**, salvo se o formato real do widget exigir correção técnica comprovada.
- Fonte **Logs**, filtrada pelo serviço/ambiente/evento e pela presença de `mdc.processadora`.
- Computação **Count**.
- **Group by** campo aninhado `@mdc.processadora` usando a sintaxe/estrutura **real da definição JSON suportada pela versão do Datadog**.
- Limite de categorias que não descarte BANKLY, DXC e futuros valores PISMO (Top 10 pode ser suficiente se a cardinalidade verificada for baixa; não assuma sem conferir).
- Nome que reflita **eventos observados**, não **contratos efetivados**. Ex.: `Origem — Eventos de contratação por processadora`.
- Não use porcentagem implícita se o widget estiver configurado para contagens. Se desejar propor uma proporção percentual, apenas documente como melhoria futura; **não altere a fórmula nesta execução sem evidência da semântica e suporte da visualização**.

### Widget 1.2 — evolução temporal de eventos de contratação por processadora

- Preserve o tipo **Timeseries**.
- Mesma fonte, filtro e agrupamento do widget 1.1, com o comportamento temporal compatível.
- Título explícito: `Origem — Volume de eventos de contratação ao longo do tempo`.
- Não filtre a query por valor fixo de processadora.
- Evite dupla contagem indevida por configuração de agregação; contagem de logs não equivale a deduplicação por contrato.

### Sobre Flex Logs

A interface usada na validação tinha **Include Flex Logs** habilitado, e documentos anteriores citam `flex_tier`. **Não invente** nomes de propriedades do JSON. Confirme no schema/documentação disponível ou em widget funcional do mesmo dashboard como o armazenamento aplicável é representado. Se a API de dashboard não suportar a mesma consulta/armazenamento da UI, registre esse impedimento e **não apresente o widget como funcional**.

## 5. Política de modificação mínima e verificação local

1. Produza uma cópia de trabalho da **definição remota atual** (preferencial) e altere **somente** os nós que representam os widgets 1.1 e 1.2, preservando IDs, outras propriedades não relacionadas, layout, grupos, notas, variáveis, permissões, filtros e os demais widgets.
2. Não altere widgets 1.3, 1.4 nem qualquer widget dos grupos 2, 3 ou 4.
3. Não reformate/reordene por conveniência toda a estrutura. Uma serialização JSON pode variar, mas a **comparação semântica** dos nós não-alvo precisa permanecer igual.
4. Valide o arquivo JSON e sua estrutura com verificação local. Use o código/script existentes sem reescrevê-los salvo correção mínima imprescindível e fundamentada; alterações no script também devem ser auditáveis.
5. Gere um **diff semântico**: número de widgets antes/depois, títulos afetados, campos efetivamente modificados e confirmação de **zero alterações** em widgets não-alvo. Não inclua dados de autenticação.
6. Se surgir diferença inesperada, **interrompa antes da publicação**.

Não suponha que colocar a string de pesquisa no JSON seja suficiente: os widgets precisam usar o schema da API de Logs adequado para fonte, agrupamento, compute e armazenamento.

## 6. Publicação controlada no UAT

**Somente se todas as verificações anteriores passarem:**

1. Releia o estado remoto **imediatamente antes do PUT** para detectar alterações concorrentes. Se houver drift, interrompa e reconcilie; não sobrescreva mudanças alheias.
2. Execute **uma única publicação** através do script/método já existente, sem exposição de credenciais.
3. Verifique status da resposta; se houver erro, **não fique alterando queries e repetindo PUTs em loop**.
4. Faça nova leitura da definição remota para confirmar persistência das mudanças de 1.1 e 1.2 e **preservação integral** dos demais widgets.
5. Se a publicação não puder ocorrer com segurança, pare após a validação local e marque **PRONTO PARA PUBLICAÇÃO, NÃO PUBLICADO** (ou **BLOQUEADO**, se houver incompatibilidade estrutural).
6. **Nunca afirme que publicou se não houver evidência da resposta e da leitura posterior.**

## 7. Validação funcional do dashboard

Após publicar:

- Verifique, se o acesso permitir, o comportamento real dos widgets na UI do dashboard. Caso não consiga, registre como **pendente de validação manual**, mesmo que o JSON tenha sido aceito.
- Compare com o Log Explorer usando **mesma janela temporal, mesmo serviço, mesmo ambiente, mesmo filtro e mesma política de Flex Logs**.
- O resultado manual anterior (**BANKLY 10 / DXC 2, 12 logs em 1 hora**) é **fotografia histórica**, não critério para resultados futuros. Números poderão mudar conforme o tráfego e a janela.
- Aceite PISMO ausente como potencial estado esperado no momento atual; o widget deve aceitar esse valor quando ocorrer.
- Se o dashboard e o Log Explorer divergirem, verifique primeiro os filtros, janela, agrupamento e armazenamento, não invente mensagens ou faça mudanças sucessivas sem diagnóstico.

## 8. Documentação e encerramento

Atualize o relatório já existente:
`docs/darwin-monitor/planos/05-investigacao-ind07-origem-venda-digital.md`

Registre separadamente:
- **Validação manual do Log Explorer pelo usuário** (Count + Group by funcionando; BANKLY 10, DXC 2 na janela observada).
- **Validação da definição JSON** (passou/não passou).
- **Publicação UAT** (realizada/não realizada e motivo).
- **Validação visual no dashboard** (validada/pendente/falhou).
- **Cobertura semântica**: **PARCIAL** para "contratos por processadora", pois logs de contratação podem representar tentativas/eventos e não contratos efetivados.

Se útil, anexe ao relatório um resumo do diff **sem dados sensíveis**. Não altere outros documentos de indicadores.

### Resposta final obrigatória (curta)

Informe somente:
1. Quais foram os **dois widgets** encontrados e alterados;
2. Se foi feito **backup e verificação contra o estado remoto**;
3. Se houve **zero mudanças em widgets não-alvo**;
4. Se o JSON local foi validado;
5. Se a publicação no Datadog UAT foi realizada;
6. Se a visualização e os números foram conferidos na UI;
7. Pendência real, se houver.

**Encerre aqui. Não avance ao IND-01 e não faça mudanças adicionais não solicitadas.**
