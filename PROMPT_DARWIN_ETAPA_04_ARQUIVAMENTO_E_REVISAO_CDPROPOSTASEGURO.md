# DARWIN MONITOR — ETAPA 04/04
## Arquivamento controlado, limpeza de referências e verificação da instrumentação em homologação

### 0. PAPEL, OBJETIVOS E ESCOPO

Atue como engenheiro sênior de software e observabilidade, responsável por curadoria documental e por uma **verificação técnica somente leitura** da instrumentação implementada.

A Etapa 03 foi relatada como **CONCLUÍDA**. A Etapa 04 deve organizar o acervo do Darwin Monitor, retirar da navegação ativa os documentos superados, preservar integralmente o histórico e garantir que os documentos canônicos continuem corretos e com links válidos.

**Informação nova e prioritária do usuário:**
- A instrumentação de `cdpropostaseguro` **já se encontra em ambiente de homologação**, segundo o usuário.
- O usuário quer promovê-la a produção **o quanto antes**, obedecendo os controles do projeto.
- A implementação pode ser examinada nas branches `feature/SEGNP2-5340` de:
  - `sboot-pseg-base-orch-gestao-eventos-conta-cartao`
  - `sboot-pseg-base-orch-gestao-eventos-pagamento`

**Consequência obrigatória:** não deixe o plano documental tratar indiscriminadamente essa instrumentação como "ainda não desenvolvida". Verifique o código das duas branches e classifique com precisão `planejada / implementada na feature / implantada em homologação (informação do usuário, se não verificada tecnicamente) / validada em homologação / liberada para produção / implantada em produção`, conforme a evidência efetiva disponível. Não transforme a informação de que está em homologação em prova automática de aprovação para produção.

### 1. ESCOPO FUNCIONAL PRESERVADO

**Indicadores ativos de Seguros:**
1. Índice de Sucesso — Gestão Eventos Conta Cartão.
2. Taxa de Falha — Gestão Eventos Conta Cartão.
3. Taxa de Falha no Cancelamento — Gestão Eventos Conta Cartão e Gestão Eventos Pagamento.
4. Taxa de Reprocessamento — Gestão Eventos Conta Cartão.

**Indicadores pendentes de decisão da PO/Negócio:**
5. Parcela Cobrada × Parcela Lançada — Gestão Eventos Pagamento; na planilha, `bvseg`.
6. Origem — Venda Digital; na planilha, `bvseg`, com regra funcional ainda ambígua.

Preserve os seis como recorte documental. Não reative outros indicadores. Os dois `bvseg` **não devem desaparecer** apenas por não estarem no MVP dos quatro de Seguros. Não atribua `Origem` a MOB sem prova.

### 2. PREPARAÇÃO E FONTES

1. Identifique a **raiz real do projeto `darwin-monitor/`** usando o diretório atual. Se já estiver nessa raiz, NÃO crie `docs/darwin-monitor/` dentro dela.
2. Consulte `README.md`, `REVALIDACAO_TECNICA_ETAPA_02.md`, `AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md`, documentos canônicos de `indicadores/`, `dashboards/`, `planos/`, `01-arquitetura-e-fontes.md`, `gaps-e-proximos-passos.md` e outros arquivos transversais **somente se realmente existirem** e forem pertinentes.
3. Leia `README_FERRAMENTAS_AMBIENTE.md` caso precise reutilizar procedimentos verificados; não confunda o guia com evidência de telemetria.
4. Inspecione o conteúdo atual de `analises-por-indicador/` e documentos candidatos ao arquivo **antes de qualquer movimentação**.
5. Consulte `git status` e os diffs existentes, caso seja um repositório Git. Não sobrescreva trabalho local de terceiros. Não faça commit/push.
6. Respeite os padrões de documentação já existentes (Markdown com links relativos, tabelas, notas de status). Não crie uma segunda estrutura canônica.
7. **Hierarquia de evidência:** código/branch confirmado para implementação; logs/Datadog para telemetria observada; pipeline/ambiente para implantação; planilha e PO para definição de negócio; documentos históricos como contexto, não como fato vigente.

### 3. VERIFICAÇÃO DIRIGIDA — `cdpropostaseguro` NA BRANCH `feature/SEGNP2-5340`

**Esta é uma verificação vinculada à atualização documental e ao planejamento de release; NÃO é trabalho de implantação.**

Localize os **dois repositórios reais**. Não presuma paths de sistema; valide-os.

