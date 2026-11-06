# PROMPT 05E — Construção Local de Widgets Candidatos e Validação Delegada ao Operador

> **Execução:** nova sessão do Gemini CLI, dentro do workspace corporativo autorizado.
>
> **Repositório público:** este arquivo contém apenas instruções metodológicas. NÃO enviar a este repositório planilhas, relatórios, código, nomes internos de serviços, IDs de widgets, consultas literais, payloads, métricas ou evidências do ambiente corporativo.
>
> **Autorização nesta etapa:** construir/editar SOMENTE artefatos CANDIDATOS locais, separados dos canônicos e do dashboard remoto. Consultas remotas SOMENTE DE LEITURA, quando já autorizadas. **NÃO publicar no Datadog, NÃO alterar o JSON canônico e NÃO modificar aplicações, scripts ou outros widgets existentes.**

## 1. Missão e mudança metodológica

Atue como engenheiro de observabilidade que prepara **artefatos locais prontos para revisão humana**, não como publicador.

A equipe decidiu eliminar um bloqueio artificial: **não é necessário que o Gemini execute consultas no Datadog Log Explorer para preparar a estrutura local dos widgets**. Quem executa e comprova consultas no Log Explorer é o **operador humano**.

Diferencie rigorosamente:
1. **QUERY CANDIDATA FUNDAMENTADA:** construída a partir de código e/ou configuração existente; ainda não comprovada no Log Explorer.
2. **QUERY VALIDADA PELO OPERADOR:** executada pelo responsável no Log Explorer, com evidência comunicada explicitamente (captura, quantidade de resultados, período e filtro).
3. **SEM EVIDÊNCIA NA JANELA:** consulta executada mas sem eventos; não significa query incorreta ou fluxo inexistente.
4. **QUERY REPROVADA:** falha de sintaxe, mistura de fluxos, ausência de atributo esperado ou semântica não sustentada por evidência.

**A ausência de confirmação de logs NÃO bloqueia a preparação de um JSON candidato local. Ela BLOQUEIA a declaração de validação em UAT e a autorização de publicação do widget que dependa desses logs.**

Não tente novamente superar limitações da API de logs por credenciais, cookies ou caminhos alternativos. Não gaste contexto com falhas repetidas de autenticação; disponibilize ao responsável um checklist enxuto de execução manual.

## 2. Escopo fechado — sem novas auditorias gerais

Use como fonte principal o **Relatório 25** do plano local (prefixo 25- em darwin-monitor/planos/) e consulte pontualmente os relatórios anteriores 22–24 apenas se necessário.

Trabalhar exclusivamente com os **sete registros selecionados** da planilha mestre:
- **Verdes:** linhas **2, 11, 13, 14 e 19**;
- **Cinzas:** linhas **17 e 18**;
- **Amarelos fora do escopo:** linhas **4, 8, 9, 10, 12 e 15**.

A planilha — não o nome dos widgets existentes — determina a cobertura exigida. Os amarelos **não devem ser investigados nem motivar criação de widgets**. Não remover widgets compartilhados com outros objetivos somente porque estejam relacionados a itens fora do escopo.

IND-01, IND-02 e IND-07 possuem trabalho previamente consolidado; **preservar**. Trabalhar apenas nas lacunas efetivamente identificadas nos sete registros.

**Combinações de widgets são permitidas** quando oferecem uma observabilidade complementar e explicável. Elas não viram automaticamente cobertura integral de uma fórmula ou desfecho de negócio não demonstrado.

O relatório 25 apontou preliminarmente **um ajuste de widget e nenhuma criação/removal**. Não mude isso apenas para produzir mais widgets; entretanto, se uma lacuna real e viável demonstrar necessidade de um widget novo, **construa seu protótipo local separado**, documente a evidência e encaminhe para revisão humana.

## 3. Leitura seletiva e preservação da verdade operacional

1. Localize o relatório 25 e leia o backlog, a matriz de aderência e os critérios de aceite.
2. Confirme os requisitos originais das sete linhas na planilha e observe eventuais observações relevantes, sem reabrir os amarelos.
3. Inspecione o dashboard JSON local e a configuração **remota em modo leitura se possível**, registrando a origem e data de cada snapshot. **Não presumir identidade entre remoto e arquivo canônico antigo.**
4. Confirme a configuração do widget alvo (ID real, grupo, query, agregação, título, descrição, variáveis/template) e a origem de cada sinal em código.
5. Inspecione apenas o código necessário para confirmar logs/prefixos/campos. Não reexecutar auditorias extensas nem varreduras indiscriminadas.
6. Verifique se há código/testes/scripts de **validação local** já existentes; use-os somente após confirmar que não escrevem em arquivos canônicos nem realizam PUT/POST.

