# PROMPT 06G — Consolidar a Homologação P1 em UAT e Preparar Promoção Aditiva para PROD

> **Executor:** Gemini CLI, somente no workspace corporativo autorizado.
>
> **PUBLICAÇÃO DESTE PROMPT:** repositório público contém apenas método sanitizado. Manter **exclusivamente no workspace privado**: nomes de serviços/campos internos, consultas, índices, logs, identificadores de propostas e dashboards, hosts corporativos, JSONs, hashes de artefatos privados, screenshots, relatórios e dados do banco. Não criar commits no GitHub público, nem enviar artefatos corporativos para lá.
>
> **AUTORIZAÇÃO EXPRESSA DO RESPONSÁVEL PARA 06G:** registrar o aceite **visual e funcional em UAT** dos DOIS widgets P1 já importados manualmente pelo responsável; reconciliar, sob as condições de segurança deste roteiro, o JSON canônico **local UAT** com o JSON exportado **após a importação**; preparar um **novo candidato local para PROD** que ADICIONE somente esses dois widgets à base PROD atual preservando os demais. Autoriza criar backups e relatórios locais privados e usar Git LOCAL apenas para leitura de status/histórico/diffs. **NÃO AUTORIZA nova importação, PUT/POST, atualização remota, deploy, mudanças de código, commit local, push, criação de facet ou mudança de qualquer widget PROD existente.**
>
> **ESTADO CONFIRMADO PELO RESPONSÁVEL:** os testes A/B de consulta/agrupamento no Log Explorer PROD foram executados e funcionaram; o candidato local criado no 06F foi **importado manualmente em UAT**; **ambos** os widgets novos foram exibidos e conferidos na UI UAT com dados reais: (A) ranking de ocorrências de retentativa/falha por proposta; (B) stream especializado com identificador estruturado visível. Essa é **validação humana declarada e evidência visual da conversa**, não um GET autenticado nem uma prova de equivalência byte a byte entre JSON remoto e candidato. Não pedir para refazer esses testes.
>
> **DECISÕES PRESERVADAS:** veto homologado aos três filtros P0; os indicadores/streams brutos existentes não devem ser alterados para exigir a presença do identificador. P1 aprovadas e testadas em UAT. P2 (contadores de negócio adequados, fontes externas, outras instrumentações) continua fora do escopo deste prompt.

## 1. Objetivos e ordem obrigatória

**Objetivo A:** registrar formalmente a homologação manual das duas P1, reconciliar a **fonte de verdade de UAT** e preservar o histórico privado.

**Objetivo B:** preparar, exclusivamente de forma local, a **promoção ADITIVA das duas P1** para o dashboard PROD existente, com filtros corretos do ambiente PROD, sem substituir novamente os grupos ou a configuração dos 19 widgets antigos.

**Ordem:** inventário e integridade Git → verificar export de UAT pós-importação → reconciliar canônico somente se houver prova suficiente → preparar candidato PROD a partir de baseline PROD confiável → diff e relatório → PARAR aguardando aprovação do responsável.

Não refazer discovery, não revalidar novamente testes já confirmados, não criar uma cadeia artificial de subetapas. Resolver no mesmo ciclo documental/local o que for possível com os arquivos existentes.

## 2. Inventário dos arquivos e controle por Git LOCAL

Ler seletivamente o último relatório técnico de construção local P1 e o adendo de homologação manual, canônico UAT e candidato 06F; localizar também último backup/export pós-publicação de PROD e eventual export UAT posterior à importação. Os caminhos concretos devem ser descobertos no workspace, sem presumir nomes ou versões.

Antes de qualquer escrita, executar **apenas leitura** no repositório Git local:
- `git status --short`, branch atual, `git log -1` e, quando relevante, `git diff`/diff staged dos caminhos pretendidos;
- registrar se há mudanças locais não relacionadas ou arquivo canônico já modificado;
- **não** dar `git add`, `commit`, `push`, `reset`, `checkout`, `restore`, `stash` nem reformatar arquivos de terceiros;
- não converter esta etapa em tarefa de versionamento remoto: Git é **somente local** e sua operação de commits permanece sob controle do responsável.

Criar snapshots privados imutáveis e hashes dos arquivos de origem antes da reconciliação, sem sobrescrever backups existentes. Se houver alterações preexistentes conflitantes no canônico ou no destino do relatório, **não sobrescrevê-las**; registrar o bloqueio específico e continuar apenas com as atividades independentes e não destrutivas.

## 3. Aceite funcional em UAT — registrar de modo fiel

Adicionar um adendo datado ao relatório privado da etapa de construção 06F ou, se ele estiver congelado, ao relatório novo deste 06G:

