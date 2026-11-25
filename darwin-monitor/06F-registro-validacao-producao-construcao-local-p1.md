# PROMPT 06F — Registro dos Testes Manuais e Construção Local dos Dois Widgets P1 em UAT

> **Executor:** Gemini CLI, exclusivamente no workspace corporativo autorizado.
>
> **SEGURANÇA DO REPOSITÓRIO PÚBLICO:** este arquivo é apenas um roteiro metodológico sanitizado. Não inserir em GitHub códigos e nomes de serviços internos, atributos e queries literais, IDs/URLs de dashboards, widgets ou clientes, logs, screenshots, JSONs, relatórios privados, caminhos corporativos específicos, números de propostas, segredos ou credenciais. Os artefatos e resultados desta etapa permanecem SOMENTE no workspace corporativo.
>
> **AUTORIZAÇÃO DO RESPONSÁVEL (após a etapa 06E):** registrar sua validação humana **em PROD** dos testes A e B e **CONSTRUIR LOCALMENTE** um candidato JSON de **UAT** com os dois widgets P1 aprovados em nível de desenho. Autoriza-se exclusivamente: leitura dos arquivos e decisões existentes, adendo documental ao relatório privado anterior, criação de um novo arquivo JSON candidato e de um novo relatório privado de preparação, execução de validadores locais e geração de diff. **NÃO** autoriza publicação em UAT/PROD, importação pela UI, PUT/POST, alteração do canônico UAT, alteração do JSON PROD, edição de código Java/instrumentação, mudança de queries/widgets existentes, deploy, commit, push, cadastro de facets, criação de monitores ou acesso a segredos.
>
> **DECISÕES JÁ TOMADAS:** os três filtros obrigatórios P0 anteriores foram **REJEITADOS**, com veto homologado. Os dois desenhos P1 — ranking operacional de propostas com maior atrito/retentativas e stream adicional especializado por proposta — foram **APROVADOS CONCEITUALMENTE**. As propostas P2 seguem fora do escopo.
>
> **NOVA EVIDÊNCIA DECLARADA PELO RESPONSÁVEL:** ele realizou manualmente os testes A e B **em PROD** no Log Explorer e confirmou: (a) chegada de registros pertinentes; (b) atributo de proposta observável; (c) agrupamento selecionável e executado pelo campo estruturado da proposta no teste A; (d) consulta do teste B retornando registros com proposta observável. Isso valida a capacidade de **consulta e agrupamento** no ambiente testado, mas **NÃO comprova** ainda o widget completo em UAT, a adequação numérica dos dados para taxa executiva, nem cobertura universal de todos os eventos. Registrar a comprovação como **depoimento de validação manual do responsável**, sem inventar screenshots, contagens, HTTPs ou provas independentes.

## 1. Objetivo desta execução

Produzir **um único candidato local de UAT com exatamente dois novos widgets P1**, preservando integralmente os widgets de dados e estruturas aprovados no canônico UAT, e apresentar um diff/relatório para autorização humana posterior. Não fragmentar em inúmeros prompts.

O projeto usa os relatórios privados sequenciais do diagnóstico, revisão crítica e homologação anterior. Localizar o relatório mais recente da homologação de P0/P1, confirmar que suas consultas candidatas e restrições coincidem com o que foi testado pelo operador e reutilizá-las. Não refazer o discovery, não repetir desnecessariamente os testes manuais PROD e não inventar queries.

## 2. Fontes privadas e reconciliação inicial

Ler **somente o necessário**:
- relatório privado da homologação anterior (habitualmente o de número 39), incluindo as queries candidatas e o checklist manual;
- relatório anterior de revisão crítica de segurança e eventuais referências pontuais;
- JSON **canônico UAT** encerrado e aprovado, preservado no workspace;
- último snapshot local confiável de UAT, se útil para confirmar que o canônico é a base correta;
- esquema real dos widgets do dashboard existente e exemplos de widgets comparáveis;
- evidências locais de filtros, índices/storage e nomenclatura efetiva de ambiente.

**Fonte de construção:** canônico UAT, não o dashboard PROD. Nunca copiar `env`, IDs ou metadados de PROD como se fossem UAT.

