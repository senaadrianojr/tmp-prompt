# DARWIN MONITOR — ETAPA 02/04
## CORREÇÃO FINAL DE INTEGRIDADE DO RELATÓRIO — PRÉ-GATE ETAPA 03

### 0. Missão e limites

Atue como revisor técnico sênior de observabilidade, arquitetura de integração e documentação de engenharia.

A investigação da Etapa 02 JÁ FOI REALIZADA. Há relatórios, documentos anteriores, evidências de código, planilhas e resultados de acesso autenticado ao Datadog. A última versão do relatório, entretanto, contém **campos vazios, placeholders, referências genéricas e conclusões mais fortes do que as evidências apresentadas**.

**OBJETIVO ÚNICO:** recuperar evidências existentes, corrigir inconsistências e produzir a versão final, completa, verificável e navegável do relatório da Etapa 02, para revisão humana antes da Etapa 03.

**Isto não é um novo discovery. Não recomece a investigação de todos os fluxos, não crie dashboards, não altere configurações nem execute a Etapa 03.**

Apenas UM arquivo pode ser modificado nesta execução:

    ./REVALIDACAO_TECNICA_ETAPA_02.md

Use a raiz REAL do projeto darwin-monitor como referência. Confirme o diretório atual antes de qualquer operação; não crie novamente uma estrutura redundante docs/darwin-monitor/.

Preserve os demais documentos, inclusive o guia README_FERRAMENTAS_AMBIENTE.md, a auditoria Etapa 01 e todos os arquivos de indicadores, dashboards, planos e código. Não crie outros documentos, relatórios auxiliares, backups, scripts persistentes, commits nem arquivos *-rev2.md.

### 1. Problemas concretos a corrigir

As capturas da versão anterior do relatório exibiram:

- "Código-Fonte: Repositórios ..." em vez de nomes reais, branch/commit e referências.
- "Planilhas: Leitura textual de ..." sem especificar arquivo, aba, linha e coluna usadas.
- "Datadog: Leitura da definição do dashboard (ID:)" com identificação vazia.
- Coluna "Responsável:" sem valor na matriz dos indicadores.
- "Divergência ()" sem o conteúdo da divergência.
- Referências como "O widget 0 é um MVP válido" sem identificação auditável do widget.
- Plano de Etapa 03 usando "****", campos vazios e referências inexistentes a arquivos.
- "Backlog: indicadores bloqueados ()" sem nomes.
- Afirmação "Calculável Hoje" para Taxa de Falha no Cancelamento e Taxa de Reprocessamento sem demonstração explícita de numerador, denominador e query completa.
- Afirmação de que Taxa de Falha depende de instrumentação aparentemente motivada somente pela inexistência de um widget.
- Proposta de nova instrumentação no Índice de Sucesso sem comprovar se Evento 31/status 8 já produz sinal consultável.

Trate isso como uma **falha de integridade documental** e de rastreabilidade, não como mera formatação.

**Nunca substitua uma referência desconhecida por um texto plausível inventado.** Se não conseguir recuperar uma evidência, escreva "NÃO COMPROVADO" e explique exatamente qual informação falta e de onde ela deveria vir.

### 2. Recuperar antes de modificar

1. Confirme que está na raiz efetiva de darwin-monitor.
2. Leia integralmente a versão atual de REVALIDACAO_TECNICA_ETAPA_02.md.
3. Leia a auditoria corrigida AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md e os documentos atuais relevantes dos seis indicadores.
4. Recupere, **somente em leitura**, versões anteriores do relatório por histórico Git, se houver, e os documentos de código/Datadog/indicadores já examinados. Use git log / git show / git diff em modo leitura quando disponíveis e pertinentes.
5. Não presuma que o conteúdo mais recente é necessariamente o mais correto: compare evidências e preserve referências mais completas encontradas em versões anteriores.
6. Não reexecute varreduras extensivas de repositórios. Caso falte uma evidência específica, faça busca direcionada no arquivo, símbolo, classe, método, widget ou query que já foi apontado.
7. Reaproveite a metodologia já comprovada de leitura das planilhas e acesso autorizado ao Datadog. O objetivo é complementar lacunas pontuais, não refazer o diagnóstico inteiro.
8. Se a versão histórica do relatório não estiver disponível, não invente históricos. Registre essa limitação.

**Ponto de controle:** antes de salvar, identifique internamente quais alegações você conseguiu restaurar com prova e quais permanecem desconhecidas.

### 3. Escopo imutável dos seis indicadores

Trabalhe exclusivamente com:

