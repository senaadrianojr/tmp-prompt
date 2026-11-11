# PROMPT 05I — Preparação de Sincronização Canônica e Padronização Editorial dos Widgets (Somente Candidatos Locais)

> **Executor:** Gemini CLI no workspace corporativo autorizado.
>
> **Repositório público:** este prompt contém somente diretrizes sanitizadas. Não enviar a este repositório dashboard JSON, consultas, identificadores, nomes internos, planilhas, relatórios, capturas, métricas, logs, código, credenciais, dados de clientes ou endpoints corporativos.
>
> **Permissão desta etapa:** consultas remotas autorizadas **somente de leitura** e criação de **novos artefatos candidatos locais**. **NÃO** sobrescrever/sincronizar ainda o arquivo canônico; **NÃO** publicar ou atualizar o dashboard remoto; **NÃO** alterar Java, scripts, testes, pipelines, Git corporativo ou produção. A autorização anterior para a publicação de um widget já foi consumida e **não** se estende à padronização de outros widgets.

## 1. Contexto encerrado: reutilize, não reabra

O ciclo anterior de saneamento e publicação de um único widget em UAT foi concluído, com GET de conferência e validação visual posterior declarada pelo responsável. Há relatório privado de execução com prefixo `29-` em `darwin-monitor/planos/`. O JSON canônico local permaneceu intocado por determinação de governança.

A equipe quer agora fechar a entrega de observabilidade, tornando o dashboard mais fácil de entender em apresentações e para pessoas que não participaram da implementação. **Aproveitar esta etapa de reconciliação para elaborar um pacote local de padronização de títulos e descrições**, sem mudar a telemetria nem os critérios técnicos aprovados.

**Não reabrir auditorias e investigações já concluídas.** Escopo dos indicadores da planilha: **sete linhas selecionadas**, cinco verdes e duas cinzas; as seis linhas amarelas anteriormente excluídas continuam fora do escopo. Não criar widgets para os indicadores excluídos. Entretanto, widgets já existentes que prestem serviço compartilhado **não devem ser eliminados ou modificados sem razão**.

**Perímetro de responsabilidade:** observar as aplicações sob gestão da equipe. Sucesso operacional comprovado até a transferência de responsabilidade pode ser classificado como sucesso local, sem exigir observação do processamento assíncrono de aplicações externas. Separar esse sucesso local do resultado final de negócio quando forem coisas distintas. Não declarar falta de cobertura operacional apenas porque o sistema externo não foi monitorado.

## 2. Dois produtos independentes na mesma execução

**Produto A — Sincronização canônica preparada, ainda NÃO aplicada:**
1. Ler o relatório 29 e localizar o arquivo canônico atual, o dashboard UAT validado e snapshots anteriores.
2. Realizar **GET remoto atual em leitura** e salvar novo snapshot de backup imutável local com timestamp, origem, hash e metadados. Se não houver GET acessível, registrar bloqueio e parar a preparação canônica; não usar snapshot velho como se fosse o atual.
3. Comparar semanticamente o **remoto atual x canônico atual** (todos os elementos e propriedades persistentes), preservando alterações manuais de UAT, configurações de precisão, tempos, filtros e conteúdo de widgets. Identificar diferenças conhecidas e qualquer novidade.
4. Produzir **uma cópia local candidata a novo canônico**, baseada no estado remoto mais recente e validada contra ele, **sem sobrescrever o canônico existente**. Se houver normalizações exigidas pelo formato local, documentá-las e provar equivalência funcional.
5. Incluir no relatório a lista exata das diferenças e o procedimento proposto para futura sincronização, sujeito a aprovação específica do responsável.

**Produto B — Padronização editorial preparada, ainda NÃO aplicada:**
1. Usar a **cópia derivada do remoto atual** como base; produzir **um segundo JSON candidato separado**, contendo **apenas propostas de título de grupos, título dos widgets e descrições dos widgets**.
2. Verificar a relação real de cada grupo e widget com os indicadores e com a pergunta que respondem; reutilizar a matriz e os relatórios privados já consolidados. Não auditar novamente código e Log Explorer.
3. Gerar no relatório uma tabela **grupo/widget | indicador relacionado | título atual | título proposto | descrição atual | descrição proposta | justificativa**. Não reclassificar indicador ou inventar relação só para preencher nomenclatura.
4. Quando a relação for ambígua (grupo atende múltiplos indicadores, widget compartilhado, nome local inconsistente), **não alterar automaticamente**: manter título atual no candidato e listar a sugestão como **REVISÃO HUMANA PENDENTE**.
5. Quando o tipo/schema de widget **não aceitar** `description` ou a propriedade não for exibida, **não inserir campo inválido**. Registrar limitação e alternativa apenas se suportada e sem criar/reorganizar widgets.
6. **Não aplicar renomeações em UAT nesta etapa**. O produto B é uma proposta visual a ser aprovada após revisão do diff.

## 3. Padrão editorial obrigatório

### Títulos de grupos

Padrão conceitual:
`<Nome legível do indicador executivo relacionado> — <qualificação breve do conjunto de widgets>`

- Nome **humano, não código técnico**; utilizar nomenclatura fiel à planilha (por exemplo, indicador de origem, índice de sucesso, falha no cancelamento), sem inventar novos indicadores.
- Complemento deve comunicar propósito (como contratos por origem, processamento de recorrências ou cancelamentos) e não repetir genericamente "monitoramento".
- Se um grupo atende **mais de um indicador**, usar identificação clara e honesta de conjunto, em vez de sugerir relação exclusiva falsa. Colocar como proposta condicionada se não houver denominação inequívoca.
- Não alterar a ordem, a hierarquia, a quantidade de grupos nem as posições.