Se não for possível obter snapshot remoto, prossiga com o candidato baseado na cópia local, mas sinalize **RECONCILIAÇÃO REMOTA PENDENTE**: esse candidato não pode ser promovido/publicado sem comparação posterior com export remoto atual.

## 4. Primeiro artefato: ajuste operacional de cancelamento

Preparar **localmente** a versão candidata do widget existente identificado no relatório 25 para separar:
- ocorrências de **esgotamento das tentativas de cancelamento** de uma rota específica;
- ocorrências de **estorno/restituição financeira**, que não podem compor o mesmo sinal.

Preservar identidade, tipo, grupo, layout, agregação e demais configurações que não precisem mudar. Mudar somente o mínimo tecnicamente necessário: **filtro/query, título e descrição**.

A descrição deve afirmar apenas a unidade comprovável: por exemplo, **ocorrências de emissão do sinal de esgotamento com acionamento do caminho de erro/DLQ**, não “propostas únicas definitivamente não canceladas” e nem “mensagens efetivamente entregues à DLQ” caso o log ocorra antes dessa confirmação.

Verifique que a query candidata:
- usa termos literais efetivamente encontrados no código;
- restringe serviço, ambiente, caminho e fluxo com a maior precisão disponível;
- evita misturar cancelamento e estorno;
- mantém variáveis de template pertinentes;
- não considera contagem de hits HTTP equivalente a falhas terminais nem uma linha de log igual a um negócio único.

**Não afirme eliminação de 100% de falsos positivos sem consulta real comprobatória.** A classificação atual deve ser **QUERY CANDIDATA FUNDAMENTADA — VALIDAÇÃO HUMANA PENDENTE**, se ainda não houver comprovação visual.

## 5. Novos widgets: somente quando uma lacuna real justifica

Depois de preparar o ajuste principal, examine o backlog dos sete indicadores e identifique se existe **nova visualização viável com base em sinais reais**, além das já existentes.

Para cada possível novo widget, documente:
- linha/requisito da planilha e objetivo a atender;
- pergunta operacional específica;
- campo, evento, métrica e unidade contabilizável;
- por que os widgets atuais, isolados ou combinados, não bastam;
- cardinalidade, risco de duplicação e diferença entre tentativa, mensagem, contrato e conclusão;
- query candidata e seu estado de validação;
- critérios objetivos para aceitar ou rejeitar a criação.

**Se houver uma proposta demonstravelmente útil mas ainda sem validação UAT, produza seu protótipo local de modo separado e identificável como NÃO PUBLICÁVEL**, sem integrá-lo automaticamente ao dashboard candidato principal. Se não existir fundamento suficiente, não o construa: documente a lacuna.

NÃO inventar métricas, eventos, campos, correspondências Charge ID, taxas percentuais, cálculos de datas ou condições de cobrança indevida. Onde o indicador exige informação não observável, documente a dependência em vez de fabricar a aparência de cobertura.

Em especial, uma trilha de postagens Kafka não demonstra automaticamente a **comparação efetiva** de identificadores. Caso um requisito dependa de verificação de igualdade/divergência, avalie se há sinal observável real de resultado da comparação. Se não houver, mantenha status parcial/não comprovado e proponha instrumentação futura sem alterar código.

## 6. Artefatos locais — sem tocar no canônico

Crie os artefatos somente no workspace corporativo, em área de **candidatos/drafts** já existente; se não existir, pode criar um subdiretório específico do Darwin Monitor para esses **novos arquivos**, sem alterar os anteriores.

Entregas:
1. **JSON candidato completo local**, baseado no snapshot confiável disponível, preservando todos os widgets não aprovados para alteração; se snapshot remoto não disponível, marcar a proveniência LOCAL/DESATUALIZAÇÃO POSSÍVEL no relatório.
2. **Protótipos de novos widgets, se justificados**, em um arquivo JSON separado, explicitamente **NÃO PUBLICÁVEL** até validação e aprovação.
3. **Relatório Markdown local** de execução da etapa 05E em darwin-monitor/planos/, com prefixo **26-**, incluindo matriz de alterações e queries para validação humana.

