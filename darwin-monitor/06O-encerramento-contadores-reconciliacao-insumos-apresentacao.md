# PROMPT 06O — Encerramento dos Quatro Contadores, Reconciliação UAT/PROD e Insumos para Apresentação

> **Executor:** Gemini CLI no workspace corporativo autorizado. Esta é uma etapa de **consolidação e documentação**, posterior à implementação local dos quatro contadores e à **importação manual já realizada pelo operador** em UAT e PROD.
>
> **SEGURANÇA:** esta instrução reside em repositório público e contém apenas metodologia sanitizada. **NÃO** publicar aqui nomes de serviços, eventos, atributos, consultas, IDs/URLs de dashboards, logs, dados operacionais, código ou planilhas e relatórios corporativos, JSONs de UAT/PROD e hashes privados. A execução, os backups e os relatórios devem permanecer exclusivamente no workspace corporativo local.
>
> **FATO COMUNICADO PELO RESPONSÁVEL:** os **quatro novos contadores operacionais** propostos e preparados na etapa anterior foram importados **manualmente** por ele em **UAT e PROD**. Ele confirmou que **todos estão funcionando e mostrando números coerentes**. Registre isso como **homologação funcional declarada pelo operador**, sem pedir nova importação, refazer consultas já aprovadas ou alegar que o Gemini executou GET em ambientes remotos. A reconciliação com JSONs exportados é uma prova documental **distinta** dessa homologação.
>
> **DIRETRIZ OPERACIONAL RATIFICADA:** o Darwin Monitor será usado principalmente por desenvolvedores para diagnosticar **falhas de negócio, erros técnicos, gargalos, retentativas e saúde dos componentes de sua responsabilidade**. Encaminhar solicitação e receber retorno positivo do parceiro é um **sucesso local**; retorno negativo ou erro local observável configura falha na fronteira correspondente. Não exigir comprovação de desfecho financeiro externo para reconhecer esse sucesso local, mas não apresentar sucesso local como liquidação fim a fim. Os quatro novos widgets são **volumes individuais**. **NÃO calcular taxas nem fórmulas neste ciclo.** A possibilidade de `total = sucesso + erro` depende de populações compatíveis, contagem não duplicada e estados de conclusão; será avaliada separadamente caso o responsável queira.
>
> **AUTORIZAÇÃO EXATA:** leitura local de documentação e JSONs, inventário e diffs, snapshots imutáveis, sincronização **condicional e segura** de referências locais após equivalência demonstrada, relatório técnico privado e **um insumo documental organizado para a futura apresentação do dashboard**. Não publicar/importar novamente, não editar dashboards ou widgets remotamente, não modificar consultas, fórmulas, código Java, instrumentações, monitores ou infraestrutura. **Git somente LOCAL e de leitura:** nenhum `git add`, `commit`, `push`, `reset`, `restore`, `checkout`, `stash` ou limpeza destrutiva.

## 1. Objetivo e ordem de execução

Concluir **num único ciclo local**:
1. registrar o aceite funcional humano dos quatro novos contadores em UAT e PROD;
2. identificar **exports oficiais pós-importação** dos dois dashboards e compará-los aos candidatos locais da etapa anterior;
3. reconciliar com segurança o **canônico UAT** e o **snapshot/referência PROD**, exclusivamente quando a equivalência estiver comprovada;
4. verificar o estado final esperado de **25 widgets de dados por ambiente** (21 anteriores + 4 novos), sem regressões;
5. registrar insumos para a **revisão futura da documentação de apresentação**, identificando por que os novos widgets existem, o que medem e o que não medem;
6. entregar o relatório e interromper a execução. **Não iniciar a documentação final da apresentação nem novos monitores** nesta fase.

Não converter esta consolidação em novo discovery, nova rodada de análise dos 21 widgets antigos ou uma sequência de microaprovações desnecessárias. Tratar bloqueios de UAT e PROD de modo independente.

## 2. Inventário privado e verificação do Git LOCAL

