# PROMPT 06E — Homologar a Decisão P0 e Aprovar o Desenho P1 com Validação Humana de Viabilidade

> **Executor:** Gemini CLI, exclusivamente no workspace corporativo autorizado.
>
> **Segurança do repositório público:** este prompt publica APENAS método e orientações genéricas. Nomes internos de serviços, atributos, logs, consultas literais, fontes, números/IDs de widgets e dashboards, URLs corporativas, planilhas, relatórios privados e evidências permanecem SOMENTE no workspace corporativo. Não copiá-los para este repositório ou para mensagens externas.
>
> **AUTORIZAÇÃO EXPRESSA DO RESPONSÁVEL NESTA ETAPA:** (1) homologar a **DECISÃO DE REJEITAR os filtros P0 originais**, conforme a revisão crítica do relatório privado da etapa 06D; (2) aprovar **somente o DESENHO** das duas propostas P1: ranking de propostas com maior atrito/retentativas e stream especializado adicional com chave estruturada, preservando o stream original. **Não é autorização para implementar P0, construir widgets P1, editar JSON, publicar dashboards, mudar código ou criar recursos remotos.**
>
> **Contexto:** o diagnóstico anterior identificou que a instrumentação de correlação por proposta foi implantada, mas pouco aproveitada analiticamente. A revisão crítica concluiu que filtros de presença do identificador, aplicados indiscriminadamente a métricas operacionais, descartes ou ao stream global, podem ocultar justamente eventos problemáticos. As duas P1 são evoluções laterais e não destrutivas, mas sua viabilidade na organização precisa de confirmação pontual.

## 1. Fonte de verdade e escopo

Consultar **seletivamente** o relatório privado mais recente de revisão de viabilidade e seus antecedentes estritamente necessários: diagnóstico anterior, decisões registradas, JSON canônico UAT, último candidato/export confiável de PROD e evidências existentes de logs. Conferir os nomes reais no workspace; não presumir que o conteúdo das capturas ou declarações substitui a análise dos artefatos.

**Entregável:** um adendo de homologação e especificação das duas P1, com roteiro objetivo para o responsável validá-las manualmente pelo Datadog, sem criar nem alterar nada durante esta etapa. Oportunidades P2 ficam registradas como backlog **não autorizado**.

**Importante:** registrar que a classificação original `P0` descrevia alterações urgentes propostas, agora formalmente **REJEITADAS**. A "aprovação do P0" solicitada pelo responsável significa **aprovação do VETO aos três filtros P0**, não aprovação de sua aplicação.

## 2. Homologação de segurança P0 — decisão definitiva desta etapa

Localizar e citar no relatório privado as três propostas anteriores e a revisão que as rejeitou. Registrar a decisão do responsável:

1. **Stream global de logs:** NÃO restringir por presença da chave estruturada. Preservar mensagens malformadas, eventos com falha de extração e qualquer log pertinente.
2. **Descarte/falha de cancelamento e DLQ:** NÃO exigir chave estruturada como condição para contar ou exibir descarte. Falhas sem identificador são sinais relevantes, não ruído descartável.
3. **Volumetria de início/sucesso local:** NÃO inserir filtro obrigatório da chave que possa subcontar o processamento bruto ou distorcer comparações.

Aprovação do veto significa **zero alterações nos widgets já existentes**. Não retomar essas propostas como P0 em futuras implementações sem evidência nova, revisão humana e autorização específica. Explicar por que preservar indicadores de volume e o stream original mantém a honestidade operacional.

## 3. Aprovação conceitual P1-A — ranking de propostas com maior atrito

Elaborar a **especificação funcional e técnica pronta para aprovação de construção futura**, sem criar o widget:

