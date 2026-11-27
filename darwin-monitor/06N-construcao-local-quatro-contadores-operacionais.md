# PROMPT 06N — Construção Local de Quatro Contadores de Volumetria Operacional (sem fórmulas)

> **Executor:** Gemini CLI, no workspace corporativo autorizado.
>
> **SEGURANÇA DO REPOSITÓRIO PÚBLICO:** este documento contém somente metodologia. Não publicar aqui nomes internos de serviços/eventos/atributos, consultas e filtros corporativos, IDs/URLs de dashboards/widgets, logs, valores de negócio, código-fonte, planilhas ou artefatos JSON e relatórios privados. Todos os dados de execução e arquivos finais permanecem **somente no workspace corporativo local**.
>
> **APROVAÇÃO EXPRESSA DO RESPONSÁVEL:** o diagnóstico do relatório privado da etapa 06M (relatório técnico mais recente conhecido: 46) e a proposta de **quatro contadores individuais operacionais** estão aprovados em princípio, **sem cálculo percentual nesta etapa**. Autoriza-se construir **DOIS NOVOS JSONs CANDIDATOS LOCAIS** (UAT e PROD) com os quatro widgets novos, documentar o diff e preparar a homologação manual. **NÃO** autoriza importação/publicação/PUT/POST, edição remota, alteração de widgets existentes, código Java, instrumentação, métricas, monitores, testes de erro em produção, nem commit/push Git.
>
> **MUDANÇA DE INTERPRETAÇÃO EXPLICITAMENTE DETERMINADA PELO RESPONSÁVEL:** o monitor é uma ferramenta principalmente para **desenvolvedores detectarem erros técnicos, erros funcionais, problemas de fluxo, gargalos e saúde das aplicações na própria fronteira**. Se o componente encaminha uma solicitação ao parceiro e recebe uma resposta positiva, **o resultado LOCAL acompanhado é sucesso**, independentemente de atividades externas posteriores. Se recebe erro ou há erro local evidenciado, **é falha observável no componente**. Não exigir prova de liquidação final/efeito externo para reconhecer sucesso de handoff/retorno local. **Não** usar esse recorte para prometer sucesso fim a fim do negócio.
>
> **INTERPRETAÇÃO MATEMÁTICA PARA O FUTURO, NÃO PARA IMPLEMENTAR AGORA:** para uma MESMA população de operações e resultados finais locais mutuamente exclusivos e exaustivos, `total = sucesso + erro`, `sucesso = total - erro` e `erro = total - sucesso`. Evitar dupla contagem por retries, logs repetidos, falhas transitórias recuperadas, mensagens reentregues e operações ainda em andamento. Em fluxos assíncronos, números de entrada e resposta na mesma janela de relógio podem refletir populações temporais diferentes. **NÃO criar fórmulas no JSON nem renomear proxies como se essa identidade já estivesse validada.** Primeiro contar e observar as quatro séries/valores isolados; somente depois discutir fórmulas com o responsável.
>
> **CRITÉRIO DE SIMPLICIDADE:** não reabrir a discussão arquitetural sobre o resultado final dos parceiros, nem reiniciar o discovery. Usar as quatro queries candidatas existentes no relatório 46, confirmando a correspondência com as fontes e corrigindo apenas discrepâncias comprovadas. Não exigir alteração Java quando a telemetria já é suficiente. Não tratar mensagens, tentativas e propostas como sinônimos.

## 1. Entregável único de construção — 4 widgets, 2 ambientes

Partir de **fontes locais oficialmente reconciliadas após a última publicação** para cada ambiente. Último inventário conhecido: **21 widgets de dados por ambiente, quatro grupos**. Confirmar antes de editar candidatos; o objetivo esperado é **25 widgets de dados por ambiente** (21 anteriores intactos + 4 novos).

Construir quatro **query_value** / contadores escalares de volume, obedecendo ao desenho aprovado no relatório 46 e aos títulos honestos:

