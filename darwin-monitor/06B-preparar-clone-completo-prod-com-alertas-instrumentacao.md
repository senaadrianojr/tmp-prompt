# PROMPT 06B — Clonar UAT, Classificar Instrumentação e SUBSTITUIR Dashboard Existente em PROD

> **Executor:** Gemini CLI, exclusivamente no workspace corporativo autorizado.
>
> **REPOSITÓRIO PÚBLICO:** este arquivo contém somente método sanitizado. Identificadores de dashboards, URLs corporativas, códigos, queries literais, relatórios, snapshots, nomes de serviços, credenciais e evidências ficam **somente no contexto e workspace privados**. O operador fornecerá **na mesma mensagem de execução do Gemini** o host e ID exatos do dashboard PROD existente a substituir. Nunca inventar esse destino nem inferi-lo de um nome parecido.
>
> **AUTORIZAÇÃO EXPRESSA DO RESPONSÁVEL (11/11/2026):** o responsável autoriza **sobrescrever COMPLETAMENTE um dashboard MVP existente em PROD**, cujo host e ID serão identificados na mensagem privada, com uma cópia integral do dashboard UAT finalizado, adaptada a PROD, incluindo widgets ainda dependentes de instrumentação marcados de forma visível. **Essa autorização abrange uma única publicação controlada no dashboard-alvo e a verificação posterior**, além de toda a preparação local. Não há necessidade de solicitar uma aprovação intermediária se os gates abaixo estiverem satisfeitos. **NÃO** criar outro dashboard, escrever em outro ID/organização, modificar UAT/canônico, publicar código, executar deploy, mudar scripts, fazer commit/push ou fazer rollback automático.
>
> **MUDANÇA DE ESTRATÉGIA:** substitui nesta execução o procedimento antigo de preparação seletiva: agora entregar o clone **completo** e realizar a publicação em **um ciclo**, não dividi-lo em prompts menores. Preservar o histórico do prompt antigo sem segui-lo.

## 1. Objetivo e fonte de verdade

UAT foi encerrado e aceito pelo responsável; utilizar o relatório privado de encerramento mais recente e o **JSON canônico UAT sincronizado** como base, após conferir por GET que o remoto UAT não possui divergências materiais. O dashboard deve conter a estrutura integral (notas, grupos e todos os widgets de dados) e os títulos e descrições editoriais já aprovados. Não reabrir auditorias históricas nem refazer consultas UAT.

**Destino:** um dashboard **PROD já existente**, de versão MVP antiga, que o usuário autorizou explicitamente **substituir por completo**. Confirmar os seguintes dados somente no contexto privado:
- host e ID exatos fornecidos pelo operador (não copiar ID nem host do UAT);
- organização/tenant de PROD que responde pelo dashboard;
- título atual e identidade do recurso obtidos por GET; deve corresponder ao dashboard MVP apontado pelo usuário;
- ambiente real e nomenclatura da tag de PROD (não pressupor `env:prod` se for `env:prd` ou outro valor).

Se o destino privado **não estiver presente, não for acessível por GET ou não corresponder ao dashboard MVP autorizado**, PARAR, sem PUT. Não substituir outro recurso por semelhança de nome. A URL poderá ter slug variável; o ID e o tenant exatos são a referência de destino, mas ambos exigem conferência.

## 2. Escopo funcional

O clone de PROD deve manter os elementos **atuais de UAT**: grupos, notas, widgets e layout, inclusive widgets que ainda não operam por ausência da instrumentação. A cópia deve expor de forma honesta:
- widgets **operacionais e com dados comprovados em PROD**;
- widgets **aguardando instrumentação ou deploy em PROD**;
- widgets cuja **disponibilidade em PROD ainda não foi comprovada**.

Preservar as **sete linhas selecionadas** da planilha já consolidada; não construir indicadores das seis linhas excluídas. Não criar novos widgets ou fórmulas e não refatorar aplicações. Todas as descrições já documentadas devem permanecer e os avisos não devem apagar o objetivo funcional do widget.

**Fronteira de responsabilidade:** sucesso de um serviço sob gestão da equipe deve ser medido até o marco comprovado de execução/transferência sob sua responsabilidade. Não exigir evidência da aplicação externa para reconhecer sucesso local comprovado. Não declarar sucesso de cancelamento quando há apenas sucesso no tratamento técnico de falha; não chamar volume de DLQ de taxa de reprocessamento.

## 3. Inventário curto, por widget (não por grupo)

Ler seletivamente somente o código/relatório necessário, o dashboard atual e evidências já disponíveis. Para TODOS os widgets, inclusive aninhados, formar uma matriz privada:
`grupo | widget | dependência técnica efetiva | presença no código da branch principal | evidência de deploy | evidência de telemetria PROD | status | ajuste de query/env | título/aviso`.