Antes de gerar:
1. Inventariar recursivamente o dashboard base (grupos, header notes, widgets de dados, descrições, layouts, template variables, queries existentes). O último estado conhecido era **4 grupos e 19 widgets de dados**, mas conferir pelo JSON.
2. Criar hash do canônico e registrar sua integridade; **não alterá-lo**.
3. Verificar se o candidato P1 já existe. Não sobrescrever um arquivo de candidato preexistente; escolher novo nome versionado e registrar sua origem.
4. Se existirem divergências materiais que impedem identificar a fonte canônica, parar e explicar; **não buscar cookies nem realizar leitura autenticada por meios bloqueados**.

## 3. Adendo de validação humana — sem exagerar as conclusões

No relatório privado anterior, acrescentar **uma seção de adendo datada e claramente identificada**, sem substituir o conteúdo já homologado:

`Validação pelo responsável em PROD | Teste A: consulta retornou dados, campo observável, agrupamento pelo campo executado | Teste B: consulta retornou dados e coluna/atributo de proposta observável | Meio: Log Explorer com acesso normal | Evidência: confirmação explícita do responsável | Limite: sem comprovação independente de captura, quantidade de grupos, cardinalidade de longos períodos ou comportamento completo do novo widget em UAT`.

Se o adendo já existir, evitar duplicações. Não escrever que houve criação ou publicação de widgets em PROD. Registrar que **desenho P1 + consultas/agrupamento validados** permitem avançar para **construção local**.

## 4. Widget P1-A — Top List operacional de propostas com maior atrito

Construir **somente no JSON candidato local**, dentro do grupo UAT funcionalmente apropriado, usando schema real de Top List suportado pelos widgets atuais:

- Usar os **sinais específicos e queries reais** de retentativa/falha identificados nos relatórios privados e confirmados nos filtros de UAT, nunca termos inventados.
- Fonte: logs/índice/storage corretos para UAT. Não introduzir filtros que eliminem o tráfego dos widgets operacionais antigos.
- Agregação: **contagem de ocorrências dos sinais selecionados**, agrupada pelo atributo estruturado de proposta. Ordenação decrescente, top 10 ou top 20 conforme configuração suportada.
- Especificar título e descrição que deixem explícito: `Ranking de ocorrências de falha/retentativa por proposta com identificador presente` (adaptar sem alterar o sentido); **não** chamar de contagem de propostas únicas concluídas, taxa de falha, resultado final de negócio ou reprocessamentos comprovadamente bem-sucedidos.
- Quando apropriado, reconhecer que registros sem identificador não participam desta visualização especializada, mas continuam preservados nos indicadores brutos/stream global.
- Usar facet/group-by pelo atributo testado pelo operador e validar sintaxe JSON e compatibilidade do tipo de widget/consulta. A confirmação de agrupamento no Log Explorer em PROD não valida automaticamente todos os detalhes de configuração do widget em UAT.
- Evitar criar tag individual de proposta em custom metric ou Log-based Metric; nenhum recurso adicional está autorizado.
- Manter layout/posicionamento independente, sem deslocar nem diminuir widgets existentes. Se o layout não acomodar sem modificá-los, documentar e parar para decisão humana, em vez de alterar o existente.

## 5. Widget P1-B — Segundo stream especializado por proposta

Construir **somente no mesmo JSON candidato local**, no grupo do stream original ou área tecnicamente adequada:

- Criar **novo widget** do mesmo tipo de stream/list realmente aceito no dashboard e manter o stream global **integralmente intacto** (query, colunas, filtros, título, descrição, layout e semântica).
- Consulta especializada restrita à presença do identificador estruturado de proposta, com serviços e ambiente UAT realmente relevantes recuperados do relatório privado anterior.
- Exibir coluna do identificador, campos de contexto operacional já suportados e ordenação útil, respeitando o formato do widget. Não presumir que uma configuração do Log Explorer é automaticamente um campo válido do widget de dashboard.
- Título e descrição explicam que é visão especializada por proposta e **não substitui o stream global**, que permanece responsável por mostrar inclusive falhas de extração, logs malformados e eventos sem chave.
- Preservar todas as ocorrências no stream original; nenhum filtro de proposta é introduzido em widgets existentes.
- Posicionar sem sobrepor ou modificar blocos existentes; não alterar a estrutura editorial aprovada.

## 6. Guardrails de preservação: NÃO implementar P0 nem P2