1. **W1.4 — Solicitações de Contratação Digital:** volume de eventos/solicitações digitais observáveis na entrada da esteira digital, no grupo de origem. **Unidade: eventos ou solicitações observadas**, conforme prova; não classificar automaticamente como todas as adesões concluídas do negócio.
2. **W2.9 — Mensagens Recebidas da Recorrência:** volume de **entradas de mensagens/eventos no fluxo de recorrência** observado no componente, grupo de processamento/sucesso. Não declarar automaticamente quantidade de recorrências únicas se houver reentrega.
3. **W2.10 — Sucessos Locais da Recorrência:** volume de **retornos positivos ou conclusões locais sustentadas pela fonte** ao encaminhar/processar recorrência. O responsável define esse retorno positivo dentro da fronteira como **sucesso do nosso componente**. Preservar a definição do evento exato e reconhecer que a saída para o parceiro não precisa significar liquidação externa.
4. **W2.11 — Falhas Funcionais da Recorrência:** volume de **eventos de falha funcional de negócio** realmente emitidos. Este contador é útil, mas **não deve ser apresentado como TODAS as falhas técnicas + funcionais** se a query só cobre uma categoria. Outros widgets antigos de erros técnicos e retentativas continuam complementares; não alterar sua cobertura.

**Usar os nomes exatos/taxonomia dos relatórios privados**, mesmo se divergem das etiquetas conceituais acima; documentar fielmente qualquer adaptação. Se o relatório 46 associou o quarto componente de `total de adesões` a uma fonte de contratação digital, manter essa distinção: **ele não é automaticamente o total correspondente às falhas de recorrência**. O objetivo presente é visibilidade de quatro volumes, não fingir a construção de uma taxa executiva com universos incompatíveis.

## 2. Verificação limitada da evidência antes da geração

Ler seletivamente:
- HANDOFF mais recente e **relatório 46**, matriz dos quatro componentes e suas quatro queries literais;
- canônico UAT e export/snapshot de referência PROD mais recentes **após as publicações e saneamento editorial**, se devidamente comprovados; não usar fonte obsoleta;
- configurações do dashboard e exemplos locais de widgets `query_value` compatíveis (schema, requests, compute, título, descrição, layout);
- trechos relevantes de código e relatórios somente se indispensáveis para confirmar o marcador/semântica de cada consulta.

**Não repetir análise de todos os indicadores.** Para cada novo contador, registrar em documento privado:
`evento e unidade | serviço/ambiente | query derivada de 06M | contador bruto ou proxy | o que comprova | o que não comprova | presença de retries/falhas transitórias | janela e filtro | teste humano de aceite`.

As evidências documentais do 06M indicam sinais já existentes. **Não declarar que as quatro queries retornam dados reais em UAT/PROD sem validação nesses ambientes**. Não excluir artificialmente falhas sem identificador, nem exigir presença de atributo de proposta nas contagens brutas. Manter veto aos filtros destrutivos homologado anteriormente.

## 3. Construção aditiva e isolada, sem mexer nos existentes

Criar dois arquivos **NOVOS** em área privada de candidatos, com nomes próprios e sem sobrescrever:
- `dashboards/uat-candidato-06n-quatro-contadores-operacionais.json`
- `dashboards/prod-candidato-06n-quatro-contadores-operacionais.json`

Se nomes estiverem ocupados, acrescentar sufixo versionado. Não editar o canônico UAT, exports pós-publicação, snapshots PROD ou candidatos antigos.

- **UAT:** copiar base local válida de UAT e acrescentar somente W1.4 e W2.9–W2.11, adaptando consultas à nomenclatura e variáveis reais de UAT.
- **PROD:** copiar baseline PROD próprio e acrescentar os mesmos quatro widgets com filtros/nomenclatura corretos de PROD. **Não clonar UAT sobre PROD** nem remover avisos/descrições específicos do ambiente.
- Nomes, descrições e unidades devem refletir exatamente **o evento contado**; são volumes técnicos do componente, com ressalvas claras, não taxas.
- Usar query_value com agregação de **contagens acumuladas no período selecionado**, sem confundir `last point` de um bucket com total da janela. Verificar como o backend Datadog agrega a query de logs (ex.: sum/count no horizonte), evitando afirmar capacidade não testada. O tipo de data source e schema devem ser compatíveis com os widgets atuais e a ferramenta disponível.
- Evitar fixar IDs de widget herdados ou duplicados de outra origem; inserir IDs somente conforme o schema e comportamento real da UI.
- Posicionar os widgets em seus grupos sem sobrepor/alterar layout dos widgets existentes; se a UI/layout exigir modificação dos anteriores, **bloquear o candidato** e explicar a limitação, sem movê-los silenciosamente.
- Não inserir filtros por proposta que escondam eventos sem identificador, não reduzir escopo dos widgets brutos existentes, não modificar queries/fórmulas/títulos de outros 21.

