# PROMPT 05D — Consolidação do Escopo Final e Plano de Conclusão do Darwin Monitor

> **Execução:** nova sessão do Gemini CLI, dentro do workspace corporativo autorizado.
>
> **Atenção: repositório público de prompts.** Este arquivo contém apenas diretrizes genéricas. Não publique neste repositório conteúdo do ambiente corporativo: planilhas, relatórios, código, nomes internos, IDs, queries, métricas, logs, capturas, credenciais ou dados de clientes.
>
> **Fase atual: fechamento do escopo e plano objetivo de execução.** A única alteração autorizada é criar um novo relatório **local** de planejamento. **Não editar nem publicar widgets, código, JSON, scripts ou recursos remotos.**

## 1. Missão — convergir para a entrega, não reabrir auditorias

Atue como revisor técnico e planejador de implementação de observabilidade.

Já foram realizadas etapas de auditoria do indicador de cancelamento, desenho operacional e reconciliação com a planilha de monitoramento, com relatórios locais de prefixos **22-**, **23-** e **24-** no diretório de planos do workspace Darwin Monitor.

**Seu objetivo único nesta sessão é encerrar a definição do escopo e produzir um PLANO EXECUTÁVEL E FINITO de construção, saneamento, validação e publicação controlada do dashboard**, limitado às linhas expressamente selecionadas pelo responsável.

Reutilize as evidências e decisões existentes. **Não refaça investigações extensas nem crie uma nova rodada de auditoria geral.** Verifique detalhes pontuais somente quando necessários para uma decisão de implementação ou para identificar uma inconsistência material.

**Diretriz central: PLANILHA → CÓDIGO/CONTRATOS → LOGS/MÉTRICAS REAIS → DATADOG/DASHBOARD.**
O dashboard atual não determina o escopo, e o simples fato de existir um widget com nome parecido não comprova atendimento ao requisito.

## 2. Escopo congelado por decisão humana — obrigatório

A referência é a planilha-mestre **Monitoramento Cartões 1.xlsx**, na aba de indicadores de monitoramento. As cores abaixo foram definidas previamente pelo responsável como triagem de viabilidade.

### INCLUIR — cinco linhas VERDES

- **Linha 2:** Índice de Sucesso.
- **Linha 11:** Taxa de Falha.
- **Linha 13:** Taxa de Falha no Cancelamento.
- **Linha 14:** Taxa de Reprocessamento.
- **Linha 19:** Origem.

**Verde significa viável ou parcialmente viável; não significa integralmente atendido.**

### INCLUIR — duas linhas CINZAS

- **Linha 17:** Parcela Cobrada × Parcela Lançada — conferência relacionada a Charge ID.
- **Linha 18:** Parcela Cobrada × Parcela Lançada — datas/MOB.

**Cinza significa viabilidade a confirmar, não obrigação de inventar solução.** Decida com evidências se é atendível integralmente, parcialmente ou se depende de outra fonte/instrumentação.

### EXCLUIR — seis linhas AMARELAS

**Linhas 4, 8, 9, 10, 12 e 15.**

Essas linhas foram consideradas pelo responsável **não atendíveis apenas com Datadog e logs**, inclusive por restrições registradas na coluna **OBSERVAÇÕES** da planilha.

Regras:
- **NÃO investigar tecnicamente, desenhar, construir ou propor novos widgets para atender essas seis linhas.**
- Não gastar esforço procurando uma maneira indireta de implementar os indicadores amarelos.
- Não remover automaticamente widgets existentes só por estarem associados a uma linha amarela: podem servir a outra finalidade legítima.
- No relatório, documente apenas que elas foram excluídas **por decisão expressa do responsável**, sem reabrir sua viabilidade.
- Se a coluna OBSERVAÇÕES contiver restrição pertinente a qualquer linha dentro do escopo, registre essa restrição e a necessidade de decisão humana, sem tentar contorná-la.
- **Não reclassifique as cores nem amplie o escopo por conta própria.**