| Indicador | Aplicação(ões) informada(s) |
|---|---|
| Índice de Sucesso | Gestão Eventos Conta Cartão |
| Taxa de Falha | Gestão Eventos Conta Cartão |
| Taxa de Falha no Cancelamento | Gestão Eventos Conta Cartão + Gestão Eventos Pagamento |
| Taxa de Reprocessamento | Gestão Eventos Conta Cartão |
| Parcela Cobrada × Parcela Lançada | Gestão Eventos Pagamento |
| Origem | Venda Digital |

Nomes de repositórios anteriormente identificados como candidatos reais:
- sboot-pseg-base-orch-gestao-eventos-conta-cartao
- sboot-pseg-base-orch-gestao-eventos-pagamento
- springboot-pseg-base-venda-digital

**Confirme no workspace antes de citar:** os nomes são referências do discovery anterior, não prova automática da versão de código atual.

As planilhas revelaram uma diferença entre valores da coluna Responsável:
- Índice de Sucesso, Taxa de Falha, Falha no Cancelamento e Reprocessamento: Seguros, segundo verificação anterior;
- Parcela Cobrada × Parcela Lançada e Origem: bvseg, segundo verificação anterior.

Verifique a referência exata à planilha/aba/célula ou preserve a informação como "constatado em execução anterior, referência primária ainda não recuperada". **Não transforme bvseg em Seguros por inferência.** Os dois indicadores permanecem no inventário técnico delimitado, porém com **escopo de implementação dependente de decisão da PO/Negócio**.

Origem segue sem definição funcional confirmada. Distinguir origem do contrato/canal de venda de via de processamento Pismo/DXC. **Nunca equiparar Origem com MOB sem evidência.**

### 4. Hierarquia de evidência — critérios rigorosos

Para cada conclusão, informe o nível de suporte:

1. **CÓDIGO COMPROVADO:** repositório, branch/commit quando disponíveis, caminho do arquivo, classe/método/símbolo e linha/faixa quando possível; explicar o comportamento concreto.
2. **PLANILHA CONFIRMADA:** nome real do XLSX, aba, linha/célula do indicador e do responsável; diferenciar leitura atual de conclusão herdada.
3. **DATADOG OBSERVADO:** ambiente, serviço, dashboard ID, widget ID ou identificador verificável, tipo de sinal, query/filtro/intervalo, retorno observado e momento do teste (omitindo dados sensíveis).
4. **DOCUMENTAÇÃO ANTERIOR:** documento, caminho, seção, conclusão relatada e se foi ou não revalidada agora.
5. **HIPÓTESE / PENDENTE:** o que falta e quem ou qual ferramenta pode comprovar.

**Separações obrigatórias:**
- Código implementado ≠ dado efetivamente emitido.
- Widget existente ≠ widget com dados.
- Widget com dados ≠ indicador de negócio válido.
- Logs de erro ≠ taxa de falha sem população total.
- Mensagem na DLQ ≠ contagem confiável de operações reprocessadas.
- HTTP 200/2xx ≠ sucesso funcional.
- Ausência de widget ≠ necessidade comprovada de instrumentação.
- Metadados de dashboard ≠ execução bem-sucedida da query nem leitura do valor.
- 0 retornado ≠ ausência do evento se há janela, filtro, permissão ou retenção não investigados.

Não descreva "comprovada no Datadog" uma taxa cuja única evidência seja definição de widget contendo um filtro de erro.

### 5. Validação PONTUAL de queries, numeradores e denominadores

Recupere primeiro as queries completas nos documentos anteriores ou na definição de widgets que já foram consultados. Se necessário, e apenas para fechar lacunas específicas, acesse novamente os recursos **autorizados e somente leitura** do Datadog.

Siga o runbook existente e o método de autenticação já validado. Não leia nem imprima cookies em logs ou relatório. Não utilize a API pública como substituto obrigatório. Não faça mudanças de dashboards, widgets ou monitores nesta revisão.

Para os indicadores classificados como "Calculável Hoje":

**A. Taxa de Falha no Cancelamento**
- A query existente mede numerador (falhas definitivas após retries)?
- Qual query/fonte mede denominador (total de cancelamentos elegíveis)?
- As duas populações usam mesmos filtros, aplicação, intervalo e regra funcional?
- Como são tratados os cancelamentos pendentes, as tentativas repetidas e o resultado final?
- Os dois valores foram realmente retornados por consultas ou apenas há uma consulta que conta logs de erro?
- Se não houver denominador comprovado, **reclassifique para PARCIAL / NÃO COMPROVADO COMO TAXA**.

**B. Taxa de Reprocessamento**
- A query existente mede mensagens na DLQ, tentativas ou operações únicas reprocessadas?
- Há query/fonte real para denominador da taxa?
- Como evitar duplicidade por retry e redelivery?
- Mensagens que chegaram à DLQ são a mesma população exigida na regra funcional?
- Se só existir uma contagem de DLQ, classifique como **métrica operacional parcial**, não "Taxa de Reprocessamento calculável hoje".

