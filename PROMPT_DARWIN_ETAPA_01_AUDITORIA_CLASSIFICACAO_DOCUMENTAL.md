# PROMPT — DARWIN MONITOR | ETAPA 01 DE 04
## Auditoria e classificação documental — escopo reduzido

### Papel
Atue como auditor técnico de documentação e arquiteto de observabilidade. Sua missão NESTA ETAPA é INVENTARIAR, LER, ANALISAR E CLASSIFICAR a documentação existente do projeto Darwin Monitor, preparando uma revisão controlada nas etapas seguintes.

**Esta é a Etapa 01/04. Não execute revalidação técnica aprofundada, reescrita documental, arquivamento físico nem implementação.**

### Local de trabalho
- Diretório-alvo: `docs/darwin-monitor/` no workspace/repositório aberto.
- Primeiro confirme a existência do diretório e descubra sua estrutura REAL; não suponha que os nomes e arquivos exemplificados neste prompt ainda existam.
- Se o diretório não existir ou não estiver acessível, interrompa a execução, explique o impedimento e não crie uma estrutura substituta.
- Respeite a organização e o padrão atual de documentação (Markdown, links internos, metadados, convenções de nomeação e estrutura voltada a desenvolvedores/Obsidian/RAG).
- Escopo de inspeção principal: documentos e artefatos de referência relacionados a `docs/darwin-monitor/`; analise referências e vínculos para arquivos externos somente quando acessíveis e pertinentes. Não modifique arquivos externos.

### Decisão de escopo já tomada com o time
A entrega corrente do Darwin Monitor será limitada aos SEIS indicadores abaixo, desde que correspondam a registros atribuídos à área **SEGUROS** na coluna `Responsável` da planilha de referência.

| ID | Indicador | Aplicação(ões) identificada(s) com o time |
|---|---|---|
| 01 | Índice de Sucesso | Gestão Eventos Conta Cartão |
| 02 | Taxa de Falha | Gestão Eventos Conta Cartão |
| 03 | Taxa de Falha no Cancelamento | Gestão Eventos Conta Cartão; Gestão Eventos Pagamento |
| 04 | Taxa de Reprocessamento | Gestão Eventos Conta Cartão |
| 05 | Parcela Cobrada x Parcela Lançada | Gestão Eventos Pagamento |
| 06 | Origem | Venda Digital |

Regras:
1. A lista acima é uma **decisão de escopo**, não uma prova de que cada fonte, cálculo ou integração já foi validado.
2. A coluna `Responsável = SEGUROS` é um filtro obrigatório da entrega, e deve ser conferida na planilha original. Normalize diferenças triviais de caixa/espaçamento, mas não atribua responsabilidade ausente por suposição.
3. Se a planilha contradisser a seleção dos seis, registre uma **divergência que exige decisão humana**. Não descarte automaticamente um dos seis nem altere a planilha.
4. Indicadores com outro responsável ficam **FORA DO ESCOPO**. Indicadores de SEGUROS não incluídos na lista ficam **BLOQUEADOS PARA ESTA ENTREGA** (não significa inviabilidade definitiva).
5. Registre, quando houver evidência, o motivo conhecido dos bloqueios: necessidade de BI, banco, conciliação de fontes externas, informação não emitida como telemetria, regras pendentes etc. Se o motivo não estiver comprovado, declare **motivo não confirmado**.
6. Não amplie o escopo ao encontrar outros indicadores viáveis.

#### Atenção ao indicador Origem
Existe a hipótese de que "Origem" se refira à **via de processamento do pagamento da contratação do seguro**, possivelmente Pismo ou DXC. Também pode significar origem/canal de contratação. **Isso não foi confirmado.**
- Classifique essa definição funcional como PENDENTE DE VALIDAÇÃO até localizar evidência inequívoca.
- Não apresente Pismo/DXC como conclusão comprovada.
- Registre a diferença entre *origem do contrato*, *canal de venda* e *via de processamento do pagamento*.
- Verifique como os documentos descrevem o indicador e assinale divergências.

### Objetivo específico da Etapa 01
Entregar um diagnóstico DOCUMENTAL que responda:
- Quais arquivos existem, onde estão, para que servem e a quais indicadores/assuntos se referem?
- Quais são os documentos ativos de referência e quais são registros históricos, duplicados, revisões intermediárias, mistos ou desatualizados?
- Onde existem afirmações conflitantes, escopo antigo, nomenclaturas diferentes, links quebrados, referências circulares ou múltiplos documentos reivindicando ser a versão vigente?
- O que deve permanecer, o que precisará ser revisado parcialmente, o que é candidato a arquivo histórico e o que depende de decisão humana?
- Quais documentos precisarão ser revalidados na Etapa 02 e atualizados na Etapa 03?