Pontos de atenção:
- correlação estruturada por proposta nos serviços responsáveis;
- processadora/origem no serviço de venda digital **somente se a instrumentação não estiver comprovadamente disponível em PROD**;
- demais campos ou eventos que tornem widgets específicos dependentes de versão ou telemetria.

**Branch principal ≠ versão efetivamente implantada ≠ log observado.** Não afirmar ausência do campo só porque não apareceu em uma janela sem tráfego. Por outro lado, não anunciar "operacional comprovado" apenas porque o código existe na master.

Quando a prova de PROD exigir consultas ao Log Explorer e o Gemini não puder executá-las de modo autorizado/confiável, **não inventar validação nem bloquear automaticamente a cópia**: classificar `PENDENTE DE COMPROVAÇÃO EM PROD` e marcar visualmente. Reportar verificações opcionais para sustentação posterior, sem criar nova etapa obrigatória antes desta publicação.

## 4. Avisos visuais claros e não enganosos

Classificação para títulos e descrições:
1. **OPERACIONAL COMPROVADO EM PROD:** telemetria aplicável e filtros comprovados. **Sem aviso**.
2. **AGUARDANDO INSTRUMENTAÇÃO/DEPLOY:** evidência material de que atributo/versão exigidos ainda não estão implantados. Prefixar título com `⚠ `; acrescentar à descrição `⚠ Aguardando instrumentação/deploy em PROD: [dependência real].`.
3. **PENDENTE DE COMPROVAÇÃO:** não há prova suficiente de deploy e/ou telemetria correspondente. Prefixar título com `⚠ `; acrescentar à descrição `⚠ Disponibilidade em PROD não comprovada: [prova específica que falta].`.

Não marcar como aguardando instrumentação com base apenas na ausência de eventos ou em uma branch desatualizada. Não colocar um aviso em todos os widgets de um grupo indiscriminadamente; avaliar por widget. Se `⚠` não puder ser exibido por limitações reais da UI, empregar fallback textual conciso. **Não inserir elementos de UI ou badges que não existam no schema do dashboard.**

Mesmo com aviso, manter query e visualização originais adequadamente adaptadas. **Não neutralizar ou remover filtros de correlação necessários para fingir que o gráfico funciona**. "Sem dados" num widget pendente não deve ser interpretado como zero falhas ou zero atividade. Onde não houver instrumentação, o aviso deve ser inequívoco.

## 5. Preparação do candidato completo e adaptação de PROD

1. Fazer GET UAT atual e conferir equivalência funcional com o canônico aprovado; salvar snapshot, hash e metadados.
2. Fazer GET do dashboard PROD **exato** a substituir. Salvar antes de qualquer escrita **backup integral, imutável, datado, com SHA256**, contendo sua configuração atual. Verificar que o backup pode ser relido e que o destino corresponde exatamente ao autorizado.
3. **Inspecionar e listar o impacto da substituição integral:** tudo que existir exclusivamente no dashboard MVP de PROD (grupos, tabs, links, filtros, configurações ou widgets antigos) poderá ser perdido. Essa substituição foi autorizada, portanto não é necessário preservar o layout antigo, mas registrar o diff e impedir que a operação alcance outros dashboards.
4. Produzir o novo JSON PROD a partir do UAT, com título que identifique PROD e com a estrutura integral, substituindo **somente os valores relacionados ao ambiente e à semântica aprovada de avisos**. Preservar número e ordem dos grupos e widgets de origem. Remover ID e metadados read-only próprios do dashboard UAT do payload e usar **somente o ID PROD alvo validado na URL do PUT** e nos locais exigidos pelo schema; nunca criar novo recurso.
5. Resolver serviço, ambiente, índices e variáveis com configuração **real do tenant PROD**. Não fazer substituição global cegamente; verificar `env` dentro de queries, URLs e template variables e evitar links para UAT. A falta de prova de telemetria não impede publicação de um widget marcado; **uma query inválida, variável ausente ou filtro de ambiente inventado é bloqueio técnico** para publicar o payload inteiro.
6. Conferir duplicidade de IDs, campos proibidos/read-only, schema do Datadog e ausência de conteúdos de UAT que afetem filtros. Aplicar avisos sem alterar cálculos.
7. Preparar candidato completo e diff estruturado **UAT × candidato PROD** e **PROD MVP atual × payload de substituição**. O primeiro explica adaptações do ambiente e avisos; o segundo demonstra o que será sobrescrito (substituição **integral autorizada**, não diff cirúrgico).

## 6. Gates de segurança obrigatórios, dentro da mesma execução (sem nova aprovação)

