# PROMPT 05J — Publicação Editorial Controlada em UAT e Sincronização Final do Canônico Local

> **Execução:** Gemini CLI, somente no workspace corporativo autorizado.
>
> **SEGURANÇA — REPOSITÓRIO PÚBLICO:** este arquivo contém somente instruções metodológicas e sanitizadas. Não publicar aqui JSONs de dashboard, relatórios privados, nomes de serviços, IDs, URLs internos, queries, logs, códigos da aplicação, métricas, dados de clientes, capturas, cookies, tokens nem informações de ambiente.
>
> **AUTORIZAÇÃO DO RESPONSÁVEL TÉCNICO JÁ CONCEDIDA:** o usuário autorizou expressamente **os dois gates** preparados na etapa 05I: **(A)** atualizar o arquivo canônico local com o estado final validado do dashboard UAT e **(B)** publicar as **melhorias editoriais de nomes e descrições** propostas no candidato B. O usuário informou possuir backup próprio do UAT. **Não solicitar novamente a mesma autorização** se todos os critérios abaixo forem cumpridos. Não há autorização para publicar em produção, alterar métricas/queries, integrar protótipos, modificar aplicações, scripts/pipelines, fazer commits/push ou executar rollback remoto automático.
>
> **Importante:** só quem executa a operação é o Gemini dentro do ambiente corporativo; o ChatGPT coordena e revisa o resultado. A autorização não dispensa gates automáticos de segurança.

## 1. Missão, fontes e parada de escopo

Concluir o pacote editorial e a sincronização local em um ciclo:
**relatório da 05I → GET remoto e validação → backups → revisão editorial de segurança → diff restrito → dry-run seguro → novo GET de concorrência → um PUT UAT → GET pós-PUT e verificação → backup canônico local → sincronização do canônico com o remoto final → relatório privado e parada.**

Ler seletivamente no workspace privado:
1. Relatório da etapa 05I, prefixo `30-` em `darwin-monitor/planos/`, incluindo a tabela editorial de antes/depois;
2. Snapshot remoto da 05I e Produto **A** (candidato a sincronização canônica);
3. Produto **B** (candidato editorial da 05I);
4. Arquivo canônico local existente e últimos relatórios de publicação/validação estritamente necessários;
5. Script corporativo de publicação e modo de validação, **sem alterá-los**.

Identificar exclusivamente nesses arquivos privados o dashboard/organização/ambiente UAT corretos. Validar o destino de forma inequívoca; **nenhum outro dashboard está autorizado**.

Não reabrir auditorias, não investigar logs de novo, não modificar sinal, fontes, fórmulas ou indicadores. As descrições precisam explicar o objetivo executivo sem prometer resultados não medidos, respeitando a responsabilidade operacional da equipe até o ponto de transferência de responsabilidade comprovada.

## 2. Pré-publicação: garantia de integridade e revisão editorial mínima

1. Fazer **GET remoto atual, somente leitura**, salvar snapshot datado e checksum; capturar metadados de versão/alteração disponíveis.
2. Conferir semântica e campos persistentes do **remoto atual × snapshot da 05I/Produto A**. Se houve alteração posterior à base aprovada, **parar sem PUT e sem substituir canônico**, registrar a divergência. Não sobrescrever edição manual nova, mesmo se o usuário já tiver backup.
3. Conferir o **Produto B versus Produto A**. As únicas diferenças propostas aceitáveis são:
   - **títulos dos grupos** no campo de título suportado;
   - **títulos de widgets** no campo de título suportado;
   - **descrições de widgets** no campo `definition.description` somente onde o schema e a UI suportem.
   
   Os demais campos (IDs, tipo, grupo, ordem, layout, widgets, requests/search/queries, fórmulas, agregações, fontes, índices, variáveis de template, filtros, time ranges, precisão, cores, propriedades de UI e metadados funcionais) devem ficar **integralmente intactos**.