### Títulos de widgets

Padrão:
`<Nome curto e legível do indicador/tema> — <descrição objetiva do sinal exibido>`

- **Não** usar como prefixo visual `IND-0X` (nem outro código técnico similar) seguido do nome. Códigos permanecem **somente na matriz técnica e rastreabilidade privada**.
- Evitar repetir em cada título o nome completo do grupo; títulos devem ser curtos, distintos e compreensíveis isoladamente.
- Evitar siglas pouco conhecidas quando houver alternativa legível; conservar siglas técnicas necessárias ao contexto operacional.
- Não renomear widgets de modo que passem a afirmar percentual, taxa, sucesso integral ou falha definitiva se exibem só contagens de logs, tentativas, eventos ou volume de DLQ.
- Se o título atual **já estiver adequado**, preservá-lo.

### Descrições dos widgets

Onde o schema e a UI suportarem, preencher `definition.description` **quando ausente** e revisar descrições existentes **somente quando vagas ou enganosas**.

Em **1 a 3 frases curtas**, explicar:
1. **O que o widget exibe**: evento/métrica, contexto e unidade real (eventos, tentativas, mensagens, contagem, percentuais somente se fundamentados).
2. **Para que serve no indicador executivo**: a pergunta respondida e a relação concreta com o objetivo do indicador.
3. **Limite necessário para interpretar corretamente**, se houver: por exemplo, falha operacional vs. resultado final de negócio, ou volume de DLQ vs. taxa de reprocessamento. Não sobrecarregar com ressalvas genéricas nem tratar sucesso local confirmado como insuficiente só por existir uma etapa externa.

Exemplo de **formato abstrato**, não para copiar como evidência: `Exibe ocorrências de [evento local] por [unidade] no período selecionado. Ajuda a acompanhar [aspecto do indicador] e não equivale, isoladamente, a [fórmula não comprovada].`

**Não inventar** conclusão de negócio, SLA, deduplicação, fórmulas, percentuais, cardinalidade nem garantia de processos externos. Respeitar a fronteira de responsabilidade das aplicações monitoradas.

## 4. Rigor das mudanças propostas

Para o **produto B**, aceitar alterações **somente em títulos e descrições**:
- Título de grupo no campo real correspondente;
- `definition.title` dos widgets;
- `definition.description` dos widgets, se suportado.

**Proibido mudar** `requests`, `query`, `search`, `formulas`, `compute`, `aggregation`, `data_source`, `indexes`, IDs, tipo, ordem, posições/layout, filtros, templates, variáveis, precisão, time range, cores, unidades, links, grupo de pertencimento ou número de widgets. **Não** criar nem remover widgets.

Criar duas comparações independentes:
- **Remoto atual × cópia canônica sincronizável (A):** semanticamente equivalente, sem mudanças funcionais.
- **Base remota (A) × candidato editorial (B):** diferença limitada aos campos de apresentação permitidos, com inventário dos nomes/descrições alterados.

Validar JSON/schema, IDs únicos, contagens, árvore de grupos, configuração de filtros e propriedades. Executar apenas validadores **comprovadamente offline/read-only**, sem scripts que alterem canônico nem façam PUT/POST. Não rodar dry-run de efeito incerto.

Se houver divergência remota inesperada, preservá-la e registrar no relatório; não escrever no canônico nem tentar "corrigir" remotamente.

## 5. Entregas privadas locais

Criar arquivos novos em diretórios já usados no workspace privado, sem sobrescrever o canônico nem os candidatos históricos:
1. **Snapshot atual UAT** (GET) e hash.
2. **Candidato A — cópia sincronizável do canônico** (estado funcional remoto atual, sem padronização editorial).
3. **Candidato B — dashboard editorial** (somente títulos e descrições propostos).
4. **Relatório novo** em `darwin-monitor/planos/` com prefixo `30-`, por exemplo `30-candidatos-sincronizacao-canonico-padronizacao-editorial.md`.

O relatório 30 deve registrar: origem dos snapshots e atualidade; diferença remoto × canônico anterior; tabela **antes/depois** de grupos/widgets e descrições; unidades/sinais e nexo com indicador; itens já adequados; itens sem suporte de `description`; itens ambíguos para decisão humana; diff permitido; resultados dos testes; paths e hashes; e proposta de **dois gates separados**:
- **Gate A:** autorização futura para atualizar somente o **canônico local** com a cópia aprovada do remoto;
- **Gate B:** autorização futura independente para publicar **títulos e descrições em UAT**, com novo GET/backup/diff antes de qualquer PUT, sem afetar métricas.

**Não pressupor que autorizar Gate A autoriza Gate B, ou vice-versa.**

## 6. Resumo de execução e parada

Entregar no terminal um resumo sucinto:
- quantitativo de grupos e widgets encontrados; quantidade **proposta** de renomeações e descrições preenchidas/ajustadas;
- confirmações de zero alteração das queries e zero escrita remota/canônico;
- artefatos candidatos A e B com caminhos exatos;
- itens com dúvidas ou tipos de widget sem suporte;
- comparação dos diffs e status **PRONTO PARA REVISÃO / BLOQUEADO**;
- autorizações futuras separadas A e B.

**PARE.** Não sincronizar efetivamente o canônico, não publicar padronizações no Datadog, não tocar em produção, não modificar código/scripts/planilhas/relatórios históricos. O responsável técnico e o ChatGPT revisarão as propostas antes de autorizar qualquer aplicação.