- responsável confirmou **manualmente** a importação em UAT do candidato P1;
- confirmou **ambos** os novos widgets exibindo dados no dashboard UAT;
- P1-A apresenta propostas agrupadas com contagens de ocorrências de retentativa/falha; não equivale a taxa nem número de adesões concluídas;
- P1-B apresenta registros com identificador estruturado em coluna;
- stream geral e indicadores brutos devem permanecer preservados;
- o aceite funcional **não é** comprovação automática da igualdade semântica do JSON remoto, que exige export posterior pela UI;
- nenhum teste UAT adicional deve ser solicitado sem motivo técnico novo.

Se houver captura fornecida localmente e autorizada, citá-la somente no relatório privado; caso contrário, usar estritamente a declaração do responsável. Não inventar timestamps, volumes ou screenshots.

## 4. Reconciliação obrigatória de UAT, sem API nem cookies

**Fonte remota validável:** export JSON do dashboard UAT realizado **após a importação manual** pelo operador via interface oficial. Usar arquivo exportado já presente no workspace, se comprovadamente pós-importação. Não usar o candidato 06F como se fosse export remoto.

Se não houver export UAT pós-importação disponível:
- solicitar ao responsável **uma única ação manual**: abrir o dashboard UAT correto na UI, usar **Configure → Export dashboard JSON** e disponibilizar o arquivo exportado no workspace privado;
- marcar `EXPORT UAT PÓS-IMPORTAÇÃO PENDENTE` e **NÃO modificar o canônico** sem esse arquivo;
- pode continuar preparando um candidato PROD provisório com base local válida, mas não o declarar pronto para publicação enquanto a reconciliação de UAT estiver incompleta;
- não tentar GET/PUT com sessão via cookies, DevTools, extensões, Selenium ou credenciais alternativas.

Com export UAT:
1. Conferir identificação do dashboard, ambiente e prova temporal de que o arquivo foi gerado após a importação; evitar confundir arquivo exportado antes/depois ou UAT/PROD.
2. Comparar semanticamente export remoto × candidato 06F: estrutura raiz, grupos, notas, títulos, descrições, layout, variáveis, todas as consultas, campos, tipos, índices e filtros, inclusive em widgets aninhados. O baseline aprovado tinha **19 widgets de dados**; o candidato aditivo deve ter **21**, conferir números reais.
3. Ignorar SOMENTE metadados voláteis ou normalizações comprovadas da UI/Datadog, explicitando cada exceção. **Não** esconder diferenças em queries, títulos, filtros, descrições ou outras mudanças materiais sob "normalização".
4. Confirmar que os 19 widgets antigos e os demais elementos aprovados continuam semanticamente inalterados, e que são **exatamente dois widgets novos**.
5. Se equivalentes, preservar backup/hash do canônico anterior e atualizar **somente o canônico local UAT**, usando como referência a representação remota validada no formato esperado pelo publicador, sem IDs/metadados remotos indevidos. Verificar novamente hashes e diff; não commitar.
6. Se houver divergência material, **PARAR a alteração do canônico** e registrar diferenças objetivas. Não "corrigir" remotamente nem assumir que o candidato está certo.
7. Se não for possível atribuir com segurança o export à versão UAT recém-homologada, classificar a reconciliação como **NÃO COMPROVADA**, sem alegar sincronização.

## 5. Preparação de PROD: adição de apenas dois widgets

**Estratégia de promoção é ADITIVA**, e não novo clone integral UAT → PROD. O dashboard PROD já pode ter diferenças legítimas em relação ao UAT (avisos editoriais, filtros, variáveis, descrições, acessos, grupos e contexto de ambiente). **Preservar integralmente essas configurações existentes**.

Selecionar como base o export/snapshot PROD **mais recente e confiável** obtido após a última atualização, preferencialmente export manual atual via UI. Se somente houver snapshot antigo e não for possível confirmar que representa o estado remoto atual, pode gerar candidato marcado **PROVISÓRIO — BASE PROD NÃO CONFIRMADA**, jamais `PRONTO PARA IMPORTAÇÃO`.

A preparação deve:
1. Validar identidade do dashboard PROD existente e inventário/quantidade dos widgets já presentes. Não assumir que o layout permanece igual ao de UAT.
2. Fazer backup local imutável, hash e identificação da base PROD.
3. Transportar **somente** o design e o conteúdo funcional dos dois novos widgets homologados em UAT, mudando para nomenclatura real do ambiente PROD (ambiente, serviços, storage/índices e demais variáveis efetivas). **Não** fazer substituição cega de texto ou carregar metadados/IDs de UAT.
4. Preservar todos os widgets antigos de PROD **exatamente como estavam**: query, título, notas de disponibilidade, descrições, filtros, timespan, group by, fórmulas, layout e configurações. Não remover avisos existentes nesta etapa; eventual saneamento editorial tem revisão própria.
5. Inserir os novos widgets no grupo correto com layout válido e sem sobreposição; caso exija deslocar/redimensionar itens originais ou alterar metadados compartilhados, **PARAR e apresentar alternativa** antes de fazê-lo.
6. Garantir que não haja duplicação se os widgets P1 já tiverem sido adicionados a PROD por outro caminho; se sim, interromper geração de adição e registrar estado.
7. Identificar P1-A como **ranking de contagens de eventos** de falha/retry agrupados por proposta identificada, não taxa executiva; P1-B como stream filtrado para proposta identificada que **complementa**, não substitui, a visão global.
8. Guardar o candidato exclusivamente no diretório privado de candidatos, com nome exclusivo; nunca sobrescrever o snapshot PROD ou o candidato UAT.