**Proibido modificar** quaisquer widgets existentes para exigir presença da proposta, especialmente:
- indicadores de volume bruto de entrada/início;
- resultados locais de sucesso;
- falhas, descartes, NACK e DLQ;
- stream de logs global.

Não alterar os indicadores executivos ou fórmulas; não acrescentar `count distinct` como taxa; não somar valores únicos por buckets; não criar métricas dimensionadas por identificador único; não alterar serviços, processadores ou código-fonte; não atuar em reconciliação de banco/DW/BI.

O escopo autorizado é **acréscimo exclusivo dos dois widgets P1**; todo o resto deve permanecer igual ao canônico.

## 7. Validação local e diff obrigatório

Utilizar Python ou equivalentes locais, em modo **somente leitura das fontes e escrita do novo candidato/relatório**:

1. **Parse/schema**: JSON válido, tipos de widget, request/query, agregações, group by, visualização, título/descrição, layout e propriedades obrigatórias conforme o schema efetivamente usado.
2. **Integridade do canônico**: hash antes/depois idêntico; nenhum widget original desapareceu, duplicou ou foi alterado, inclusive aninhados, descrições, queries, template variables, notas e grupos.
3. **Contagem**: inventário de widgets de dados do candidato = inventário inicial **+2**; se inicialmente houver 19, esperar 21. Não confundir grupos e notas com widgets de dados.
4. **Diff semântico**: apenas dois nós novos de widgets, sem alterações em nós existentes, grupos/template variables ou metadados compartilhados. Se for necessário mudar layout existente, **bloquear e relatar**, não disfarçar como alteração editorial.
5. **Ambiente**: queries e atributos novos apontam para UAT. Não carregar acidentalmente valores/env, URLs ou IDs de PROD.
6. **Identidade das novas visualizações**: um Top List de contagem por proposta e um stream adicional filtrado; nenhuma alteração de widget antigo; ambos com descrição honesta sobre ausência de chave e limites.
7. **Viabilidade**: distinguir `VALIDADO ESTATICAMENTE`, `VALIDADO PELO OPERADOR NO LOG EXPLORER PROD`, `NÃO HOMOLOGADO COMO WIDGET UAT`. Não declarar sucesso em UAT se não houve renderização autorizada.

**Em caso de falha:** não corrigir adulterando o canônico ou os widgets anteriores; registrar a incompatibilidade e deixar o candidato como `NÃO APROVÁVEL` até revisão humana. Não gerar diff que mascare alterações.

Gerar caminhos/hashes locais para candidato e relatório. Não publicar artefatos corporativos no GitHub.

## 8. Entregáveis e parada

Gerar em workspace privado:

**A. Adendo ao relatório de homologação anterior**, com declaração da validação manual em PROD feita pelo responsável (sem alegar inspeção independente).

**B. Novo candidato JSON de UAT**, com nome identificável e versionado em `dashboards/`, por exemplo `uat-candidato-06f-p1-duas-visualizacoes.json` (se já existir, criar outro nome; não sobrescrever).

**C. Um relatório Markdown privado novo** em `planos/`, próximo número livre, sugestão `40-construcao-local-candidatos-p1-validacao-diff.md`, com:
1. decisão P0 homologada e P1 aprovadas em desenho;
2. registro da evidência manual do responsável e suas limitações;
3. definição dos dois widgets e suas queries reais, somente no relatório privado;
4. posicionamento dos novos widgets e justificativa;
5. validações estáticas, contagens base→candidato, hashes e diff semântico completo;
6. checklist do que permanece intocado;
7. riscos/limitações técnicas e pontos a validar após eventual publicação UAT;
8. estado final: `CANDIDATO LOCAL PRONTO PARA APROVAÇÃO` ou `BLOQUEADO COM JUSTIFICATIVA`;
9. caminho completo dos arquivos gerados;
10. evidência de **zero mudanças remotas, de código ou do canônico**.

Na mensagem final ao responsável, entregar **resumo curto e verificável**, contagem antes/depois, itens novos, evidência do adendo, resultado dos testes/diff e caminhos dos artefatos. Não afirmar publicação nem pedir para repetir os testes manuais que o responsável já realizou.

**PARAR antes de qualquer publicação ou importação em UAT/PROD.** A avaliação do candidato e a decisão de publicar são etapas separadas com aprovação humana expressa. Nenhum PUT/POST ou ação via sessão de navegador está autorizado por este prompt.