- Identidade do dashboard de destino **exatamente igual** à informada pelo usuário, tenant PROD correto, título atual compatível, e GET válido;
- Snapshot PROD pré-PUT completo salvo, checksum validado, e possibilidade de restauração manual documentada;
- JSON candidato inteiramente válido, com grupos/widgets de UAT completos, descrições preservadas, avisos coerentes, queries sintaticamente adequadas a PROD e **nenhuma dependência de ambiente UAT**;
- Validação local/dry-run somente se comprovadamente **offline, sem escrita** e sem alterar UAT/canônico; não modificar publicadores;
- Evidência de que uma publicação para o **ID PROD existente** substitui esse recurso, e não cria outro, nem envia ao tenant errado;
- GET de **concorrência imediatamente antes do PUT**: se o recurso PROD sofreu modificação desde o snapshot usado para montar o payload, **abortar sem PUT**, preservar evidências e reportar, em vez de sobrescrever trabalho paralelo;
- Se suportado de verdade, usar controle de concorrência da API; não inventar suporte `If-Match`.

**Não adicionar um gate novo exigindo prova de logs de todos os widgets.** Pendências de instrumentação conhecidas ou inconclusivas devem ser representadas pelos avisos, conforme autorizado. **Só bloquear por segurança real do recurso, risco de sobrescrita não intencional, invalidez do payload, inconsistência do destino, falta de backup ou divergência de concorrência.**

## 7. Publicação em PROD — uma única escrita autorizada

Quando os gates forem aprovados:
1. Exibir no terminal privado resumo das quantidades reais de widgets/grupos e estados, dashboard-alvo confirmado, backup, hash, diff de substituição, resultado do dry-run e aviso de que o conteúdo anterior será substituído por completo.
2. Executar **um único PUT autorizado** contra o **dashboard PROD preexistente e identificado**, usando o mecanismo corporativo de API já autorizado que suporte atualização por ID. **Não executar POST para criar outro dashboard**. Não alterar script para contornar validações; se o script existente não suportar atualização segura por ID, usar somente API oficial/caminho corporativo já autorizado e comprovado, ou abortar se isso não for possível.
3. Em timeout, 5xx, resposta ambígua ou falha, **não fazer PUT novamente automaticamente**. GET diagnóstico e status preciso `PUBLICADO`, `NÃO PUBLICADO`, `DIVERGENTE` ou `INDETERMINADO`; preservar backups. Sem rollback automático.
4. Se HTTP indicar êxito, realizar **GET imediatamente após o PUT** e comparar com o payload esperado e a origem UAT:
   - mesmos grupos e widgets de dados que UAT, com títulos e descrições adequados a PROD e avisos;
   - queries e variáveis sem referência indevida a UAT;
   - nenhuma alteração externa a esse dashboard;
   - diferenças da API limitadas a normalizações conhecidas.
5. Considerar **concluída tecnicamente** apenas se o GET pós-PUT conferir; marcar **VALIDAÇÃO VISUAL DO OPERADOR PENDENTE** (acessar dashboard em PROD e verificar título, grupos, estado dos avisos e alguns widgets com dados).
6. **Não sincronizar nem alterar canônico UAT**. O JSON candidato PROD e o snapshot pós-PUT podem servir de artefatos privados da versão de produção; não usar canônico UAT como alvo.

## 8. Entrega privada e parada

Criar relatório novo em `darwin-monitor/planos/`, prefixo **35-** (confirmar que o nome está livre), com:
- registro de autorização do operador para **substituição integral** do dashboard MVP PROD alvo, e identidade confirmada por GET;
- snapshots/hashes UAT, PROD pré-PUT e PROD pós-PUT; backup legível;
- matriz de classificação de todos os widgets e seus avisos, com provas e pendências;
- diff UAT/candidato e PROD antigo/novo; impactos de substituir o MVP;
- validação JSON/schema/dry-run e controles de concorrência;
- horário, HTTP/status da única escrita (se executada);
- diff pós-publicação e confirmação de preservação de UAT/canônico/código;
- necessidade de validação visual do usuário e lista objetiva de instrumentações a acompanhar;
- paths exatos dos artefatos privados, sem qualquer push ao repositório público.

**Resultado final em uma linha:** `DASHBOARD PROD SUBSTITUÍDO E CONFERIDO POR GET / VALIDAÇÃO VISUAL PENDENTE`, ou `NÃO PUBLICADO (NO-GO)`, ou `RESULTADO INDETERMINADO — SEM NOVO PUT`, de acordo com o que realmente ocorreu.

**PARE** após essa execução. Não fazer rollback automático, novas escritas, deploy de instrumentação, publicação em outro dashboard, alteração no canônico UAT nem lançamento de uma segunda fase de widgets.
