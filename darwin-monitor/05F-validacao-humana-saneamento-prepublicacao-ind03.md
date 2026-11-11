# PROMPT 05F — Incorporação da Validação Humana, Saneamento Local e Pré-publicação Segura

> **Executor:** Gemini CLI, no workspace corporativo autorizado.
>
> **Repositório público:** este prompt contém somente orientações metodológicas. Nunca copiar para este repositório logs, consultas literais, marcadores internos, nomes de serviços, campos/IDs internos, JSONs, relatórios, planilhas, código, métricas, capturas, credenciais ou dados de clientes.
>
> **Autorização:** leitura dos recursos autorizados e criação/ajuste de **artefatos locais candidatos**. **NENHUMA publicação no Datadog, nenhuma escrita remota, nenhuma alteração do dashboard canônico, Java, testes, scripts, infraestrutura ou configuração da aplicação.** Não criar commit, push, PR ou deploy.

## 1. Contexto já decidido — não repetir validações

As etapas 05A–05E estão concluídas; o Relatório local de prefixo `26-` documenta os candidatos construídos na 05E. Continue a partir deles, sem reabrir auditorias, sem varreduras amplas de código e sem solicitar ao Gemini execução de consultas no Log Explorer.

**Decisão expressa do responsável em 11/11/2026:**
- A consulta **Q1**, de esgotamento de tentativas de cancelamento, foi executada em UAT e **validada pelo operador**: retornou registros, inclusive recentes, com campo estruturado de correlação de proposta.
- A consulta **Q2**, de **esgotamento de estorno**, teve um **marcador textual incorreto na formulação antiga**. O responsável identificou no código o marcador correto para o fluxo e executou a consulta corrigida em UAT; ela também **retornou registros recentes e campo de correlação**. **NÃO reutilizar o marcador antigo.**
- Não exigir prints adicionais, não reexecutar estas consultas, não reclassificar automaticamente as duas como pendentes. Registrar a **validação declarada pelo operador**, e não como evidência observada diretamente pelo Gemini.
- Não foram fornecidas contagens exatas, série temporal, análise de duplicidade nem confirmação de entrega final à DLQ. **Não inventar esses dados**. Validação de existência/filtro/campo não comprova propostas únicas nem desfecho definitivo de negócio.

O repositório público propositalmente **não contém os marcadores literais**. Para Q1 e Q2, localizar o valor correto no **código e nos arquivos privados locais**, conferindo exatamente a grafia em uso. A Q2 deve usar a variante efetivamente presente no código e validada pelo operador, não a variante anterior do relatório. Se a ambiguidade persistir, sinalizar somente o ponto concreto; não reconstruir um marcador por palpite.

## 2. Escopo e controles permanentes

Fonte de verdade: planilha mestre no workspace privado, aba de monitoramento. Somente **sete linhas**: verdes **2, 11, 13, 14 e 19**; cinzas **17 e 18**. Permanecem excluídas as linhas **4, 8, 9, 10, 12 e 15**, que não devem motivar investigações nem novos widgets.

A cobertura operacional por sinais ou conjunto de widgets pode ser **parcial**, sem comprovar o objetivo integral de negócio. Preservar indicadores anteriormente publicados/validados. Não propor percentual sobre universos de eventos incompatíveis; não confundir tentativas HTTP, registros de log, DLQ, cobranças, propostas e conclusões.

Prioridade desta etapa: **IND-03**, incluindo separação entre cancelamento e estorno. As demais seis linhas entram apenas na matriz final de aderência resumida; não abrir novas frentes.

## 3. Insumos locais — leitura seletiva

1. Ler o **Relatório 26** e os dois JSONs produzidos na 05E: o dashboard candidato principal e o arquivo **separado de protótipos não publicáveis**. Descobrir os nomes e caminhos reais no workspace.
2. Consultar o Relatório 25 somente nos critérios de aceite, preservação e lacunas relevantes. Relatórios anteriores, apenas se surgir uma contradição pontual.
3. Inspecionar no código apenas os literais de log e os campos necessários para confirmar **Q1 versus Q2**, sem alterar código ou instrumentação.
4. Confirmar o alcance do candidato principal: foi reportado **um widget ajustado e nenhum widget novo integrado** em um conjunto de 19; verificar a quantidade real em vez de assumir imutabilidade do ambiente.

## 4. Saneamento dos candidatos e da documentação

- Registrar a validação humana de **Q1 e Q2** com data, escopo observado e limites da evidência.
- Corrigir a formulação **Q2** onde ainda esteja copiada como query de controle nos **novos artefatos locais**, usando o marcador literal correto extraído do código privado. Não reaproveitar expressão não existente na aplicação.
- Q2 é uma **consulta de controle de outro fluxo**: **não** adicioná-la automaticamente ao widget principal de esgotamento de cancelamento e **não** fundir suas contagens com Q1.
- Confirmar se o **widget principal já filtra corretamente Q1** e exclui o fluxo de estorno. **Se estiver correto, não mexer nele por conveniência**.
- Ajustar um JSON candidato **somente se houver falha objetiva no filtro, título ou descrição**. Se necessário, gerar **nova versão local identificável**, preservando o candidato e os relatórios anteriores como histórico, sem sobrescrita silenciosa.
- Descrever o evento somente como sinal operacional comprovável, sem prometer entrega confirmada à DLQ, contrato único, nem cobrança indevida após cancelamento.

## 5. Decisão sobre os dois protótipos isolados

