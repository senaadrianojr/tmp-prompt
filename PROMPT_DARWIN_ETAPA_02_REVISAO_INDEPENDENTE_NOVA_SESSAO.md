# DARWIN MONITOR — REVISÃO INDEPENDENTE DA ETAPA 02
## Nova sessão, validação crítica e decisão para a Etapa 03

### 0. Instrução principal

Atue como **revisor técnico independente** de arquitetura, observabilidade e documentação.

Esta é uma **nova sessão de contexto**. Você recebeu um resumo da sessão anterior para orientar a continuidade. Esse resumo NÃO é fonte de verdade e NÃO deve ser reproduzido automaticamente como resultado da revisão.

**Objetivo:** verificar, em uma única rodada focada, se `REVALIDACAO_TECNICA_ETAPA_02.md` contém evidências suficientes e coerentes para permitir a Etapa 03 — Consolidação Documental. Identifique apenas as lacunas que realmente importam e decida o gate com justificativa.

**NÃO execute a Etapa 03. NÃO repita o discovery inteiro. NÃO declare a Etapa 02 concluída apenas porque a sessão anterior o fez.**

### 1. Preflight na raiz real do projeto

1. Confirme o diretório atual. O workspace pode estar **já dentro de `darwin-monitor/`**; não crie um `docs/darwin-monitor/` redundante.
2. Leia diretamente os artefatos reais:
   - `./REVALIDACAO_TECNICA_ETAPA_02.md`;
   - `./AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md`;
   - `./README_FERRAMENTAS_AMBIENTE.md` se existir;
   - runbook corporativo de acesso ao Datadog, **somente se for preciso consultar a plataforma**;
   - documentos ativos de `indicadores/`, `dashboards/` e `planos/` relevantes aos seis indicadores.
3. Confira paths reais e, quando necessário, compare as versões anteriores do relatório via Git em modo somente leitura.
4. Use o resumo da sessão anterior **apenas como índice** de onde procurar, não como evidência de que algo foi demonstrado.
5. Registre brevemente o que pôde e o que não pôde ser acessado. Não invente evidências de código, planilha ou Datadog.

### 2. Escopo de trabalho — seis indicadores

| Indicador | Aplicação indicada pelo time |
|---|---|
| Índice de Sucesso | Gestão Eventos Conta Cartão |
| Taxa de Falha | Gestão Eventos Conta Cartão |
| Taxa de Falha no Cancelamento | Gestão Eventos Conta Cartão e Gestão Eventos Pagamento |
| Taxa de Reprocessamento | Gestão Eventos Conta Cartão |
| Parcela Cobrada × Parcela Lançada | Gestão Eventos Pagamento |
| Origem | Venda Digital |

O recorte acima é decisão de trabalho, **não validação automática de responsabilidade ou cálculo**.

A planilha foi lida em uma sessão anterior por Python/ZIP/XML e foram relatados quatro indicadores com `Responsável = Seguros` e dois com `Responsável = bvseg` (Parcela Cobrada × Parcela Lançada e Origem). Confira a evidência exata no relatório/planilha, sem normalizar `bvseg` para `Seguros` nem remover esses dois da matriz. Sua inclusão na **entrega** depende de decisão humana.

A definição de **Origem** está pendente: canal/origem da contratação ou via de pagamento (possivelmente Pismo/DXC). Não equiparar com Processamento MOB e não inventar fórmula.

### 3. Escopo da revisão: cinco pontos de alto risco

#### A — Integridade documental

Verifique o relatório efetivo, não apenas o resumo exibido no terminal:
- existem nomes de repositório, paths de código, classes/métodos e linhas ou símbolos rastreáveis?
- as planilhas estão referenciadas por nome/aba/célula onde isso é necessário?
- dashboards/widgets têm identificadores reais; queries, filtros, períodos e resultados foram registrados quando afirmados como validados?
- existem campos vazios, "****", "..." usados como substitutos de evidência, `Responsável:` sem valor, IDs em branco, `widget 0` genérico ou listas sem nomes?
- o plano da Etapa 03 aponta caminhos **existentes** e alterações documentais específicas?