- Objetivo: mostrar quais propostas com chave estruturada acumulam **mais ocorrências observáveis de retentativa/falha**, como ferramenta de triagem operacional e NÃO como taxa executiva ou prova de desfecho final.
- Fonte: os sinais de falha e retentativa **efetivamente usados no dashboard** e no relatório de revisão; recuperar suas queries e filtros de contexto reais apenas nos arquivos privados.
- Agrupamento: identificador estruturado de proposta, com ordenação decrescente pelo número de ocorrências e limite inicial de top 10 ou top 20, conforme capacidade real.
- Unidade: **ocorrências de log por proposta**, não propostas únicas concluídas, nem percentual de sucesso/falha.
- A interface pode excluir eventos sem identificador **somente nesta visão especializada**, com texto explicativo de que o dashboard principal mantém esses eventos na volumetria bruta.
- Definir título, descrição, período, index/storage, fontes, filtros, agrupamento, limite, layout sugerido e possível relação com o indicador de reprocessamento, sem alterar a definição desse indicador.
- Identificar se a chave existe como atributo pesquisável E agrupável/facet configurada. "Campo aparece no log" não prova automaticamente que ele seja selecionável como dimensão de Top List.
- Verificar viabilidade na edição atual do Datadog, inclusive Flex/Standard e permissões. Se não comprovado no workspace, registrar **DESENHO APROVADO; VIABILIDADE NA UI PENDENTE**, não afirmar homologação técnica.
- Descrever o cenário negativo: log de falha sem chave continua visível nos widgets originais e não entra no ranking especializado, de maneira explícita.

## 4. Aprovação conceitual P1-B — stream adicional especializado

Elaborar especificação completa de uma visão secundária orientada a propostas com identificador:

- Preservar o stream original em seu estado atual, **sem editar sua query, colunas, filtros ou alcance**.
- Projetar um segundo stream lateral ou no mesmo grupo do original, com pesquisa restrita a logs que tenham chave estruturada, colunas e ordenação adequadas para investigação, sem deduplicação artificial.
- Distinguir a função das duas visões: (A) **visão bruta global** para capturar inclusive falhas de extração; (B) **visão especializada por proposta** para acelerar a investigação.
- Definir título, descrição com ressalva de cobertura, fonte e tipo de widget, query privada candidata, índice/storage, colunas configuráveis, ordenação, timeframe e posicionamento planejado.
- Confirmar que o tipo de widget disponível suporta o filtro, as colunas e a consulta escolhidos. Não confundir campos de stream de logs com agregação de Top List.
- Sem adicionar ou duplicar widgets na UAT/PROD nesta etapa.

## 5. Validação humana indispensável de viabilidade — sem gravação no Datadog

A automação pode estar impedida de executar consultas reais, portanto **PREPARAR UM CHECKLIST OPERACIONAL CURTO** que o responsável realizará manualmente com seu login corporativo autorizado. Usar o Log Explorer para testes de leitura, não o editor do dashboard; UAT preferencialmente, PROD apenas para consultas autorizadas de leitura quando UAT não tiver amostra.

**Teste A — Top List / agrupamento:**
1. Entregar ao responsável a **query candidata exata** com os filtros de serviço, ambiente, índice/storage e termos de retentativa/falha recuperados dos widgets existentes, além do intervalo temporal proposto. Não inventar nomes de index ou valores.
2. No Log Explorer, abrir a consulta, confirmar registros pertinentes e se a chave estruturada está disponível na interface como facet/dimensão de agrupamento.
3. Tentar visualização de ranking/Top Values por chave, ordenada por contagem de eventos, limitada a 10 ou 20 entradas. Registrar se o caminho de visualização foi suportado e se os números representam ocorrências reais, não população de propostas distintas.
4. Se o atributo não for agrupável por falta de facet/permissão, registrar o bloqueio. **Não cadastrar facet nem alterar configuração global sem autorização.**
5. Entregar ao operador um modelo de retorno: `ambiente, período, fonte/index, dados encontrados? (S/N), agrupamento disponível? (S/N), ranking apresentado? (S/N), restrições/erro, captura sem dados sensíveis`.

