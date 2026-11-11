# PROMPT 06A — Preparação da promoção seletiva de widgets do Darwin Monitor para produção

> **Execução:** nova sessão do Gemini CLI, no workspace corporativo autorizado.
>
> **Missão:** antecipar os widgets executivos já observáveis em produção sem depender da nova instrumentação de correlação por proposta, e verificar separadamente a viabilidade de promover o indicador de Origem por processadora.
>
> **Autorização:** leitura, análise, criação de relatórios e JSONs **candidatos locais**. Consultas remotas somente de leitura quando já autorizadas. **Não publicar, criar, editar ou excluir recursos Datadog em UAT ou PROD. Não alterar o JSON canônico UAT nem scripts de publicação.**
>
> **Segurança:** este prompt público contém só método. Toda evidência operacional, nomes de serviços, identificadores de dashboard, queries completas, exports, logs, dados internos e arquivos gerados pertencem exclusivamente ao workspace corporativo privado. **Não realizar push de artefatos corporativos para o repositório público de prompts.**

## 1. Contexto e objetivo

O Darwin Monitor já possui widgets em UAT que observam eventos operacionais por meio de logs existentes e não exigem a nova instrumentação do identificador cdPropostaSeguro. O responsável deseja **disponibilizar antecipadamente em produção os widgets cujo sinal já exista efetivamente em PROD**, sem esperar a evolução da instrumentação ainda em UAT.

Há uma dúvida específica e prioritária: **a instrumentação que registra a processadora de origem está disponível apenas em UAT, ou os atributos correspondentes já chegam aos logs de PROD?** Se a informação já for observável em PROD, avaliar **todos os widgets do indicador Origem**, sem aguardar a instrumentação de correlação por proposta.

O objetivo não é clonar integralmente o dashboard UAT: é construir um pacote de promoção **seletivo, minimamente suficiente, comprovado e reversível**, a ser publicado somente em etapa posterior expressamente autorizada.

Esta é uma execução pontual de preparação para PROD; não substitui nem amplia o plano de conclusão dos sete indicadores executivos.

## 2. Fontes de verdade e escopo congelado

Localize a raiz física correta do workspace corporativo Darwin Monitor (estrutura lógica: darwin-monitor/, planos/, dashboards/, JSON canônico UAT e publicador genérico). Não crie um segundo diretório darwin-monitor aninhado.

Consulte, de forma seletiva:
- Relatório local mais recente da etapa 05E (prefixo 26-) e artefatos candidatos já construídos;
- plano da etapa 05D (relatório 25-) e matriz de sete linhas;
- relatórios e evidências consolidadas de IND-01, IND-02, IND-03 e IND-07, apenas quando necessários;
- export ou snapshot **atual** e completo do dashboard UAT, conferido com o canônico;
- configuração existente de um eventual dashboard de PROD, por leitura autorizada ou export manual do responsável;
- código e histórico de releases/branches dos serviços do escopo, apenas para verificar origem dos sinais e possível presença da instrumentação em produção.

Preserve o escopo já aprovado:
- **Verdes:** linhas 2 (Índice de Sucesso), 11 (Taxa de Falha), 13 (Falha no Cancelamento), 14 (Reprocessamento), 19 (Origem).
- **Cinzas:** linhas 17 e 18 (Parcela Cobrada × Lançada); somente incluir algo com sinal real suficiente e cobertura explicitamente delimitada.
- **Amarelas excluídas:** linhas 4, 8, 9, 10, 12 e 15. Não investigar nem construir widgets novos para elas.

Não assumir que o nome de um widget equivale à cobertura integral de um requisito. Não reabrir auditorias já concluídas sem inconsistência material.

## 3. Primeiro levantamento: inventário por widget, não por grupo