**Nesta etapa, não é permitido afirmar que uma conclusão documental foi "validada no código" ou "confirmada no Datadog" sem prova explícita já localizada e referenciada.** O objeto desta etapa é classificar o acervo, não executar o teste de viabilidade de indicadores.

### Cobertura mínima de auditoria
Inspecione recursivamente o diretório real, inclusive, se existirem:
- `analises-por-indicador/`: análises individuais, evidências e conclusões.
- `indicadores/`: definições, regras, fontes e relações com planilha.
- `dashboards/`: MVP, dashboard-alvo, indicadores adicionais, revalidações e revisões de widgets.
- `planos/`: instrumentação, correlação, métricas e eventuais coletores.
- Arquivos da raiz: README, arquitetura e fontes, gaps, próximos passos, checkpoints JSON, imagens, planilhas e PDFs de referência.
- Arquivos com nomes que indiquem versões, revisões ou revalidação (por exemplo, `rev2`, `revalidacao`, `dashboard-mvp`, `dashboard-alvo`), SEM presumir obsolescência pelo nome.

Inclua arquivos não-Markdown no inventário e indique quando não puder verificar o conteúdo. Planilhas, PDFs e imagens devem ser tratados como referências **somente leitura**. Quando houver informações suficientes, identifique qual planilha/aba/coluna sustenta o filtro `Responsável`.

### Procedimento obrigatório

#### Passo A — Inventário completo
1. Liste todos os arquivos com **caminhos relativos completos**.
2. Identifique tipo de artefato, assunto, indicadores citados, finalidade e relacionamentos/referências.
3. Registre também as fontes originalmente fornecidas pelo Negócio, que devem permanecer preservadas como documentação de referência mesmo quando descrevem indicadores fora da entrega.
4. Diferencie *não inspecionado por limitação técnica* de *inspecionado e não encontrado*.
5. Não gere um relatório por arquivo; toda a saída deverá convergir para um documento central.

#### Passo B — Classificação por CONTEÚDO
Classifique cada arquivo em exatamente uma recomendação principal, com observações adicionais quando necessário:

- **MANTER_ATIVO**: documento pertinente ao novo escopo e com papel canônico claro; pode ter pendências a revalidar posteriormente.
- **REVISAR_PARCIALMENTE**: documento misto (indicadores ativos e não ativos), com trechos úteis que devem permanecer após revisão.
- **REVALIDAR_TECNICAMENTE**: afirmações essenciais ao escopo dependem de evidências na aplicação/Datadog que ainda precisam ser verificadas na Etapa 02.
- **CANDIDATO_ARQUIVO**: conteúdo histórico, substituído, versão intermediária, desatualizado ou integralmente fora do escopo operacional; **não mover nesta etapa**.
- **REFERENCIA_HISTORICA**: fonte original de requisitos, planilha, evidência histórica ou material de origem que deve ser preservado e identificado como tal.
- **DECISAO_PENDENTE**: situação ambígua em que não é seguro determinar destino sem validação humana.

Não atribua `MANTER_ATIVO` apenas porque o título parece adequado. Em documentos mistos, prefira revisão parcial a arquivamento integral quando houver conteúdo válido. Se um arquivo servir a várias categorias, escolha uma classificação primária e registre aspectos secundários.

#### Passo C — Detecção de sobreposição e inconsistências
Procure e relacione:
- Documentos com o mesmo tema e conclusões diferentes.
- Definições divergentes do mesmo indicador (numerador, denominador, janela temporal, status de evento, duplicação/retry, fontes).
- Afirmações de disponibilidade/instrumentação que não apresentem evidência rastreável.
- Planos de instrumentação associados apenas a indicadores que saíram do escopo.
- Dashboards/queries/widgets referentes a indicadores bloqueados ou outros responsáveis.
- Referências a caminhos inexistentes e links relativos possivelmente quebrados.
- Arquivos que aparentem ser a "versão final" mas sejam contraditos por revalidações posteriores.
- Documentos cujos dados provêm de hipóteses da IA sem confirmação.

Registre os conflitos com o **caminho completo dos arquivos**, trechos ou seções (e linhas quando possível), natureza da divergência, impacto e ação indicada na próxima etapa. Não resolva disputas por mera recência de modificação.

#### Passo D — Preparação das etapas seguintes
Indique para cada arquivo ou grupo de arquivos:
- **Etapa 02 (revalidação técnica):** quais hipóteses devem ser confrontadas com código, integrações, banco, observabilidade ou responsáveis. Cite aplicação e evidências a procurar, sem inventar nomes de repositórios.
- **Etapa 03 (consolidação):** qual deverá ser o documento canônico, quais conteúdos incorporar ou corrigir e quais definições de dashboard atual/MVP, dashboard-alvo e instrumentação precisam ser harmonizadas.
- **Etapa 04 (arquivamento):** quais arquivos são candidatos a `arquivo/`, quais links precisam ser atualizados e quais fontes originais devem permanecer acessíveis.
- Aponte dependências de aprovação de Negócio/PO sem tratá-las como problemas solucionáveis pela IA.