Para os demais:

**C. Índice de Sucesso**
- Distinguir sucesso técnico HTTP de sucesso funcional;
- verificar em código Evento 31/status 8 e, apenas se houver evidência operacional, confirmar se ele aparece nos sinais consultáveis do Datadog;
- não exigir nova instrumentação antes de descartar com evidência o aproveitamento de sinal existente;
- classificar MVP parcial com precisão.

**D. Taxa de Falha**
- Identificar se o dado de falha funcional já existe em logs, eventos, spans ou métricas;
- a inexistência de widget não justifica afirmar instrumentação obrigatória;
- separar necessidade de **configurar query/widget** de necessidade de **emitir dado novo**.

**E. Parcela Cobrada × Parcela Lançada**
- Preservar evidências já identificadas sobre fontes de cobrança e lançamento, necessidade de conciliação e eventual fonte externa;
- tratar a divergência bvseg como decisão de escopo, não como prova de inviabilidade técnica.

**F. Origem**
- Preservar evidências de implementação relativas a Pismo/DXC, se realmente localizadas;
- registrar que a regra de negócio permanece pendente;
- nunca confundir com processamento MOB.

**Limite:** se não for possível obter a query ou denominador por acesso autorizado ou documentos existentes, **não empreenda exploração ilimitada**. Marque "NÃO VALIDADO" e identifique a consulta/campo faltante e o responsável pela confirmação. Isso é aceitável para seguir à consolidação *com pendências*, desde que o relatório não exagere a viabilidade.

### 6. Reconstrução do relatório com conteúdo real

Atualize **no mesmo arquivo**, mantendo Markdown legível por desenvolvedores e sistemas de consulta.

Seções obrigatórias:

#### 6.1 Resumo executivo e gate
- Data da revisão, raiz, escopo, grau de acesso confirmado e conclusões comprováveis.
- Não declarar etapa plenamente concluída se os requisitos de integridade permanecerem incompletos.
- Para avançar à Etapa 03, aceite "APTO COM PENDÊNCIAS DOCUMENTADAS" apenas quando houver base suficiente para documentar estado atual e estado-alvo **sem inventar**.

#### 6.2 Fontes realmente consultadas
- Tabela de repositórios de código, paths, branch/commit (ou "não disponível").
- Planilhas com nome, aba e referência de células (ou "não recuperado").
- Datadog: dashboard real, widget(s), query(s), período, serviço, sinais e **resultado observado**; caso só tenha metadata, escrever explicitamente "metadados, valores não confirmados".
- Referência aos relatórios antigos/versões Git recuperadas.

#### 6.3 Matriz dos seis indicadores
Campos mínimos:
- indicador;
- responsável tal como consta na planilha;
- evidência de código;
- evidência Datadog;
- fórmula/definição funcional confirmada ou pendente;
- numerador/denominador e fonte de ambos;
- classificação de disponibilidade operacional;
- MVP possível agora e limite conhecido;
- instrumentação ou outra fonte necessária (com evidência ou hipótese);
- decisão/bloqueio e proprietário.

**Não deixe células vazias**: use "NÃO COMPROVADO", "NÃO APLICÁVEL" ou "PENDENTE", conforme o caso.

#### 6.4 Fichas individuais dos seis indicadores
Incluir evidências concretas, dúvidas, queries reais quando recuperadas, regra de contagem, estados intermediários, reprocessamentos e correlações. Não inventar identificadores.

#### 6.5 Matriz de instrumentação versus configuração
Para cada alteração proposta diferenciar:
- somente query/widget;
- inclusão/correção de log, span ou métrica;
- API/banco/BI/fonte externa;
- nenhuma alteração necessária (demonstrada);
- pendente de comprovação.

Indicar aplicação exata, componente/ponto de instrumentação quando encontrado, dado faltante, motivo, condições e impacto. Evitar sugestões genéricas ("adicionar logs").

#### 6.6 Dashboard atual versus dashboard-alvo
Descrever quais widgets são comprovadamente operacionais hoje, quais são métricas parciais, quais apenas estão configurados e quais ainda exigem dados ou regra. Definir pré-requisitos para dashboard-alvo **sem implementá-lo**.

#### 6.7 Plano de Etapa 03 POR ARQUIVO REAL
Produzir tabela obrigatória:
- caminho relativo EXATO de arquivo atualmente existente;
- seção atual relevante;
- ação específica: manter/editar/consolidar/registrar lacuna;
- evidência técnica a incorporar;
- lacuna funcional mantida explicitamente;
- prioridade e critério de aceite documental.