Para cada repositório:
- Confirme que a branch `feature/SEGNP2-5340` está disponível. Prefira inspecioná-la **sem alterar o checkout atual** (por exemplo, `git show`, `git diff`, `git log` com refs existentes). Não faça checkout, pull, fetch, merge, rebase ou qualquer mudança no workspace sem necessidade e autorização específica.
- Identifique commit(s), arquivos/classe/método e diferenças de instrumentação relevantes em comparação com a branch de referência usada pelo projeto, **somente se ela puder ser determinada com segurança**. Não invente qual branch equivale à produção.
- Verifique **o que foi realmente implementado** para `cdpropostaseguro`: logs de início/fim, eventos de sucesso/falha, eventuais métricas, spans/traces, tags, parâmetros de correlação, tratamento de exceção, retries, idempotência, exposição de resultado funcional e mudanças de configuração.
- Confirme se a instrumentação cobre ambos os lados dos fluxos relevantes e se há consistência entre Conta Cartão e Pagamento. Não presume cobertura completa apenas porque existe classe ou variável com esse nome.
- Identifique quais indicadores do recorte se beneficiam diretamente da instrumentação, com referência de código; diferencie observação de inferência.
- Busque evidência de testes existentes, CI e critérios de qualidade para as branches; se não houver acesso a resultados de execução, diga `NÃO VERIFICADO`.
- Considere riscos para produção: logs sensíveis (CPF, dados de cartão, tokens), cardinalidade de tags, volume/custo, sobrecarga, degradação de processamento, confidencialidade, compatibilidade entre versões e consistência do correlation ID.
- Não execute testes que modifiquem dados, reprocessamentos, jobs, filas ou integrações em ambientes compartilhados.
- Verifique, **se houver fonte autorizada acessível**, evidência de homologação: pipeline/artefato/commit implantado, monitoramento de homolog, smoke tests e aprovação de QA. Caso não haja, informe `INFORMADO PELO USUÁRIO — HOMOLOGAÇÃO NÃO COMPROVADA POR FONTE TÉCNICA NESTA EXECUÇÃO`, sem negar a informação fornecida.
- Não confunda instrumentação **implantada** com disponibilidade de queries, fórmula e dados completos dos indicadores. Ela pode habilitar observabilidade sem tornar todos os indicadores calculáveis.

**Produto dessa verificação:** síntese técnica rastreável da situação de `cdpropostaseguro` e um **checklist curto de prontidão para produção** com itens confirmados, pendentes e donos responsáveis.

**Regra crítica:** não realizar deploy, publicar imagem/artefato, abrir merge/PR, modificar pipeline, alterar flags, acionar jobs ou liberar produção. A promoção depende dos responsáveis autorizados e das validações aplicáveis. Não declarar `PRONTO PARA PRODUÇÃO` se faltar evidência de testes/QA, consistência de commits, aprovação ou rollback.

### 4. INVENTÁRIO INDIVIDUAL DE DOCUMENTOS — ANTES DE ARQUIVAR

Faça um inventário em memória ou na saída da sessão, classificando **cada arquivo** relevante como:

- `CANÔNICO ATIVO` — fonte atual que deve permanecer na árvore ativa;
- `REFERÊNCIA NECESSÁRIA` — informação histórica ou evidência usada por documentos atuais; manter acessível, sem quebra de links;
- `CANDIDATO A ARQUIVO` — informação consolidada ou obsoleta, após conferência individual;
- `PENDENTE DE DECISÃO` — não mover por falta de certeza.

Para cada candidato, identifique:
1. caminho exato existente;
2. finalidade e indicador relacionado;
3. **onde seu conteúdo válido está preservado** no(s) documento(s) canônico(s);
4. links de entrada e de saída (Markdown, caminhos, links referenciados em planos e READMEs);
5. decisão `MOVER` ou `MANTER`, com justificativa técnica;
6. impacto da movimentação.

**Não arquive por pertencer a uma pasta inteira sem verificar cada arquivo.**

### 5. CANDIDATOS SUGERIDOS — NÃO SIGNIFICA MOVER AUTOMATICAMENTE

A conclusão da Etapa 03 apontou:

- `analises-por-indicador/` (avaliar arquivo a arquivo);
- `dashboards/01-indice-sucesso-recorrencia.md`;
- `dashboards/revalidacao-widget-1-1.md`;
- `dashboards/revalidacao-widget-1-1-rev2.md`;
- `checkpoint_dashboard_raw.json`;
- documentos em `indicadores/` fora dos seis selecionados.