Não substitua placeholders por dados presumidos. A ausência de uma referência deve virar `NÃO COMPROVADO` com fonte/ação pendente.

#### B — Cálculo de taxas

Os dois indicadores antes classificados como "Calculável Hoje" precisam de comprovação suficiente:
- **Taxa de Falha no Cancelamento:** numerador (cancelamentos com falha definitiva), denominador (total elegível), período, mesma população, tratamento de retries, query/fonte de ambos e evidência de disponibilidade.
- **Taxa de Reprocessamento:** numerador definido funcionalmente, denominador, operação única versus tentativas, DLQ versus reprocessamento efetivo, janela, duplicidade e query/fonte de ambos.

**Encontrar um log de erro ou uma query de DLQ NÃO prova uma taxa.** Se faltarem denominador, fórmula ou resultado observado, reclassifique como `MÉTRICA PARCIAL / TAXA NÃO COMPROVADA`, com motivo objetivo. Não preserve "Calculável Hoje" por inércia.

#### C — Ausência de widget versus instrumentação

- **Taxa de Falha:** a inexistência de widget não é prova de falta de telemetria. Diferencie (a) falta apenas de query/widget, (b) dado emitido mas sem cobertura comprovada e (c) instrumentação realmente necessária.
- **Índice de Sucesso:** sucesso HTTP e sucesso funcional são coisas distintas. O Evento 31/status 8 foi identificado no código anteriormente: avalie se o dado correspondente já é observável antes de propor nova instrumentação.
- Toda proposta de instrumentação deve identificar aplicação, ponto do fluxo, campo/evento ausente, evidência da ausência e alternativa mais simples avaliada.

#### D — Evidência real no Datadog

O agente já conseguiu autenticar-se pelo procedimento corporativo, ler definições de widgets e, em teste anterior autorizado, criar/excluir um widget de teste. **Isso demonstra capacidade de ferramenta; NÃO demonstra validade dos indicadores.**

- Leia `README_FERRAMENTAS_AMBIENTE.md` e siga somente os métodos autorizados, com verificação de sessão quando necessário.
- O procedimento anterior usou um endpoint interno de current-user para **verificar sessão**, não necessariamente a API pública do Datadog. Não substitua automaticamente por API que exija API Key/Application Key.
- **Somente operações de leitura nesta revisão**. Não criar, editar ou excluir widgets, dashboards ou monitores.
- Confronte em consultas pontuais os widgets/queries citados no relatório com seus resultados **realmente observados**. Registre intervalo, sinal, serviço, filtros, agregação e se retornou dados, sem dados de clientes nem credenciais.
- **Metadado do widget ≠ execução da query; query que retorna dados ≠ fórmula correta.**
- Se a plataforma estiver indisponível agora, reutilize resultados anteriores **explicitamente rotulados como históricos** e marque as lacunas atuais. Não entre em ciclos indefinidos de autenticação.

#### E — Escopo e plano de consolidação

- Mantenha os quatro indicadores relacionados a Seguros como foco operacional da Etapa 03, sem excluir os dois com `bvseg` da documentação do backlog/pendências.
- Não declare os indicadores fora da entrega como tecnicamente inviáveis.
- Verifique que cada alteração planejada para Etapa 03 possui **path relativo real**, conteúdo a atualizar e evidência (ou pendência) a registrar.
- A Etapa 03 é **documental**: não exige que todos os widgets funcionem, mas exige que o estado atual, o estado-alvo e as incertezas sejam descritos sem afirmações inventadas.

### 4. Matriz obrigatória de julgamento