**Teste B — stream especializado:**
1. Fornecer query candidata exata para logs com chave estruturada a partir dos sinais documentados.
2. No Log Explorer, testar se os logs são retornados e se o campo pode ser exibido em coluna; registrar a diferença entre log com chave e log sem chave sem modificar o stream original.
3. Registrar `ambiente, período, registros encontrados? (S/N), coluna disponível? (S/N), filtro funciona? (S/N), restrições/erro`.
4. Se não houver logs em UAT no intervalo, não inferir falha do widget. Testar período adequado ou solicitar **apenas leitura autorizada** em PROD.

**Não exigir que o responsável:**
- forneça cookies, API Keys, credenciais ou IDs de clientes;
- crie um dashboard temporário ou salve widgets para testar;
- faça publicação, PUT, deploy, mudança de facet ou intervenção administrativa;
- exporte logs contendo propostas reais em repositório público.

A falta de dados ou de permissão deve constar como limitação, sem fabricar prova de sucesso. Se a UI permitir apenas validar parcialmente uma funcionalidade, classificar por item: `COMPROVADO`, `PARCIAL`, `NÃO COMPROVADO`, `BLOQUEADO`.

**Teste opcional, independente e NÃO BLOQUEANTE:** caso a interface já disponibilize contagem distinta por atributo nos logs, documentar separadamente a possibilidade de experimentar a operação em uma janela fixa. Não transformar isso em aprovação de taxas executivas, não somar contagens únicas por buckets para inferir entidades únicas totais, não incluir chave individual como tag de métrica e não alterar a decisão P2 já registrada.

## 6. Especificação e aceite das P1

Para cada P1, criar matriz no relatório:
`finalidade | widget original preservado | novo widget proposto | query/agrupamento conceitual | fonte/index/storage | campo necessário | vantagem operacional | limitação | risco | teste humano | resultado disponível | condição de implementação futura`.

Aceites futuros mínimos:
- nenhuma regressão no número/semântica dos indicadores originais;
- stream original permanece amplo e preservado;
- volumes absolutos de início, falha e DLQ não sofrem filtros por chave;
- novo ranking não é descrito como contador de propostas únicas nem sucesso final;
- novo stream é claramente especializado e não substitui a visão global;
- sem dados suficientes, não declarar a visualização comprovadamente operacional;
- autorização posterior necessária para editar canônico, gerar candidato, alterar widget, realizar importação/manual ou publicar UAT/PROD.

Registrar as melhorias P2 do parecer anterior apenas como **planejamento pendente**, sem implementá-las ou apresentá-las como derivação obrigatória deste aceite.

## 7. Entrega privada e parada

Produzir **um único relatório Markdown privado**, próximo número livre na pasta local de planos, sugestão:
`39-homologacao-p0-aprovacao-desenho-p1-validacao-ui.md`.

Conteúdo obrigatório:
1. Decisão expressa: **P0 ORIGINAIS REJEITADOS, VETO HOMOLOGADO**;
2. Duas especificações P1 aprovadas em nível de desenho;
3. Matriz de risco e preservação dos widgets originais;
4. Queries privadas candidatas, prontas para o operador copiar no Log Explorer, com parâmetros realmente conferidos;
5. Checklist humano resumido (Teste A + Teste B) e modelo de resposta;
6. Evidências já disponíveis x dependências da validação humana;
7. Critérios de aceite para eventual implementação futura;
8. Registro de P2 como não autorizado;
9. Declaração de integridade: nenhuma alteração de dashboards, código, JSON canônico, plugins, scripts ou recursos remotos.

Na resposta ao operador, **não confundir "desenho P1 aprovado" com "widget testado" ou "implementação autorizada"**. Apresentar em poucas linhas o que foi homologado, as duas consultas para ele testar (ou apontar ao relatório privado), os resultados que ainda faltam e o caminho do arquivo.

**PARE após a especificação. Não implementar, não editar JSON, não salvar widgets, não publicar Datadog nem fazer commits ou deploys. Aguarde validação humana da UI e autorização explícita para futura construção.**