Ler de forma direcionada:
- HANDOFF mais recente e relatório privado de construção local dos quatro contadores (etapa anterior, último número de relatório conhecido: 47);
- relatório de auditoria quantitativa imediatamente anterior (último número conhecido: 46) e decisões de escopo;
- **candidatos locais de quatro contadores** construídos para UAT e PROD na etapa anterior;
- canônico UAT atual, snapshot/referência oficial PROD pós-saneamento e seus backups;
- **exports dos dashboards efetivamente publicados depois da importação dos quatro contadores**, se disponíveis na raiz ou pasta privada pertinente do workspace;
- documentação de apoio à apresentação da etapa anterior apenas como **rascunho para revisão posterior**, não como retrato definitivo.

Descobrir os caminhos e versões reais, não presumir que o nome de um candidato indica qual versão está publicada. Conferir `git status --short`, branch, último commit e diffs pertinentes antes de qualquer escrita. Não sobrescrever arquivos alterados ou ignorados sem inventário prévio.

Para cada fonte, registrar no relatório privado: **caminho, ambiente, origem, timestamp quando demonstrável, hash SHA-256, inventário de grupos/widgets e status de autenticidade**.

## 3. Export pós-publicação: prova obrigatória de reconciliação

O responsável confirmou **publicação manual e operação**, mas **não afirmou nesta mensagem ter colocado os novos exports na raiz**. Portanto:
- procurar os exports UAT e PROD **realmente feitos após a importação dos quatro novos widgets**; identificar por conteúdo, ambiente, título/identidade, contagem de widgets e evidências de versão, não apenas pelo nome do arquivo;
- candidato local da etapa anterior **NÃO É** export oficial do dashboard publicado;
- se algum export atualizado **não estiver disponível**, indicar **uma única ação manual específica para aquele ambiente**: exportar JSON pela **interface oficial** do dashboard já atualizado, salvando-o na raiz ou pasta privada informada do workspace;
- marcar esse ambiente `PUBLICAÇÃO HOMOLOGADA PELO OPERADOR — RECONCILIAÇÃO DOCUMENTAL PENDENTE`; **NÃO atualizar sua referência local nem alegar equivalência de exportação**;
- continuar consolidando o outro ambiente se sua prova estiver disponível; também preparar as informações para apresentação independentemente desse bloqueio;
- não usar API sem credenciais autorizadas, cookies de sessão extraídos, extensões bloqueadas, DevTools ou mecanismos alternativos para contornar restrições corporativas;
- não solicitar nova importação nem repetir testes funcionais já confirmados.

Se o export identificar 25 widgets e contiver os quatro novos, isso confirma a **configuração publicada** naquele momento; não equivale a provar novamente que as queries retornam dados reais. A evidência de funcionamento continua sendo a declaração do operador.

## 4. UAT: reconciliação canônica condicional

Comparar, recursivamente:
- **canônico UAT anterior** à adição dos contadores;
- **candidato 06N UAT** preparado e aprovado para importação;
- **export oficial UAT pós-importação**.

Inventariar grupos, nota raiz, variáveis, IDs normalizados, layout, títulos, descrições, queries, filtros, fórmulas, tipos e fontes, inclusive em widgets aninhados.

**Critérios objetivos:**
- contagem esperada: 21 → 25 widgets de dados e **exatamente quatro novos contadores**;
- todos os 21 anteriores preservados em seu significado e configuração, salvo normalização administrativa de export/import comprovada individualmente;
- quatro widgets novos correspondem aos candidatos, com ambientes, tipos, fontes, unidades e consultas corretos;
- nenhum widget de fórmula/taxa foi adicionado;
- nenhuma alteração indevida em grupos, nota, filtros globais, queries antigas ou descrição editorial já homologada.

Se for demonstrada equivalência semântica:
1. produzir backup **imutável** do canônico anterior com hash e nome exclusivo;
2. sincronizar **somente o canônico local UAT**, no formato esperado pelo validador/publicador local, sem copiar indevidamente IDs/metadados da interface;
3. validar o JSON e diff depois da escrita, listando **exatamente** os nós novos/normalizações justificadas;
4. registrar o status `UAT RECONCILIADO — CANÔNICO SINCRONIZADO`;
5. **não commitar**; deixar a revisão do Git local para o responsável.