**Nenhum widget de taxa, percentual ou fórmula será criado** — inclusive nenhuma soma automática sucesso+erro agora. Esses cálculos ficam para decisão futura, depois da observação isolada.

## 4. Integridade, Git local e resultados da validação offline

Usar apenas ferramentas locais:
1. `git status --short`, histórico e diff **somente leitura**; nenhum add, commit, push, reset, restore, checkout ou stash;
2. SHA-256 dos baselines e candidatos, com snapshots privados imutáveis quando necessário;
3. parse/schema completo e validador local em **dry-run OFFLINE** (não acionar publicador remoto);
4. inventário recursivo por grupo: esperado 21→25, quatro novos e zero remoções;
5. diff semântico demonstrando **somente quatro widgets adicionados** em cada ambiente, sem alteração de qualquer nó anterior, notas, queries, variáveis, descrições ou metadados compartilhados;
6. confirmar fonte e filtros do ambiente; nenhuma tag de ambiente errado;
7. verificar que os novos widgets contam volume no horizonte selecionado, não um único log arbitrário nem uma taxa;
8. diferenciar `JSON/schema validado`, `consulta comprovada em Log Explorer`, `widget renderizado em UAT` e `dados comprovados em PROD`. Nenhuma dessas provas substitui automaticamente as outras.

Se houver bloqueio de schema, ambiente ou layout, registrar o motivo preciso, não improvisar mudanças em originais. Nunca tentar extrair sessão, cookies, API keys ou contornar controles corporativos.

## 5. Homologação humana prevista — fase seguinte, sem publicação agora

Fornecer roteiro curto e específico para o responsável:
- importar **primeiro em UAT**, após aprovação separada do candidato;
- conferir os quatro valores com consultas equivalentes no Explorer, usando a **mesma janela temporal** e filtros compatíveis; identificar diferenças por buckets, atrasos, reentregas e eventos de teste;
- verificar se entradas e saídas locais são observáveis; verificar se erros funcionais representam a categoria real de falha; conferir se o volume de contratação digital é universo distinto de recorrência;
- documentar quais números são coerentes **isoladamente**, mesmo que um quociente entre eles ainda não seja legítimo;
- só depois de homologação UAT e autorização específica preparar/importar PROD e reconciliar exports;
- **etapa posterior e separada:** decidir se os contadores permitem fórmula operacional de `total = sucessos + erros` com uma mesma população fechada, ou uma taxa percentual, sem criar nada automaticamente.

Não pedir ao operador para fazer testes em PROD que gerem eventos artificialmente; somente consultas de leitura e observação de tráfego real.

## 6. Relatório privado, decisão e parada

Produzir UM novo relatório Markdown privado, próximo número livre de `planos/`, sugestão:
`47-construcao-local-quatro-contadores-operacionais-uat-prod.md`.

Deve conter:
1. decisão do responsável: **foco na saúde dos componentes e sucesso/erro locais**, não exigência de resultado final externo;
2. explicação direta da classificação operacional de sucesso e erro e risco de operações pendentes/retried, sem transformar o trabalho em debate matemático;
3. os quatro novos widgets, fontes/unidades/queries literais e limites específicos;
4. contagens por grupo e total antes/depois em UAT e PROD;
5. arquivos candidatos, baseline, SHA-256, validação e diff completo;
6. garantia de que os 21 widgets anteriores, vetos e limites continuam intactos;
7. status de eventual fragilidade de fonte, atributo ou schema por componente;
8. checklist visual de UAT e PROD para homologação humana posterior;
9. nenhuma taxa criada, nenhuma operação remota realizada, zero alteração Java, zero commit/push;
10. próxima decisão necessária: `APROVAR OU AJUSTAR CANDIDATOS LOCAIS PARA IMPORTAÇÃO MANUAL UAT`;
11. documentação para apresentação derivada de etapa anterior permanece **pendente de atualização** até a validação dos quatro contadores.

Na resposta final ao operador, entregar tabela dos quatro widgets (nome, evento, unidade, condição de aceite), inventário 21→25 por ambiente se realmente validado, caminhos dos dois candidatos e do relatório, e **o bloqueio específico, se houver**. Não voltar a discutir a fórmula de Produto nem declarar taxa válida nesta execução.

**PARADA OBRIGATÓRIA:** esta execução apenas prepara JSONs locais e documentos para revisão. **Não importar/publicar nada em UAT/PROD**, não alterar canônicos, código, fonte de telemetria ou Git local, e não iniciar a documentação final de apresentação. Aguardar autorização expressa da próxima fase.