**Condições específicas:**
- Histórico de revalidação de widget pode conter uma query ou decisão não transposta para a versão canônica. Preserve ou consolide a referência antes de mover.
- `checkpoint_dashboard_raw.json` pode ser evidência de uma configuração antiga ou ter uso externo. Não descartar e não mover sem confirmar que não é consumido por nenhuma rotina e que a procedência está clara.
- Indicadores fora dos seis são **fora da entrega atual**, não necessariamente obsoletos. Se contiverem informação de domínio ou forem referências usadas em outras análises, preservar em árvore ativa ou arquivar individualmente apenas com comprovação.
- Planilhas XLSX, PDF e imagens originais que fundamentaram decisões do Negócio são **fontes primárias**; não mover automaticamente.
- `AUDITORIA_ETAPA_01_ESC0PO_DOCUMENTAL.md` (ou o nome real confirmado) e `REVALIDACAO_TECNICA_ETAPA_02.md` são histórico de decisões rastreáveis; preservá-los.
- Instrumentação já implementada na feature/homologação **não é documento obsoleto**; atualize apenas o estado de acompanhamento nos documentos canônicos pertinentes.

### 6. EXECUTAR APENAS ARQUIVAMENTO SEGURO

Esta Etapa 04 AUTORIZA arquivamento **reversível de documentação comprovadamente redundante** sob as salvaguardas abaixo.

1. **Pré-checagem:** não inicie movimentos se houver alterações preexistentes que possam ser sobrescritas; em caso de risco, pare nesse arquivo e relate.
2. Escolha **um único** diretório `arquivo/` na raiz real do Darwin Monitor, reutilizando-o se existir. Se só houver `z_historico/` já estabelecido no projeto, prefira respeitar essa convenção; **não crie ambos**.
3. Preserve ao máximo a estrutura de caminhos relativa dentro do arquivo histórico, por exemplo `arquivo/dashboards/<nome-original>.md`. Não renomeie arquivos para `_old`, `final-v2` ou variações desnecessárias.
4. Mova somente os candidatos cuja informação já esteja consolidada e cuja dependência e referência sejam conhecidas. Para ambíguos, preserve o original e marque como `PENDENTE`.
5. Não excluir definitivamente. Use movimentação reversível e registre **origem -> destino -> justificativa**.
6. Se criar índice do arquivo, use **apenas um README/índice dentro do diretório histórico**, com classificação de histórico, data da movimentação, destino das definições ativas e nota de que o conteúdo arquivado não é a especificação vigente. Não criar relatórios paralelos ou pastas redundantes.
7. Após cada movimento, atualize os links relativos dos documentos ativos e, quando necessário, dos históricos envolvidos; valide que todos os alvos ainda existem. Não alterar links externos sem necessidade.
8. Se um documento histórico estiver referenciado por automação, script, template ou ferramenta e a referência não puder ser atualizada com segurança, **não mova** e reporte.
9. O arquivo histórico deve ficar fora da navegação operacional principal, mas continuar encontrável pelo índice; não gerar múltiplas cópias conflitantes.
10. Não reescreva conteúdo histórico para que pareça ter sido produzido hoje. A versão original deve ser preservada, exceto ajustes estritamente necessários para restabelecer links relativos após movimentação, com observação identificável.
11. Não usar `rm -rf`, exclusões em massa ou operações destrutivas.
12. Se não houver nenhum item seguro, **Etapa 04 pode terminar com 0 movimentos**, produzindo diagnóstico das razões; não force arquivamento apenas para mostrar trabalho feito.

### 7. ATUALIZAÇÃO DOCUMENTAL PONTUAL SOBRE HOMOLOGAÇÃO E PRODUÇÃO

Ao concluir a leitura da branch, **somente se houver documentos canônicos pertinentes já existentes**, ajuste minimamente:

- o plano `planos/01-instrumentacao-cdpropostaseguro.md` (se existir);
- a seção pertinente de `dashboards/03-dashboard-alvo.md` e/ou `dashboards/04-gaps-de-instrumentacao.md` (se existirem);
- o documento transversal de gaps/roadmap que já acompanha próximos passos (se existir).

Registre:
- implementação em `feature/SEGNP2-5340` nos dois repositórios, **apenas com referências de código realmente verificadas**;
- status de homologação como **informação do usuário**, salvo evidência de deployment/QA confirmada;
- quais eventos/metrics/spans estão instrumentados e quais ainda são apenas propostas;
- o que falta para promover a produção, sem inventar prazo, autorização ou aprovação;
- o que ficará para validação no Datadog **após** o deploy;
- se `cdpropostaseguro` viabiliza somente parte do dashboard-alvo, deixar isso explícito.