**Total de trabalho: EXATAMENTE 7 linhas (5 verdes + 2 cinzas).** Confirme o conteúdo das células e os objetivos correspondentes na planilha; se houver diferença entre o nome listado acima e a célula real, transcreva o original e sinalize a divergência, preservando a seleção por número de linha.

## 3. Regras de aderência: widgets individuais E combinação de widgets

Um requisito pode ter atendimento operacional útil por **um widget ou por um conjunto complementar de widgets**. Examine ambas as possibilidades.

Para cada linha selecionada:
1. Registrar objetivo, fórmula, observações relevantes, unidade e evento/desfecho realmente exigido.
2. Verificar os sinais já documentados em código, contratos, logs e APM, separando **observado em UAT**, **presente em código** e **alegado em relatório**.
3. Identificar os widgets existentes que contribuem, com o papel de cada um e o grau de independência ou sobreposição.
4. Explicar **qual pergunta operacional cada widget responde** e **qual porção do objetivo original permanece descoberta**.
5. Considerar criação de widget novo quando há dado real, utilidade e lacuna não coberta.
6. Considerar ajuste de widget quando há intenção correta, mas query, título, descrição, fórmula ou filtro inadequado.
7. Considerar remoção quando o widget é enganoso, tecnicamente infundado ou realmente redundante, **somente depois de confirmar que ele não apoia outro requisito**.
8. **Não chamar “integral” a soma de widgets apenas correlatos.** A combinação precisa demonstrar cobertura semântica do requisito, não apenas tratar do mesmo assunto.

**Classificações de aderência permitidas:**
- **INTEGRAL — WIDGET ÚNICO COMPROVADO**
- **INTEGRAL — CONJUNTO DE WIDGETS COMPROVADO**
- **PARCIAL — WIDGET ÚNICO COMPROVADO**
- **PARCIAL — CONJUNTO DE WIDGETS COMPROVADO**
- **SINAL IDENTIFICADO, MAS SEM VALIDAÇÃO UAT**
- **NÃO COBERTO / DEMANDA CONSTRUÇÃO**
- **DEPENDENTE DE INSTRUMENTAÇÃO, NEGÓCIO OU OUTRA ÁREA**
- **INVIÁVEL COM DADOS ATUAIS**

Não misture na mesma classificação **cobertura do objetivo de negócio** e **valor operacional auxiliar**. Registre os dois separadamente se divergirem.

## 4. Preserve as decisões anteriores sem declarar sucesso artificial

As etapas anteriores já trataram sinais de recorrência, taxa de falha, origem e cancelamento.

- Indicadores publicados e validados manualmente pelo responsável **permanecem intactos neste ciclo**; não reabrir publicação concluída sem evidência material nova.
- IND-01 e IND-02 possuem limitações conhecidas de reconciliação por adesão/proposta; não converter contagens de logs ou retries em percentuais fictícios.
- O IND-03 possui um recorte de **falhas operacionais de cancelamento** e um possível objetivo distinto de **cobranças indevidas posteriores**. O saneamento de um widget operacional pode ser útil, mas não prova atendimento integral à segunda questão.
- Sinais de retry, NACK e erros HTTP não são equivalentes a falhas terminais nem a propostas únicas.
- Cancelamento e estorno são processos diferentes. Não os agrupar como mesma métrica.
- A existência de campos de correlação em alguns fluxos não comprova presença em todos os caminhos; validações de logs pós-deploy podem continuar pendentes por falta de tráfego.

Se um indicador já publicado estiver **operacionalmente encerrado no recorte aprovado**, registre isso. **Separadamente**, classifique sua aderência ao requisito original com honestidade, sem forçar integralidade.

## 5. Insumos e inspeção mínima — sem desperdício

Localize dentro do workspace privado:
- A planilha mestre (linhas 2, 11, 13, 14, 17, 18 e 19; coluna OBSERVAÇÕES para contexto).
- O relatório **24-** de reconciliação (fonte principal de progresso e inventário).
- Os relatórios **22-** e **23-** somente nos trechos necessários ao IND-03.
- As definições atuais do dashboard (cópia local e, quando acessível, leitura remota **somente leitura**).
- Evidências e documentação anterior dos indicadores já consolidados, apenas para evitar retrabalho.