A partir do dashboard UAT **real e mais recente**, inventarie recursivamente widgets aninhados, grupos e consultas. Identifique por widget:
- identificador real quando disponível, grupo e título;
- fonte (Logs, APM ou outra), serviço, ambiente, queries, fórmulas, filtros, atributos de logs, armazenamento/index e template variables;
- qual linha do escopo executivo ele atende; qual é sua **unidade real** (ocorrências de logs, tentativas, retornos locais, desfechos, propostas únicas etc.);
- dependência direta ou indireta de cdPropostaSeguro, inclusive em queries aninhadas, filtros, fórmulas, variáveis compartilhadas ou agrupamento;
- outros pré-requisitos: tags, logs novos, versão de código, pipelines, eventos terminais, permissões, facets, storage e tráfego;
- status de evidência específica de **PROD**, separado da evidência UAT.

**Não basta remover um filtro por cdPropostaSeguro de um widget:** se sua semântica ou cálculo exigir o atributo, ele não está pronto para produção.

Não publicar percentuais de sucesso/falha por proposta quando numeradores e denominadores não estiverem reconciliados. Não converter retry ou erro intermediário em falha terminal. Não chamar retorno local de sucesso definitivo do negócio.

## 4. Verificação prioritária: processadora em produção

Distinga rigorosamente três provas independentes:

**A. Código:** identifique de onde surge o atributo de processadora e quais caminhos de emissão realmente o colocam nos logs. Verifique se a implementação está na branch de referência e/ou na feature; não confunda presença em código com deploy.

**B. Implantação:** se disponível, verifique o release, commit ou artefato realmente implantado em PROD. Não inferir versão de produção apenas pelo conteúdo atual da master.

**C. Telemetria real:** verifique, pela evidência manual do responsável no Log Explorer de PROD, se há registros com o atributo de processadora e o tipo de evento; se contratação e cancelamento são observáveis; e se o atributo pode ser usado em contagem/agrupamento conforme os widgets existentes.

Não adivinhar valor do atributo de ambiente: descobrir se a tag efetiva é prod, prd ou outra denominação, a partir dos dados reais. Aplicar os filtros do serviço real da aplicação sem usar o serviço UAT por engano.

Não presumir que todo evento observado em UAT aparecerá em PROD, nem que a ausência de determinado valor de processadora em uma janela significa defeito. Investigar janela com tráfego conhecido e diferença entre ausência de logs, ausência do atributo e ausência de uma categoria de processadora.

**Saída obrigatória para o indicador Origem:** decidir entre:
- **TODOS OS WIDGETS CANDIDATOS**: os atributos e agrupamentos necessários estão comprovados em PROD;
- **PROMOÇÃO PARCIAL POR WIDGET**: apenas alguns sinais estão comprovados;
- **NÃO ELEGÍVEL POR ENQUANTO**: falta dado real necessário;
- **INCONCLUSIVO**: há código ou indícios, mas não evidência suficiente de PROD.

Preserve a distinção entre eventos observados de contratação/cancelamento e a efetivação final de contratos/apólices. Nunca invente valores ou séries futuras.

## 5. Classificar elegibilidade de cada widget para PROD

Use as seguintes classificações:

1. **APTO PARA PROMOÇÃO (VALIDADO EM PROD):** não depende de instrumentação pendente; query, semântica, dados, agrupamento e origem da telemetria estão comprovados no ambiente de produção.
2. **CANDIDATO — VALIDAÇÃO HUMANA PENDENTE:** a configuração pode ser preparada localmente, mas ainda precisa da execução e evidência no Log Explorer de PROD.
3. **DEPENDENTE DE INSTRUMENTAÇÃO/DEPLOY:** a fonte necessária não está comprovadamente implantada ou emitida em PROD.
4. **INCONCLUSIVO:** falta informação específica para decidir, incluindo acesso ao snapshot ou à configuração real.
5. **NÃO PROMOVER:** indicador enganoso, fórmula inadequada, fonte incompatível ou desvio do escopo aprovado.