Para cada um dos seis indicadores, apresente no resultado:
- **Regra funcional:** confirmada / parcial / pendente, com fonte.
- **Comportamento do código:** comprovado / parcial / não verificado, com referência.
- **Datadog:** query com valor observado / apenas configuração consultada / não verificado, com referência.
- **Numerador e denominador:** ambos presentes / parcial / não se aplica; identificar suas fontes.
- **Viabilidade atual:** indicador calculável / métrica operacional parcial / depende de query / depende de instrumentação / depende de fonte externa / bloqueado por decisão.
- **Ação seguinte:** editar documento, consultar fonte específica, solicitar decisão do Negócio, implementar em etapa futura; evitar ações genéricas.

Use a distinção entre **observado**, **inferido** e **proposto**. Nunca converter hipótese em fato para preencher a matriz.

### 5. Método de execução eficiente

1. Faça uma **passagem completa** pelo relatório para identificar evidências ausentes e contradições.
2. Execute **somente** as verificações dirigidas que alterariam a decisão do gate ou uma classificação de viabilidade; priorize os dois indicadores chamados "Calculável Hoje" e os dois marcados como dependentes de instrumentação/MVP.
3. **Limite o escopo:** não recatalogar os 28 arquivos da auditoria, não criar nova taxonomia, não investigar indicadores excluídos, não refazer mapeamentos já comprovados.
4. Se não houver como confirmar uma informação em tempo razoável, registre a pendência, responsável e impacto; **não simule confirmação**.
5. Não produza novas versões do documento em arquivo de nome diferente.

### 6. Saída desta revisão — SÓ LEITURA

**Nesta execução não edite arquivo algum.** Nem o relatório da Etapa 02, nem READMEs, nem repositórios, nem Datadog. A saída deve ser **apenas um parecer estruturado no chat/terminal**, permitindo aprovação humana antes de qualquer alteração.

Apresente o parecer na seguinte ordem:

**1. Veredito objetivo**
- `APTO PARA ETAPA 03`, `APTO COM PENDÊNCIAS DOCUMENTADAS` ou `NÃO APTO`;
- fundamento técnico do veredito, em até um parágrafo.

**2. Matriz dos seis indicadores**
- use os campos da seção 4, com referências;
- diferencie calculabilidade do indicador de simples existência de widget.

**3. Falhas de documentação realmente encontradas**
- caminho do arquivo/seção, trecho problemático, evidência e correção objetiva;
- se não encontrou falha, diga o que inspecionou para chegar a essa conclusão;
- não liste problemas de versões antigas já corrigidos.

**4. Pendências prioritárias para a Etapa 03**
- separar o que **impede a documentação** do que **impede apenas a implementação**;
- questões de responsabilidade `Seguros`/`bvseg` e definição de Origem devem ter dono humano.

**5. Plano de correção mínimo**
- se houver erros, listar somente as alterações necessárias em `REVALIDACAO_TECNICA_ETAPA_02.md` e as evidências a preservar;
- evitar recomendar novo discovery completo.

**6. Decisão solicitada ao usuário**
- recomendar `PROSSEGUIR DIRETO À ETAPA 03` se o relatório estiver íntegro;
- ou `CORRIGIR PONTOS ESPECÍFICOS ANTES` se existirem falhas materiais;
- ou `SOLICITAR DECISÃO DE NEGÓCIO` quando necessária para a documentação.

**Pare e aguarde a aprovação do usuário.** Não atualize o relatório automaticamente e não inicie a Etapa 03.

### 7. Regras finais

- Respeite as ferramentas já comprovadas, mas revalide permissões de cada nova sessão quando indispensável.
- Não alegue impossibilidade sem verificar o recurso disponível; também não contorne controles corporativos.
- Não exponha cookies, tokens, dados pessoais nem informações de sessão.
- Não faça commit/push, não altere dados de produção e não execute testes que tenham efeitos colaterais.
- O resumo recebido da sessão anterior é contexto secundário. **Arquivos e consultas verificáveis prevalecem.**
- Seja rigoroso, mas pragmático: **o objetivo é encerrar a revisão da Etapa 02, não perpetuá-la**.