Não ler todas as planilhas de ponta a ponta outra vez. Não inspecionar dependências de build, milhares de arquivos ou repositórios inteiros indiscriminadamente.

**Quando a configuração remota não estiver acessível, não afirmar que a cópia local é idêntica ao dashboard remoto atual**. Marcar a conferência como pendência obrigatória imediatamente antes de uma futura alteração.

## 6. Matriz final: sete linhas e decisões acionáveis

Monte uma matriz com **EXATAMENTE sete linhas principais**, uma por linha incluída (detalhes de widgets podem ser expandidos em subtabelas):

| Linha | Requisito original | Cobertura do objetivo de negócio | Cobertura operacional por widget(s) | Evidência UAT | Gap objetivo | Ação necessária | Bloqueio | Próximo passo |
|---|---|---|---|---|---|---|---|---|

Ação por widget/subfluxo deve ser uma de:
- **MANTER**
- **AJUSTAR**
- **CRIAR**
- **REMOVER**
- **VALIDAR QUERY/TELEMETRIA**
- **INSTRUMENTAR (PROPOSTA, NÃO EXECUTAR)**
- **DEPENDÊNCIA EXTERNA (DOCUMENTAR E ENCERRAR NO NOSSO ESCOPO)**

**Regra de encerramento por requisito:** “concluído” só quando a cobertura adotada for tecnicamente comprovada, aprovada pelo responsável e registrada como **integral, parcial ou não implementável**, com a justificativa e a lacuna do objetivo original. Não declarar integral algo parcial.

## 7. Fechar o backlog em vez de iniciar novas auditorias

Faça o plano com o **menor conjunto de mudanças que efetivamente melhora a cobertura dos sete indicadores**.

### A. Entregas já atendidas ou consolidadas
Indique o que permanece **MANTER**, quais validações humanas já foram confirmadas e quais pendências residuais não bloqueiam a conclusão do recorte operacional.

### B. Ajustes/criações com dados já conhecidos
Liste somente mudanças candidatas fundamentadas. Para cada item:
- Linha da planilha e objetivo que ele atende;
- Widget existente ou **novo**, sem inventar identificadores;
- Ação (ajustar/criar/remover);
- Métrica, evento ou log e unidade contável;
- Ganho de cobertura e razão de não duplicação;
- Necessidade de validação de query e existência de tráfego;
- Dependências reais;
- Critério de aceite na UI;
- Efeito sobre outros indicadores e risco de regressão.

### C. Linhas cinzas (17 e 18) — decisão delimitada
Use os achados já existentes e **somente verificações pontuais** para determinar se é possível comparar os sinais requeridos, incluindo chaves, consistência temporal e fronteiras dos sistemas.

Se o cruzamento exige fonte transacional externa não observável de modo confiável, classifique a dependência e a cobertura possível; **não abra uma investigação interminável**. Registre exatamente qual informação falta e onde teria de ser obtida.

### D. Pendências que não devem bloquear o dashboard
Distinguir pendências de **qualidade futura / evolução de instrumentação** de pendências realmente impeditivas para publicar um widget. Não adiar entregas úteis esperando dados que não são necessários para a semântica adotada.

## 8. Cronograma técnico de execução — pequenos pacotes autorizáveis

Proponha uma sequência curta de etapas subsequentes, agrupando alterações por risco e dependência, sem repetir todo o histórico.

**Pacote 1 — Ajustes simples e fundamentados:** correções mínimas de widgets com sinal comprovado, evitando mudanças colaterais.

**Pacote 2 — Lacunas em indicadores verdes:** criação/ajuste de widgets realmente necessários quando dados existem, com consultas e critérios de aceite.

**Pacote 3 — Conclusões dos indicadores cinzas:** implantar a parte demonstrável ou registrar impossibilidade/dependência com critério objetivo de encerramento.