Não crie nova especificação de instrumentação; **corrija a informação de status onde ela já existe**. Não trate todos os demais gaps do dashboard-alvo como resolvidos só porque a instrumentação está em homologação.

### 8. QUALIDADE, SEGURANÇA E CONTROLE DE ALTERAÇÕES

**Alterações permitidas:**
- mover/organizar documentos e artefatos históricos **somente sob os critérios da seção 6**;
- atualizar links e índices na documentação do Darwin Monitor;
- corrigir pontualmente estado de instrumentação e referências à homologação em documentos canônicos do Darwin Monitor.

**Proibições:**
- não alterar código ou histórico Git dos dois projetos de aplicação;
- não fazer checkout, merges, deploy, alterações de CI/CD, promover homologação/produção, mudar feature flags ou instrumentação;
- não alterar bancos, filas, jobs, dados de clientes, métricas reais, dashboards, widgets, monitores ou alertas no Datadog;
- não alterar relatórios das Etapas 01/02 para reescrever o passado;
- não introduzir segredos, cookies, tokens, headers de sessão, CPFs e dados de cartão em nenhum arquivo;
- não instalar dependências sem necessidade e não editar código para "consertar" o que está fora do escopo;
- não criar documentação histórica paralela, novos planos imprecisos ou arquivos `rev2/final2`;
- não fazer commit/push sem autorização;
- não confundir testes read-only com homologação operacional validada.

Se precisar escolher entre concluir um arquivamento duvidoso ou preservar integridade, **preserve integridade**.

### 9. CHECKS DE CONCLUSÃO

Antes de declarar Etapa 04 concluída, verifique:

- [ ] Uma única fonte documental ativa para cada um dos quatro indicadores de Seguros.
- [ ] Parcela Cobrada × Lançada e Origem seguem rastreáveis e pendentes de PO/Negócio.
- [ ] Documentos históricos só foram movidos após avaliação individual; não houve exclusão permanente.
- [ ] Nenhuma informação técnica válida se perdeu por mover relatórios/queries antigas.
- [ ] Todos os links relativos ativos e histórico essencial continuam acessíveis.
- [ ] `README.md` e índice de dashboards/indicadores não direcionam para versões obsoletas como documentação vigente.
- [ ] A verificação da branch `feature/SEGNP2-5340` foi realizada **em ambos** os repositórios ou seus impedimentos foram documentados.
- [ ] O estado de `cdpropostaseguro` é descrito sem confundir feature, homologação validada e produção.
- [ ] Checklist de produção identifica itens confirmados, pendentes, responsáveis e rollback, sem liberar deploy.
- [ ] A matriz MVP versus alvo continua coerente e não transforma logs de DLQ/erros em taxas sem denominadores.
- [ ] `git diff` e inventário final permitem identificar cada documento movido/alterado e seus motivos.
- [ ] Nenhum código, configuração operacional ou ambiente foi alterado.

### 10. RESPOSTA FINAL OBRIGATÓRIA

Em português, apresente:

1. **Status da Etapa 04:** `CONCLUÍDA`, `PARCIAL` ou `BLOQUEADA`, com motivo.
2. **Arquivamento:** tabela de `origem -> destino`, justificativa e fonte canônica que substitui cada item, indicando quantos foram mantidos por dúvida.
3. **Documentação ativa:** caminhos alterados, checagem de links, duplicações resolvidas e itens preservados.
4. **Verificação `cdpropostaseguro`:** resultados separados por Conta Cartão e Pagamento: branch, commit(s), pontos de instrumentação encontrados e qualidade/limitações das evidências.
5. **Status de homologação:** confirmado por fonte técnica ou apenas informado pelo usuário; informar claramente.
6. **Prontidão para produção:** checklist compacto `CONFIRMADO / PENDENTE / NÃO VERIFICADO` cobrindo build, testes, CI, homologação, observabilidade, compatibilidade, aprovação, plano de rollback e quem deve decidir o deploy.
7. **Impacto nos indicadores:** quais dos quatro melhoram com a instrumentação; quais queries, denominadores e regras continuam pendentes.
8. **Pendências da PO/Negócio:** `bvseg`, Origem, fórmulas/thresholds não definidos.
9. **Integridade:** confirmação de ausência de deleções definitivas, alterações de código e operações no Datadog; relação de diffs e links.
10. **Próximo passo recomendado:** separar claramente o **fluxo de release de produção** (controlado e priorizado) do **trabalho de configuração/validação dos widgets no Datadog**.

**Pare após esta entrega. Não realize deploy, não abra PR, não publique dashboards e não inicie nova etapa sem autorização expressa do usuário.**