**PROIBIDO:**
- sobrescrever o JSON canônico, export, backups prévios ou relatórios 22–25;
- substituir ou editar o script de publicação;
- alterar Java, testes, configuração, credenciais ou ambiente remoto;
- commits, push, PR, pipeline ou deploy.

Se os nomes sugeridos já existirem, escolha nomes novos e registre os caminhos efetivos. Não sobrescreva artefato sem revisão explícita.

## 7. Testes locais e diff semântico

Execute validações **sem efeitos remotos**:
- JSON bem formado;
- estrutura e schema preservados conforme suporte real do dashboard;
- IDs existentes intactos; não duplicar IDs;
- contagem, ordem, grupo/layout e demais propriedades dos widgets preservados, exceto alterações explicitamente propostas;
- diff semântico de cada mudança: **antes/depois**, path e justificativa;
- nenhuma exclusão acidental de alteração manual remota conhecida;
- existência de todas as variáveis de template utilizadas;
- nenhuma mudança em indicadores previamente validados;
- protótipos não publicados separados do candidato principal.

Para qualquer validador/dry-run, confirme ANTES que é realmente seguro e não faz escrita em API ou no canônico. Se houver dúvida, **não execute**; descreva como validar manualmente.

Apresente **a quantidade real** de widgets preservados, ajustados e propostos — não force valores históricos se o snapshot atual divergir.

## 8. Pacote de validação a cargo do responsável humano

Não tente executar queries de logs via API. Gere **no máximo cinco consultas prioritárias**, preferencialmente começando pelas duas que diferenciam **esgotamento de cancelamento** e **esgotamento de estorno**, extraídas/confirmadas a partir do código local.

Para cada consulta, entregar em formato copiável:
- nome e objetivo;
- query LITERAL completa, sem placeholders, com os nomes reais de serviços/ambiente que existirem no workspace corporativo;
- período recomendado (considerar data de deploy e tráfego disponível);
- quais campos/mensagens devem aparecer, como o identificador estruturado quando aplicável;
- o que comprova inclusão, exclusão, duplicação e correlação;
- o que significa zero registros e como registrar a inconclusão;
- tipo de evidência que o operador deve retornar (print, resultado resumido, janela).

**Nunca registrar evidência como validada somente porque a query foi preparada ou há métricas APM correlatas.** A validação será feita posteriormente pelo usuário, que enviará resultados.

**Essa validação não é bloqueio para concluir os arquivos CANDIDATOS locais. É bloqueio para promoção do widget à condição de validado/publicável quando depender de logs.**

## 9. Relatório de execução local (prefixo 26-)

O relatório deve conter:
1. Objetivo e escopo congelado (7 linhas; 6 amarelas excluídas).
2. Fontes efetivamente lidas e grau de atualidade do snapshot.
3. Widget operacional a ajustar: ID, grupo, antes/depois, semântica, riscos e status da query.
4. Outros widgets/protótipos: justificativa e estado, ou razão de não criá-los.
5. Inventário real de widgets preservados e checagem de regressão.
6. Diff semântico local e resultados dos testes seguros.
7. Queries para execução humana no Log Explorer, com roteiro.
8. Matriz atualizada de aderência aos 7 indicadores: não declarar atendimento integral sem evidências.
9. Bloqueios reais **para publicação**, separados de pendências **para construção local**.
10. Caminhos exatos dos arquivos criados/alterados e confirmação de **nenhuma alteração no canônico ou Datadog remoto**.

## 10. Resumo final e parada

Apresente no terminal:
- Quantidade de widgets existentes preservados, ajustados no candidato e novos protótipos;
- Caminhos dos JSONs candidatos e relatório 26;
- Status de cada consulta (candidata, validada pelo operador, sem evidência, reprovada);
- Até cinco queries prioritárias para o humano executar, apontando também o relatório onde estão copiáveis;
- O que falta para promover o candidato ao próximo gate;
- Lista exata de arquivos criados/alterados;
- Declaração de **nenhuma escrita no Datadog e nenhum código, canônico ou script alterado**.

**PARE após a construção e documentação dos CANDIDATOS LOCAIS.**

**Não publicar, não executar PUT/POST, não iniciar a etapa de deploy ou fazer modificações remotas.** A revisão humana do diff e das evidências do Log Explorer é obrigatória antes de considerar publicação.