Se houver divergência material ou atribuição duvidosa de export, **não atualizar o canônico**. Apresentar o diff que impediu a sincronização, sem presumir que o remoto está errado ou tentar corrigi-lo.

## 5. PROD: confirmar a publicação e atualizar referência local sem substituir configurações

Comparar separadamente:
- **snapshot/referência PROD anterior** à promoção dos quatro contadores;
- **candidato PROD 06N**;
- **export oficial PROD pós-importação**.

Confirmar identidade do dashboard existente e todo o inventário. Esperado: 21 → 25 dados; **quatro adições**, sem alteração dos 21 antigos e sem perda de avisos editoriais/descrições/variáveis especificamente aprovadas para PROD.

**Não usar o canônico UAT para sobrescrever PROD.** UAT e PROD podem ter IDs, ambiente, filtros, avisos e configurações legítimas diferentes.

Se houver equivalência semântica suficiente:
- produzir **snapshot oficial local privado, imutável e versionado** do export PROD pós-06N, com hash e identificação da fonte;
- se existir convenção formal de `canônico PROD`, obedecê-la somente mediante prova de que sua atualização é permitida e inequívoca; caso contrário, registrar o snapshot como referência sem inventar um artefato novo obrigatório;
- demonstrar no diff que os 21 antigos não foram mudados pela promoção;
- status `PROD RECONCILIADO — REFERÊNCIA PÓS-06N ARQUIVADA`.

Se houver discrepância, registrar detalhes e interromper somente a reconciliação afetada, sem executar correção remota ou alterar o backup histórico.

## 6. Aceite funcional e natureza dos quatro novos volumes

Registrar formalmente a confirmação humana para os quatro contadores, sem inventar números pontuais ou períodos de teste:

- **Contratações digitais observadas:** contador do evento técnico da esteira digital, com unidade conforme query efetiva;
- **Mensagens/recorrências recebidas:** contador de entradas observáveis no processamento assíncrono, com ressalva de retentativas/reentregas se pertinente;
- **Sucessos locais da recorrência:** volume de resultados positivos na fronteira local; **retorno positivo do parceiro torna a nossa etapa bem-sucedida**, sem declarar liquidação externa;
- **Falhas funcionais da recorrência:** volume de eventos de erro funcional do fluxo; não chamar de `total de erros técnicos e de negócio` se a query cobre somente falha funcional.

Para cada novo widget, extrair do JSON final e do relatório 47:
`ID | grupo | título atual | tipo | fonte | unidade | evento contado | filtro/ambiente | limitações | interpretação útil para dev | indicador de Produto a que ajuda | status UAT/PROD | evidência`.

**Não transformar contagens de eventos em propostas distintas, número de operações de negócio concluídas ou indicadores percentuais sem comprovação.** Os contadores permitem interpretar os fluxos; cálculos futuros somente após validação de compatibilidade de população e janela.

Ao final, reconhecer explicitamente que a homologação visual e numérica foi prestada pelo responsável em ambos os ambientes e que a auditoria offline não repetiu essa inspeção real.

## 7. Insumos atualizados para a apresentação — SEM reescrever o 06L ainda

Gerar **UM novo documento privado de insumos para apresentação**, separado do relatório técnico, contendo:

1. retrato atual do dashboard **pós-06N**, com total efetivamente comprovado e grupos;
2. matriz `Indicador originalmente pedido | Widgets antigos relacionados | Novos contadores associados | O que respondem | O que não respondem | Limitações`;
3. **quatro fichas novas** explicando, em português claro, o que o dev vê, qual sinal o contador captura, em que incidente ajuda, limitações e relação com indicador de Produto;
4. **ordem de demonstração do dashboard**: panorama e volumes → sucesso local → falha funcional e erros técnicos correlatos → investigação → origem e processamento externo fora do recorte;
5. mensagens para a apresentação sobre a distinção `indicador executivo de Produto` vs `monitoramento acionável dos devs`;
6. explicação acessível de por que os contadores foram adicionados: dar volumes absolutos em paralelo a gráficos temporais, sem falsear quocientes;
7. ressalvas que precisam permanecer na narrativa: classificação do sucesso local, diferenças entre erro funcional/técnico, tratamento de retentativas, limites de desfecho externo e No Data;
8. recomendações de alteração na **documentação 06L existente**: seções, tabelas e fichas que precisarão atualização para incluir os quatro widgets e eventual mudança de contagem;
9. checklist de evidências e perguntas frequentes que ainda precisam ser respondidas na apresentação, **sem inventar respostas**;
10. itens que **não são pré-requisito da apresentação atual**: fórmulas futuras, monitores automáticos, novas instrumentações e posterior sanitização baseada no uso real.