**Pacote 4 — Consolidação final:** revisão dos títulos, descrições, grupos, duplicações, matriz de aderência e registro de cobertura final de cada linha.

Cada pacote deve indicar **entradas, mudanças propostas, testes/validação local, bloqueios, aprovação necessária e definição de pronto**. Reorganize/mescle pacotes se a evidência recomendar, mas não criar uma longa série de prompts para tarefas triviais.

A **construção local**, a **publicação remota** e a **verificação pós-publicação** devem ocorrer sob autorizações separadas:

1. **Antes de construir:** revisão humana da lista precisa de mudanças.
2. **Antes de publicar:** obter export/snapshot remoto atualizado, proteger backup e mudanças manuais, preparar candidato local, demonstrar diff semântico, verificar dry-run e testes, e solicitar **autorização explícita**.
3. **Depois da publicação autorizada:** conferir o dashboard remoto e registrar validação real.

**Esta etapa 05D NÃO autoriza executar nenhum desses três passos.**

## 9. Relatório local único e conciso

Criar somente em **darwin-monitor/planos/** um Markdown de planejamento com nome sugerido:

**25-escopo-final-plano-conclusao-darwin-monitor.md**

Se já existir, escolher outro nome e não sobrescrever.

O relatório deve ser **conclusivo, rastreável e enxuto** (evitar relatório enciclopédico):
1. Escopo congelado: as sete linhas selecionadas, cores e seis exclusões.
2. Matriz de aderência das sete linhas com referência à planilha.
3. Cobertura por widget **e por combinações**; objetivo integral versus recorte operacional.
4. Backlog **fechado e ordenado**, com MANTER/AJUSTAR/CRIAR/REMOVER/VALIDAR/INSTRUMENTAR/DEPENDÊNCIA e critérios de aceite.
5. Indicadores já encerrados e itens que **não devem ser retrabalhados**.
6. Decisão objetiva e condicionantes das duas linhas cinzas.
7. Plano de execução em pequenos pacotes até a validação em UAT.
8. Bloqueios reais versus pendências não impeditivas.
9. Critérios finais para encerrar a entrega dos indicadores.
10. Arquivos criados/alterados, limitações e confirmação de ausência de mudanças remotas.

Não copiar para o relatório grandes trechos dos relatórios anteriores; apontar os caminhos locais e resumir apenas as decisões que governam o plano.

## 10. Validações finais e PARADA

Antes de concluir, confirme:
- [ ] Apenas as linhas **2, 11, 13, 14, 17, 18 e 19** entraram no plano de trabalho.
- [ ] Linhas **4, 8, 9, 10, 12 e 15** foram excluídas sem novas investigações nem propostas de widgets específicos.
- [ ] Os objetivos da planilha prevaleceram sobre nomes e quantidade de widgets.
- [ ] Combinações de widgets foram avaliadas sem rotular indevidamente cobertura integral.
- [ ] As duas linhas cinzas receberam uma decisão/pendência finita e verificável.
- [ ] Toda alteração futura possui justificativa, evidência, critério de teste e ganho de cobertura.
- [ ] Os indicadores concluídos anteriormente não foram modificados.
- [ ] Só um novo Markdown local foi criado e relido.
- [ ] Nenhum Java, teste, JSON, script, Git remoto, pipeline, deploy ou recurso Datadog sofreu alteração.

**Resumo final no terminal, curto e operacional:**
1. Sete linhas em escopo e estado atual de cada uma;
2. Quantos widgets propõe MANTER / AJUSTAR / CRIAR / REMOVER (números reais, não inventados);
3. Primeiro pacote executável, respectivas dependências e critério de aceite;
4. Pendências impeditivas x não impeditivas;
5. Caminho do novo relatório;
6. Lista exata de arquivos alterados e confirmação de **nenhuma escrita remota**.

**PARE após a entrega do plano. Aguarde revisão e autorização humana para iniciar a construção.**