4. Confirmar que todos os nomes e descrições correspondem exatamente às propostas **do relatório privado 30**. Não realizar uma nova rodada criativa de renomeações; não inserir `description` em tipos que não renderizam esse campo.
5. Fazer uma **checagem editorial curta e direcionada**, sem nova auditoria:
   - Títulos de grupos evidenciam o indicador executivo e uma breve qualificação, sem associação falsa a indicador único quando o grupo atende a mais de um;
   - Títulos de widgets usam nome/tema legível + sinal exibido, **sem prefixo técnico de código de indicador**;
   - As descrições contam o que o sinal mede (unidade real), para qual objetivo do indicador contribui e, **apenas quando necessário**, qual limite evita erro de interpretação;
   - Atenção a widgets de **DLQ/reprocessamento**: não renomear contagem de mensagens como taxa; não afirmar ingestão, entrega física, consumo, reprocessamento, deduplicação ou conclusão sem evidência concreta para esse marco. Também não confundir sucesso técnico local comprovado com falta de cobertura só porque outra aplicação executa o restante;
   - Preservar o widget principal de cancelamento já publicado e validado no ciclo anterior, inclusive seu sentido e query; não reabrir sua auditoria.
6. **Se houver dúvida semântica material** (nome enganoso, novo campo não suportado, afirmação não comprovada), **não publicá-la como se estivesse aprovada**. Registre precisamente o(s) campo(s) problemático(s) e interrompa sem PUT para decisão do responsável. Não tentar corrigir silenciosamente o Produto B nem publicar versão parcialmente editada fora do candidato aprovado.
7. Garantir backup local imutável do estado remoto **pré-PUT** e do canônico local **pré-sincronização**, com hash e paths; conferir integridade e possibilidade de recuperar os arquivos. A existência de backup do usuário não substitui os backups deste ciclo.

## 3. Montagem do payload e guarda de concorrência

1. Construir um **payload privado temporário** partindo do **GET remoto mais recente** e aplicando apenas os títulos e descrições aprovados do Produto B, por paths/IDs sem alterar os demais valores.
2. Comparar semanticamente remoto × payload: **somente campos editoriais previstos no Relatório 30**, nenhum widget adicionado/removido/reordenado, nenhuma outra diferença funcional.
3. Validar JSON/schema, tipos, IDs únicos, grupos e layout; executar dry-run somente quando comprovadamente **offline, sem PUT e sem alteração do canônico**.
4. Fazer **outro GET imediatamente antes do PUT**, conferir identidade de ambiente e comparar os campos persistentes e metadados de versão com a base usada na montagem. Se houver mudanças, **parar**. Usar mecanismo de concorrência otimista da API quando houver suporte comprovado; não inventar suporte a `ETag`.
5. Preparar resumo em terminal privado com quantidades reais de grupos/widgets, número de títulos e descrições a alterar, checksum do backup, diff permitido, resultado do dry-run e confirmação de destino UAT.

## 4. Publicação editorial controlada — Gate B (autorizado)

Somente com todas as pré-condições aprovadas:
1. Executar **uma única escrita** no dashboard UAT autorizado, pelo mecanismo corporativo aprovado e sem alterar scripts. Se a API exigir PUT integral, montar o payload a partir do remoto atual, mantendo todos os campos persistentes não editoriais. Se não for possível garantir isso, **abortar sem publicar**.
2. Registrar horário, resultado HTTP e detalhes da transação **apenas no relatório privado**.
3. Em timeout, resposta ambígua ou erro de escrita, **não repetir PUT**. Fazer GET de diagnóstico, classificar o estado e interromper sem sincronizar o canônico para um estado incerto.
4. Após PUT bem-sucedido, executar **GET imediato de conferência**. Fazer diff semântico **pré-PUT × pós-PUT**, comparando também o pós-PUT com o payload esperado:
   - todos os títulos/descrições publicados correspondem ao candidato editorial aprovado;
   - **zero** alterações de métricas, queries, fórmulas, filtros, índices, templates, posições, grupos estruturais, IDs, tipo ou demais configurações;
   - sem perda das alterações manuais anteriores ou do widget validado no ciclo anterior;
   - documentar separadamente normalizações automáticas inofensivas da API.