Consultar as fontes primárias de indicadores se necessário para conferir o mapeamento, mas não refazer toda a documentação do 06L nesta etapa. **Não modificar o 06L, o HANDOFF ou documentos antigos automaticamente.** O novo documento deverá permitir gerar/revisar a versão final da apresentação em etapa expressamente autorizada a seguir.

## 8. Provas e entregáveis

Gerar **um relatório Markdown privado** no próximo número livre da pasta `planos/`, sugestão:
`48-encerramento-contadores-reconciliacao-uat-prod.md`.

Gerar **um documento Markdown privado** separado de insumos para apresentação, com nome exclusivo em pasta documental já existente, sugestão:
`insumos-apresentacao-darwin-monitor-pos-06n.md`.

**Relatório 48 deve conter**:
- linha do tempo resumida 06M → 06N → importações manuais → 06O;
- declaração de homologação humana de funcionamento em UAT e PROD;
- fontes, hashes e identificação de exports pós-importação, com status por ambiente;
- diff canônico UAT anterior/candidato/export, snapshot PROD anterior/candidato/export;
- contagem esperada 25 e contagem efetiva por ambiente, preservação dos 21 antigos;
- arquivo canônico UAT sincronizado **somente se comprovado** e snapshot PROD armazenado com hashes;
- inconsistências/normalizações do Datadog, pendências e risco operacional;
- resultado do validador local offline/schema e status do Git local antes/depois;
- caminhos exatos dos novos arquivos e dos arquivos antigos afetados com autorização;
- zero publicação remota, zero edição Java/monitores/queries e zero commits/push nesta execução;
- recomendação de única etapa seguinte: **atualizar/terminar a documentação 06L usando os 25 widgets** depois da reconciliação (ou com ressalva de export pendente).

**Inspeção final obrigatória**:
- nenhum dashboard remoto modificado;
- nenhum widget atual reconfigurado remotamente;
- fontes/exports originais mantidos intactos;
- nenhum arquivo sobrescrito sem backup/condição;
- status Git apresentado fielmente e sem commit;
- informação sobre funcionamento atribuída à confirmação do operador, não a GET ou métricas consultadas pelo Gemini;
- somente evidências locais efetivas sustentam o fechamento técnico da reconciliação.

## 9. Estados finais inequívocos e parada

Por ambiente, classificar:
- `PROMOÇÃO 06N HOMOLOGADA E RECONCILIADA` — export oficial pós-importação comprovado, diff compatível e referência local atualizada;
- `PROMOÇÃO 06N HOMOLOGADA PELO OPERADOR — EXPORT PENDENTE` — funcionamento relatado, mas ainda sem export confiável;
- `PROMOÇÃO 06N HOMOLOGADA PELO OPERADOR — DIVERGÊNCIA DE REFERÊNCIA` — há export com alteração material a avaliar.

**Não usar `encerrado tecnicamente` quando houver export pendente ou divergência material**, embora o funcionamento observado possa continuar homologado.

Na resposta final ao operador, dar **tabela UAT/PROD** com contagem, status de reconciliação, arquivo atualizado/snapshot, caminhos do relatório e dos insumos de apresentação, alterações realizadas no Git local (sem commit) e **somente a ação manual estritamente necessária**, se faltar algo.

**PARADA OBRIGATÓRIA:** entregar resultados e parar. **Não importar/publicar, não criar fórmulas, não criar novos widgets, não instalar monitores e não iniciar a versão final da apresentação sem autorização posterior**.