Avaliar separadamente o protótipo de **retentativas** e o de **sucesso local**, usando código e evidência disponível, sem inventar validação humana adicional.

Para cada um:
1. Qual pergunta operacional nova responde?
2. Há diferença útil em relação aos widgets já existentes?
3. Qual é a unidade contada e o risco de duplicidade?
4. A query/sinal está **validada pelo operador**, apenas fundamentada em código ou sem evidência?
5. Ele ajudaria a cobrir a linha selecionada, sem confundir retry com falha terminal ou sucesso local com desfecho de negócio?
6. Emitir decisão **RECOMENDAR PARA VALIDAÇÃO FUTURA**, **MANTER ISOLADO / ADIAR** ou **REJEITAR**, com justificativa sucinta.

**Não integrar nem publicar nenhum dos dois protótipos nesta etapa.** Se houver necessidade de nova consulta manual, apresentar apenas aquela estritamente necessária, como pendência futura, sem bloquear o fechamento do candidato principal já validado.

## 6. Snapshot remoto em leitura e reconciliação semântica

Realizar **somente GET/leitura autorizada** para obter o estado mais recente do dashboard UAT; registrar horário, origem e versão/identificador **apenas no relatório privado**. Salvar um **backup local novo e imutável** desse snapshot; nunca enviá-lo ao GitHub público.

Comparar semanticamente:
- **snapshot remoto atualizado × dashboard canônico local × candidato 05E/05F**;
- contagem e IDs, ordem, grupos, layout, configurações de queries, agregação, título, descrição, template variables, configurações gerais e alterações manuais remotas;
- preservação integral dos widgets não selecionados para alteração;
- mudanças **planejadas** versus **divergências inesperadas**.

Se a configuração remota divergir da base do candidato, **não aplicar o JSON antigo às cegas**. Apresentar as divergências e, somente se tecnicamente seguro, construir uma **cópia reconciliada local e separada** que incorpore todas as alterações remotas não relacionadas. Qualquer conflito que exija decisão humana deve permanecer bloqueado, sem sobrescrever o canônico nem o remoto.

Se o snapshot remoto não estiver acessível, marcar **BLOQUEIO PARA PUBLICAÇÃO: RECONCILIAÇÃO REMOTA PENDENTE**. Não alegar equivalência entre snapshot antigo e remoto atual.

## 7. Verificações e critérios de aceite

Antes de finalizar:
- Garantir JSON sintaticamente válido e estrutura compatível com o dashboard real.
- Verificar IDs únicos, widgets existentes íntegros, grupos/layout/ordem preservados, referências válidas e filtros/template variables existentes.
- Mostrar diff semântico detalhado de toda alteração proposta, inclusive em caso de **zero mudanças no JSON principal**.
- Confirmar que o filtro do cancelamento não agrega o fluxo de estorno; a Q2 corrigida deve servir como controle separado.
- Proteger alterações remotas mais recentes e evitar regressões de indicadores já validados.
- Verificar previamente se qualquer script de teste/dry-run é efetivamente **read-only**. Na dúvida, não executar.
- Distinguir **query validada pelo responsável** de **métrica contabilizada sem duplicidades**, que exigiria evidência adicional.
- Não reabrir pesquisa para as seis linhas excluídas.

## 8. Entrega: relatório privado da 05F

Criar em `darwin-monitor/planos/` um **novo Markdown local** com prefixo `27-`, por exemplo `27-validacao-humana-e-preflight-ind03.md`. Se já existir, escolher um nome livre sem sobrescrever. **Não alterar o histórico do Relatório 26**; indicar expressamente que a Q2 antiga foi substituída no registro da 05F.

O relatório 27 deve conter, objetivamente:
1. Decisões do operador em 11/11/2026 e limites da validação;
2. Q1 e Q2 **literais corretas**, somente no documento **privado**; estado do campo estruturado de correlação;
3. Estado final do widget principal e indicação se houve ou não mudança real no candidato;
4. Decisão justificada para **cada** um dos dois protótipos, sempre isolados;
5. Fonte/data do snapshot remoto, backup local e quadro de divergências;
6. Diff semântico **remoto × canônico × candidato**, com caminhos e resultado dos testes seguros;
7. Matriz concisa dos sete registros: objetivo de negócio × cobertura operacional × limitações (sem declarar cobertura integral indevida);
8. Bloqueios efetivos de publicação e pendências não impeditivas;
9. Lista exata de arquivos criados/modificados e confirmação de ausência de escrita remota ou alteração do canônico;
10. **Recomendação explícita de GO / NO-GO apenas para SUBMETER À AUTORIZAÇÃO HUMANA**, nunca autorização automática de publicação.

## 9. Resumo no terminal e parada obrigatória

Entregar ao responsável um resumo curto:
- Q1 e Q2: **validadas pelo operador**, com correção da Q2 registrada; não exigir nova rodada de prints;
- se o candidato principal mudou, e por quê;
- recomendações sobre os dois protótipos;
- números reais de widgets preservados/ajustados e divergências remotas;
- backup, candidato(s) e relatório 27 com caminhos exatos;
- bloqueios e decisões específicas ainda exigidas para publicar em UAT.

**PARE. Não publique em UAT nem produção. Não faça PUT/POST/PATCH/DELETE remoto, commit, push, PR, alteração de canônico, Java, testes ou scripts.** A publicação dependerá de revisão do pacote e de **nova autorização expressa e específica do responsável**.