5. Se houver divergências inesperadas, registrar **PUBLICAÇÃO DIVERGENTE** e **não tentar rollback remoto automático**. Preservar backups e solicitar decisão específica para qualquer escrita de recuperação.
6. O GET técnico **não substitui** validação visual do responsável na UI. Registrar validação visual como pendente, não presumir aceite da apresentação.

## 5. Sincronização final do canônico local — Gate A (autorizado)

**Fazer após a publicação editorial e após GET pós-PUT comprovadamente íntegro**, para evitar que o canônico fique imediatamente desatualizado.

1. Conferir backup imutável do canônico anterior (checksum, data e caminho).
2. Preparar uma cópia local do **remoto pós-PUT efetivamente validado** e converter apenas o mínimo exigido pelo formato canônico já existente, se houver normalizações documentadas, **sem mudança semântica**.
3. Garantir que a nova versão do canônico inclui tanto o widget previamente validado quanto os títulos/descrições editoriais recém-publicados, além de todas as demais configurações preservadas.
4. Validar equivalência semântica canônico novo × remoto pós-PUT. Fazer substituição **atômica e segura** do arquivo canônico local, mantendo backup e sem editar snapshots históricos. Registrar diff e hashes.
5. Se a publicação **não aconteceu** ou a conferência GET pós-PUT **falhou**, **não sobrescrever canônico** por um candidato hipotético; registrar **sincronização pendente**. Se a escrita remota foi bem-sucedida, mas a atualização canônica falhou, informar **UAT PUBLICADO / CANÔNICO PENDENTE**; não executar rollback remoto nem nova publicação.
6. Não realizar commit, push ou publicação em produção.

**Nota:** o Produto A da etapa 05I era fiel ao remoto *anterior* às mudanças editoriais. Não usá-lo, isoladamente, como versão final do canônico após o Gate B. O ponto de verdade final é o **GET remoto pós-PUT validado**.

## 6. Evidências e relatório privado

Criar novo relatório em `darwin-monitor/planos/` com prefixo **31-** (por exemplo `31-publicacao-editorial-uat-e-sincronizacao-canonico.md`) e snapshots/backup locais novos, sem sobrescrever relatórios e candidatos históricos.

Registrar:
- autorização específica para ambos os gates, destino e fronteiras da execução;
- GETs, timestamps, metadados, hashes, backups e conferência de concorrência;
- tabela **antes/depois** por grupo/widget/descrição, com o indicador correspondente;
- diff exato Produto A × B, remoto pré-PUT × payload, pré-PUT × pós-PUT, remoto pós-PUT × canônico atualizado;
- validação de integridade das queries/métricas e de layouts e das mudanças do ciclo anterior;
- resultado da única publicação, caso feita, e do GET posterior;
- resultado da sincronização atômica do canônico ou bloqueio e razão;
- arquivos locais criados/modificados, integridade dos backups, declaração de produção/Java/scripts/Git intactos;
- status pendente de **validação visual do usuário**.

## 7. Resumo e parada

Encerrar no terminal com exatamente um status claro, conforme o caso:
- **UAT EDITORIAL PUBLICADO E CONFERIDO POR GET / CANÔNICO SINCRONIZADO LOCALMENTE / VALIDAÇÃO VISUAL PENDENTE**;
- **NÃO PUBLICADO — NO-GO / CANÔNICO PRESERVADO**;
- **PUBLICAÇÃO DIVERGENTE OU INDETERMINADA — SEM NOVO PUT / CANÔNICO PRESERVADO**;
- **UAT PUBLICADO / CANÔNICO PENDENTE**, se a sincronização local falhar depois de uma publicação validada.

Mostrar caminhos privados de relatório, backups, snapshot pós-PUT, canônico e número real de mudanças editoriais aplicadas. **PARE.** Não iniciar produção, outras alterações operacionais, rollback remoto, commits, nova consulta de logs ou etapa posterior sem nova diretriz.