### Arquivo ÚNICO de saída
Crie ou atualize **somente** o arquivo:

`docs/darwin-monitor/AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md`

Se já existir, revise-o no lugar, evitando `rev2`, `final`, `novo` e outros derivados.

Estrutura obrigatória do relatório:
1. **Metadados:** etapa, data da auditoria (data observada no ambiente), diretório auditado, referência de escopo e ressalva de que não houve revalidação técnica.
2. **Resumo executivo:** quantidade de arquivos auditados, principais sobreposições, riscos e decisões pendentes.
3. **Escopo ativo e critérios de exclusão:** tabela dos seis indicadores, aplicação(ões) e confirmação/pendência da coluna Responsável.
4. **Inventário completo:** tabela com caminho, tipo, finalidade, indicadores relacionados, classificação, justificativa objetiva e ação recomendada. Contagens devem bater com as linhas.
5. **Documentos canônicos e duplicidades:** matriz assunto -> documento recomendado -> outros documentos relacionados -> pendências.
6. **Inconsistências e contradições:** origem, evidência, impacto, prioridade e etapa de tratamento.
7. **Matriz dos seis indicadores:** documentação existente, fontes alegadas, dashboards documentados, instrumentações sugeridas, lacunas, grau de evidência DOCUMENTAL e verificações futuras. Use `NÃO VERIFICADO` quando necessário.
8. **Indicadores bloqueados/fora de escopo:** justificativa documentada; não afirmar impossibilidade definitiva.
9. **Plano de ação ordenado para Etapas 02, 03 e 04:** arquivos-alvo, ações, dependências, critérios de conclusão.
10. **Pendências de decisão humana:** questões para Negócio/PO, Engenharia, observabilidade e responsáveis pelas aplicações.
11. **Conferência de integridade da auditoria:** arquivos lidos, arquivos não legíveis, vínculos não resolvidos e mudanças realizadas.

Produza um relatório prático, conciso onde possível, mas **sem omitir arquivos do inventário**. Prefira tabelas navegáveis e links relativos compatíveis com o padrão existente do projeto.

### Limites de autorização — INEGOCIÁVEIS
- **PERMITIDO:** ler, pesquisar, comparar, classificar, registrar referências, e criar/atualizar APENAS o relatório acima.
- **PROIBIDO:** modificar, mover, renomear, arquivar ou excluir qualquer outro documento.
- **PROIBIDO:** alterar aplicação, banco, dados, infraestrutura, dashboards, monitores, logs, configurações do Datadog, pipelines, automações ou credenciais.
- **PROIBIDO:** criar documentos auxiliares, históricos de execução, arquivos de versão, cópias de backup ou múltiplos relatórios.
- **PROIBIDO:** declarar dados, queries, dashboards, monitores ou instrumentações como validados com base apenas no texto de documentação anterior.
- **PROIBIDO:** iniciar as Etapas 02, 03 ou 04 sem solicitação posterior.
- Não imprimir credenciais, tokens, segredos ou dados pessoais em relatório ou resposta.
- Não fazer commit/push sem autorização explícita, mesmo que o ambiente ofereça Git.
- Se houver informação incompleta, documente a incerteza, indicando o que falta e onde validar.

### Critérios de aceite da Etapa 01
Só considere a etapa concluída se:
- [ ] Todos os arquivos do diretório-alvo tiverem entrada no inventário (inclusive os não legíveis, identificados como tal).
- [ ] O relatório respeitar o recorte dos seis indicadores e diferenciar `Responsável = SEGUROS` de outras responsabilidades.
- [ ] As fontes originais do Negócio forem preservadas como referências.
- [ ] Documentos mistos e conflitos entre revisões forem identificados.
- [ ] O indicador Origem permanecer hipótese até validação funcional/técnica.
- [ ] Existir recomendação justificada de manter, revisar, revalidar, arquivar ou decidir para cada arquivo.
- [ ] Houver plano de trabalho específico para as Etapas 02, 03 e 04, sem executá-las.
- [ ] Nenhum arquivo existente tiver sido alterado além do relatório autorizado.

### Resposta final obrigatória no terminal/chat
Ao terminar, informe:
1. Caminho do relatório gerado.
2. Quantidade de arquivos e classificação resumida.
3. As cinco principais inconsistências/riscos documentais.
4. As decisões ainda pendentes de confirmação humana.
5. Uma declaração explícita do que FOI e do que NÃO FOI alterado.

**Aguarde a revisão e aprovação do usuário antes de executar a Etapa 02.**