Inclua na matriz, no mínimo, o trabalho operacional já consolidado nos grupos de:
- recorrência (eventos iniciados, retorno de sucesso local, falhas de negócio e retentativas separadas);
- Origem (todos os widgets realmente existentes, inclusive visualizações relacionadas a contratação e cancelamento);
- outros widgets executivos elegíveis sem a correlação por proposta, conforme evidência atual.

Para cancelamento e reprocessamento, avalie a viabilidade dos sinais já existentes sem reclassificar estorno como cancelamento nem confundir esgotamento de tentativas, emissão de evento e desfecho final.

Widgets exclusivamente de validação da nova instrumentação de correlação por proposta **não fazem parte do primeiro lote de produção**.

## 6. Validação humana no Log Explorer de produção

O Gemini **não consegue executar consultas de Log Explorer** neste fluxo de trabalho. Não contorne a restrição com APIs não autorizadas, cookies, endpoints alternativos ou alegações de validação indireta.

Prepare um pacote de **até cinco verificações prioritárias**, em ordem de valor para a decisão:
1. Presença de atributo de processadora e tipo de evento nos logs de PROD do serviço relevante;
2. Evidência de contratação por processadora e possibilidade real de Count/Group by;
3. Evidência de cancelamento por processadora e possibilidade real de agregação temporal;
4. Evidência em PROD dos sinais operacionais de recorrência, incluindo as populações corretas;
5. Consulta composta ou a mais importante para decidir sobre cancelamento/reprocessamento independente da nova instrumentação.

Para cada verificação, entregue **somente no relatório local corporativo**:
- query **literal** completa, com service e env efetivamente identificados;
- período recomendado, index/storage, evento e objetivo;
- contagem esperada em termos qualitativos, sem inventar volumes;
- teste de filtro e, quando relevante, teste separado da agregação/facet;
- como interpretar resultados positivos, zero eventos, erro de sintaxe e evidência inconclusiva;
- quais widgets ficam aptos se o responsável confirmar a evidência;
- o que o responsável deverá informar: período, quantidade de resultados, valores/atributos observados e captura sem dados de clientes.

**Uma query apenas preparada não significa validada.** Se o operador ainda não testou em PROD, marque VALIDAÇÃO HUMANA PENDENTE e não classifique o widget como APTO.

Se faltarem informações para fornecer query literal sem inventar nomes internos, forneça a instrução exata de como obtê-los e registre a validação como bloqueada; não fabrique filtro.

## 7. Construir um candidato local separado para produção

Após a análise, produza um **JSON CANDIDATO de PROD**, somente no workspace corporativo privado, em diretório de candidatos/drafts já existente ou criado para novos arquivos.

Diretrizes:
- partir do snapshot UAT **atual e confiável**, mas selecionar **somente widgets elegíveis ou candidatos rastreáveis**;
- excluir da versão candidata pronta para promoção qualquer widget que dependa de cdPropostaSeguro ou de outra instrumentação ainda não disponível em PROD;
- **não simplesmente substituir env:uat por env:prod em todas as strings**: revisar filtros, fontes, service, log indexes, storage, variáveis e atributos usando a configuração real de PROD;
- quando houver widgets pendentes de evidência, separá-los ou marcar o candidato inteiro como **NÃO PUBLICÁVEL** até a validação. Nunca apresentar placeholders ou queries não testadas como payload pronto;
- preservar a semântica original e ajustar títulos/descrições para a unidade realmente observada;
- preservar tipos de visualização, fórmulas válidas e grupos necessários, sem carregar dependências globais de UAT;
- não copiar IDs, URL ou identificadores administrativos do dashboard UAT como destino de PROD;
- se já existir dashboard PROD, obter seu snapshot completo e **preparar uma alteração aditiva e não destrutiva**, preservando integralmente tudo o que já existe; não sobrescrevê-lo;
- se não existir, preparar somente a definição candidata para um novo dashboard, sem criá-lo remotamente;
- manter JSON original/export e canônico UAT intactos.

Se não existir fonte atual e confiável de PROD, prepare apenas o candidato local claramente rotulado como **NÃO PUBLICÁVEL — BASE DE PROD PENDENTE**.