**Não executar nova importação manual pelo Gemini, não tentar publicar com API e não fornecer instrução de publicação como se estivesse automaticamente autorizada.**

## 6. Provas locais e diff com critérios de aprovação

Executar validador local disponível (sem escrita remota) e diffs semânticos legíveis.

**UAT:**
- comparação candidato 06F ↔ export UAT efetivamente publicado, se disponível;
- 19 antigos preservados e +2 novos, sem regressões;
- sincronização do canônico somente após equivalência demonstrada;
- `git diff` local documenta alteração canônica autorizada quando realizada, sem commit.

**PROD:**
- parse/schema JSON, tipos de widget, requests e layout;
- compare baseline PROD ↔ candidato PROD: `+2 widgets` e **zero mudanças em todos os 19 antigos e demais elementos**; conferir a contagem real e não forçar um número se houver diferença prévia;
- nenhum resíduo de UAT nas queries dos dois widgets; toda adaptação respaldada por configuração PROD existente;
- no P1-A, `group by` no atributo estruturado real e contagem de ocorrências; no P1-B, filtro real e coluna configurada conforme schema;
- preservar valores e estados antigos de alertas/avisos, mesmo que haja revisão editorial futura;
- distinguir `SCHEMA/DIFF LOCAL VALIDADO`, `UAT HOMOLOGADO VISUALMENTE`, `BASE PROD ATUAL COMPROVADA`, `CANDIDATO PROD AINDA PROVISÓRIO` e `AGUARDANDO AUTORIZAÇÃO DE PUBLICAÇÃO`.

Se qualquer invariância falhar, não ocultar o diff nem declarar sucesso. Não criar um novo dashboard remoto para contornar impedimentos.

## 7. Relatório e trilha de continuidade

Produzir **um relatório Markdown privado** com próximo número livre na pasta existente de planos, sugestão `41-consolidacao-uat-e-preparacao-promocao-p1-prod.md` (sem sobrescrever existente):
1. Aceite humano UAT de ambos os P1 e fonte dessa evidência;
2. Identidade das fontes e snapshots com datas/hashes (somente no relatório privado);
3. Resultado da comparação export UAT pós-importação ↔ candidato 06F;
4. Se aplicável, backup do canônico antigo e seu novo estado, com `git diff` e sem commit;
5. Identidade do baseline PROD e status de atualização;
6. Especificação das adaptações UAT → PROD apenas para os novos P1;
7. Candidato PROD local, inventário e diff exatamente aditivo;
8. Validações e bloqueios, separando **prova técnica** de **declaração de aceite humano**;
9. Próxima ação manual estritamente necessária, caso falte export UAT/PROD;
10. Registro explícito de **zero ações remotas executadas pelo Gemini, zero alterações Java e zero commits/push**;
11. Trilha posterior em backlog documental: promoção manual de P1 a PROD somente após autorização; observação dos novos widgets em PROD; revisão editorial isolada de avisos conforme telemetria; futuras métricas de negócio não dimensionadas por identificador individual; integrações e reconciliações externas ainda pendentes.

**Estado final deve ser um só, sem ambiguidades:**
- `CANDIDATO PROD PRONTO PARA REVISÃO E AUTORIZAÇÃO` — UAT reconciliado, baseline PROD atual comprovado, diff exclusivamente +2 validado;
- `CANDIDATO PROD PROVISÓRIO` — base ainda carece de export/autenticidade ou UAT aguarda reconciliação;
- `BLOQUEADO POR DIVERGÊNCIA` — mudanças materiais ou integridade falharam.

O executor deve terminar com resumo factual dos itens sincronizados, quantidade de widgets, diffs, arquivos e **o que depende do operador**. Não pedir para repetir validação A/B ou conferência visual já concluída; solicitar export manual específico apenas se necessário.

**PARADA OBRIGATÓRIA:** entregar relatório/candidato local e **AGUARDAR AUTORIZAÇÃO EXPRESSA antes de qualquer publicação em PROD**. A importação eventual deve utilizar somente a UI oficial com a conta autorizada; não recorrer a extração de sessão nem mecanismos alternativos de acesso.