Inspecione os nomes reais em indicadores/, dashboards/, planos/ e arquivos gerais antes de citar. **É proibido usar "****", "..." ou paths imaginários.**

Para documentos históricos ou fora do escopo, registrar apenas a indicação de arquivamento futuro (Etapa 04), sem mover ou excluir agora.

#### 6.8 Decisões humanas
- PO/Negócio: confirmação de atribuição Seguros x bvseg; regra de Origem; fórmulas, janelas e thresholds quando não aprovados.
- Engenharia/Observabilidade: dados, correlation keys, instrumentações e consultas não comprovadas.
- Dados/DBA/BI: dependências externas quando identificadas.

Para cada decisão: pergunta concreta, indicador afetado, dono da decisão e impacto sobre implementação e sobre a Etapa 03.

#### 6.9 Controle final de integridade
Informar:
- seções restauradas, informações corrigidas e informações não recuperáveis;
- se houve consulta Datadog nesta revisão ou apenas reaproveitamento de consulta anterior;
- todos os arquivos modificados;
- resultado de checagem de placeholders e de referências.

### 7. Checagens automatizadas ou manuais antes de salvar

**Obrigatórias:**
1. Buscar no documento final por placeholders e campos incompletos, inclusive: `****`, `...` em referências, `( )`/`()` vazios, `ID:` sem valor, `Responsável:` vazio, `widget 0` genérico, caminhos vazios e seções sem conteúdo.
2. Confirmar se existem exatamente **seis** fichas de indicador e seis linhas na matriz, além do cabeçalho.
3. Confirmar que todas as classificações "CALCULÁVEL HOJE" possuem evidência dos dois lados do cálculo quando forem **taxas**.
4. Conferir se os paths listados no plano da Etapa 03 existem de verdade. Não criar arquivos só para satisfazer o plano.
5. Conferir que nenhum documento foi confundido com uma fonte de verdade só por ter sido gerado por IA.
6. Se algum ponto permanecer sem evidência, substituir o placeholder por uma **pendência explicativa explícita**; a checagem não exige inventar valores.
7. Conferir a alteração final por diff, garantindo que o trabalho válido anterior não foi apagado e que **somente o relatório da Etapa 02** foi modificado.

A checagem deve aparecer resumida na seção de integridade do próprio relatório.

### 8. Regras de segurança e escopo operacional

- Somente leitura em repositórios, planilhas, histórico Git e Datadog.
- Não modificar código, telemetry, cookies, runbook, README, planilhas, dashboards, widgets, monitores, bancos, infraestrutura ou arquivos de outras etapas.
- Não criar nem excluir widget de teste nesta execução.
- Não instalar dependências, não alterar permissões, não tentar burlar autenticação e não tentar métodos de acesso não autorizados.
- Proteger informações internas: **não registrar cookies, tokens, cabeçalhos privados, credenciais, dados reais de clientes ou payloads pessoais**.
- Consultas HTTP autorizadas e consultas de Datadog devem ter escopo mínimo para validar os indicadores. Não fazer varreduras amplas em dados de produção.
- Não fazer commit nem push sem autorização.
- Não executar a Etapa 03 ou a Etapa 04.

### 9. Gate final: conclusão honesta e verificável

Classifique a saída:

- **APTO PARA ETAPA 03:** relatório íntegro, referências reais, critérios de cálculo suficientes para os indicadores classificados como disponíveis, e todos os gaps identificados com clareza.
- **APTO COM PENDÊNCIAS DOCUMENTADAS:** relatório íntegro e rastreável, porém alguns indicadores permanecem não calculáveis, com fontes ou decisões pendentes claramente identificadas. A Etapa 03 poderá documentá-los sem alegar implantação.
- **NÃO APTO:** evidências essenciais ausentes, referências quebradas/inventadas ou contradições que impedem uma consolidação documental confiável.

**Não preserve uma classificação otimista apenas para manter o status anterior.** Rebaixe indicadores quando numerador, denominador, query, janela ou cobertura não forem demonstrados.

### 10. Resposta final obrigatória

Entregue, em português:
1. Caminho do único arquivo atualizado e confirmação de que nenhum outro foi modificado.
2. Quais evidências/referências foram restauradas das versões anteriores.
3. Quais afirmações mudaram, especialmente "Calculável Hoje", "Depende de Instrumentação" e "MVP parcial", e por quê.
4. Quais queries, numeradores e denominadores foram efetivamente validados e quais permanecem pendentes.
5. Plano da Etapa 03 — quantidade de arquivos reais afetados e suas finalidades.
6. Lista de decisões humanas pendentes: Seguros/bvseg, Origem e outras constatadas.
7. Resultado e justificativa do gate.

**Pare ao finalizar e aguarde minha aprovação. Não inicie a Etapa 03.**