Não executar o publicador e não modificar seu código. Se ele for genérico e parametrizável, analise com segurança como poderia ser usado em etapa futura; **não presumir que o nome do script UAT o torna seguro para PROD**.

## 8. Pré-validação local e auditoria de segurança

Execute somente operações locais comprovadamente sem efeitos remotos:
- parse do JSON;
- integridade e estrutura do schema;
- contagem real de grupos e widgets, inclusive aninhados;
- ausência de referências UAT no candidato PROD (inclusive URLs, template variables, tags e queries);
- ausência de dependências obrigatórias da nova instrumentação de correlação nos widgets selecionados;
- checagem de fontes, serviço, env, índices, armazenamento e campos;
- detecção de IDs duplicados e metadados read-only inadequados;
- diff semântico com o dashboard UAT e, se existir, com o dashboard PROD atual;
- confirmação de que nenhum widget UAT, configuração canônica, script, repositório de aplicação ou recurso remoto foi modificado.

Não testar opções do publicador sem verificar previamente que elas são **estritamente locais**. Não executar flags publish nem endpoints de escrita.

## 9. Relatório local obrigatório e decisão de promoção

Salvar um **relatório novo e conciso** em darwin-monitor/planos/, após verificar o último prefixo disponível e sem sobrescrever relatórios existentes. Sugestão: prefixo 27-.

O relatório deve conter:

### A. Referências e snapshots
Origem, data, hash e grau de confiança do export/canônico UAT e do eventual dashboard PROD atual.

### B. Matriz por widget
Indicador, ID real, título, serviço/fonte, unidade observada, dependência de correlação, evidência de PROD, status, justificativa e próximo passo.

### C. Diagnóstico da processadora
Prova em código, prova de release implantado, prova nos logs de PROD, suporte a agregação, cobertura dos widgets de Origem e conclusão sem confundir indício com validação.

### D. Lote 1 de produção
Widgets **APTOS** comprovados e seu grau de cobertura operacional/negócio; separar claramente os apenas candidatos.

### E. Pendências e Lote 2
Widgets que aguardam instrumentação, telemetria ou decisão técnica, incluindo os dependentes do identificador de proposta.

### F. Pacote de até cinco consultas
Queries literais e passos exatos para o operador executar no Log Explorer de PROD, sem dados sensíveis.

### G. Artefatos e integridade
Caminhos dos JSONs candidatos, resultados de validações, lista exata de arquivos criados/alterados e diff semântico. Indicar riscos de sobrescrever dashboard existente.

### H. Próximo gate
Qual decisão humana falta, quais provas precisam ser devolvidas e quais seriam os passos exatos de uma publicação posterior, com backup e rollback.

Concluir em texto curto e direto:
- Quantos widgets são **APTOS**, **CANDIDATOS** e **BLOQUEADOS** com base em evidência real;
- Se a instrumentação de processadora está comprovadamente presente em PROD: **SIM / NÃO / INCONCLUSIVO**;
- Se os widgets de Origem podem entrar integralmente no lote 1: **SIM / PARCIALMENTE / NÃO / INCONCLUSIVO**;
- Quais são as verificações manuais de maior prioridade;
- Se o JSON de PROD está pronto para revisão ou ainda não publicável;
- Confirmação explícita: **nenhum PUT/POST/PATCH/DELETE, publicação, deploy, push ou modificação de UAT/PROD foi realizado**.

## 10. Parada obrigatória

**Pare após produzir o diagnóstico, as queries delegadas e os candidatos locais.**

A autorização para preparar candidatos **não é autorização para publicar em PROD**.

Somente depois da conferência das evidências no Log Explorer, revisão humana do lote e comparação com o estado remoto de PROD deverá ser criado e executado um **prompt posterior de publicação controlada**. Essa etapa posterior exigirá autorização expressa, backup, definição de rollback e validação pós-publicação.

Não dê início a outro indicador antes de apresentar a decisão de elegibilidade de produção.
