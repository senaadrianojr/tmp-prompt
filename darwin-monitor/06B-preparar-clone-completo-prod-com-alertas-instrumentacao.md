# PROMPT 06B — Preparação Integral do Dashboard PROD e Publicação Manual pelo Datadog (Sem API Key)

> **Executor:** Gemini CLI, no workspace corporativo autorizado. **Operador:** responsável técnico com login corporativo normal na interface Datadog.
>
> **SEGURANÇA DO REPOSITÓRIO PÚBLICO:** este prompt contém apenas método sanitizado. Não copiar para este repositório nomes de serviços, IDs/URLs de dashboards, consultas literais, código, relatórios privados, JSONs, backups, cookies, chaves, tokens, screenshots, campos internos ou dados da empresa. O destino PROD e as informações corporativas serão fornecidos **na mensagem privada de execução**.
>
> **AUTORIZAÇÃO EXPRESSA JÁ DADA PELO RESPONSÁVEL:** substituir **integralmente o conteúdo do dashboard MVP de produção existente e especificamente identificado**, usando uma cópia completa do dashboard final de UAT adaptada à PROD. A autorização vale para esse dashboard, não para qualquer outro. O operador realizará a importação pela interface normal do Datadog; o Gemini **não realizará chamadas de escrita remota**. Não solicitar novamente aprovação apenas por não haver API Key. **Não utilizar extração de cookies, extensões alternativas, DevTools para obter credenciais, Selenium de sessão nem outros meios de contornar o bloqueio administrativo.**
>
> **ATUALIZAÇÃO DE IMPLANTAÇÃO CONFIRMADA PELO OPERADOR:** as instrumentações de processadora em venda digital e correlação por proposta nas duas aplicações de eventos já tiveram deploy em produção confirmado pelo responsável. Isso **elimina a pendência de deploy** dessas três instrumentações, mas não equivale a evidência de que cada query produziu eventos em PROD. **Não classificar como "aguardando implantação"** por falta de logs. Se faltar evidência de um sinal específico, usar o estado **"disponibilidade em PROD a confirmar"** sem bloquear a importação apenas por ausência de dados.
>
> **MUDANÇA OPERACIONAL:** a execução da versão anterior de 06B dependia de GET/PUT autenticados por script. Essa via ficou indisponível porque a extensão usada para recuperar cookies foi bloqueada e o operador não possui API Key. **Substituir a publicação por uma única importação manual do JSON através da UI oficial**, preservando a preparação e a validação local com Gemini/Python.

## 1. Objetivo: concluir com ferramentas existentes

Preparar um pacote **pronto para importar pela UI** do Datadog, usando arquivos locais privados, e acompanhar a validação da importação sem depender de API, sessão programática ou ferramentas de navegador não autorizadas.

**Fonte de verdade:** JSON canônico UAT local mais recente e relatório de encerramento privado. O operador já validou o dashboard UAT, inclusive grupos, widgets, títulos e descrições. Não reabrir auditorias anteriores. Confirmar a integridade do canônico local e a sua versão conhecida. Se for necessária informação remota que não está mais acessível por API, usar **exportação JSON pela UI feita pelo operador**, sem insistir em GET programático.

**Destino:** dashboard PROD existente que o operador forneceu, identificando host/tenant, ID e título antigo na mensagem privada. A importação deve **sobrescrever o conteúdo deste dashboard**, não criar outro nem modificar UAT. Não inferir IDs pelo nome. O título do dashboard importado deve refletir PROD.

## 2. Preparação técnica: cópia completa, sem atalhos

1. Ler seletivamente o canônico UAT, relatório privado final e qualquer matriz de instrumentação/versões já disponível. Não modificar canônico UAT, Java, scripts corporativos, testes, planilhas, pipelines ou Git.
2. Inventariar os grupos, notas e widgets de dados de forma **recursiva**. Preservar estrutura, número de widgets, layouts, tipos, títulos e descrições já aprovados.
3. Para **cada widget**, verificar filtros e variáveis: contexto, serviço, log indexes, tags, storage, `env`, templates e dependências. Converter de UAT para valores **comprovadamente válidos de PROD**. **Não** trocar todas as ocorrências de `uat` por `prod` cegamente. Se o valor real de filtro/ambiente não puder ser estabelecido, assinalar a pendência concreta e NÃO entregar JSON para importação até corrigir a configuração. Ausência de tráfego/log não equivale a filtro inválido.
4. Confirmar os deploys das três instrumentações com base na declaração do operador. Se houver evidência privada existente de telemetria, usá-la; na falta dela, sinalizar somente **disponibilidade da telemetria ainda não comprovada** e não fingir que o deploy falta. Não exigir auditorias/retestes extensos.
5. Manter o dashboard completo mesmo que algum widget tenha pendência de telemetria: `⚠` no título apenas quando houver dúvida relevante sobre disponibilidade; descrição mantém a explicação do sinal e acrescenta aviso específico. Widget operacional comprovado fica sem aviso. Não transformar eventos de log em métricas de negócio, nem declarar zero para dados ausentes.
6. Remover do JSON de importação identificadores e metadados de administração do dashboard UAT que não devam ser colados no PROD. Respeitar o formato que a ação **Import dashboard JSON** aceita; não incorporar ID de dashboard UAT como destino.
7. Produzir **um JSON pronto para importação pela UI** e sua versão candidata original, com caminhos, checksums e um resumo privado das mudanças UAT→PROD. Nenhum JSON corporativo deve ir ao repositório público.

## 3. Validação local obrigatória

Executar somente validação por Python ou ferramenta offline:
- parse JSON e conformidade básica com schema;
- total de grupos e widgets (inclusive aninhados), títulos e descrições;
- nenhuma query, template variable, link, filtro ou serviço indevidamente apontando para UAT;
- status das instrumentações coerente com os deploys confirmados;
- igualdade funcional dos indicadores com a origem, salvo adaptação de ambiente e sinalização visual;
- nenhuma alteração em canônico UAT ou recursos remotos;
- identidade do PROD alvo fornecida em privado pelo operador.

Se a configuração para importação não puder ser considerada válida, **NÃO** declarar "pronto". Entregar diagnóstico específico em vez de JSON arriscado. Não usar dry-run que esconda escrita remota.

## 4. Backup e importação manual, executada pelo OPERADOR

O Datadog documenta a sequência: **Configure → Export dashboard JSON** e **Configure → Import dashboard JSON**. A importação **substitui todo o conteúdo do dashboard aberto**. Referência oficial: https://docs.datadoghq.com/dashboards/configure/ .

**Antes de importar:**
1. O operador abre **o dashboard PROD antigo exato** na UI, confere URL/ID e sua conta/tenant.
2. Em **Configure → Export dashboard JSON**, exporta o estado corrente para backup local (mesmo que tenha um backup anterior), salva com data e não altera o arquivo. Esse backup é especialmente importante porque a importação remove todo o conteúdo antigo.
3. Se desejar comparar o estado atual com o backup anterior, o operador fornece o export ao Gemini no workspace corporativo; porém ele **já autorizou substituição integral**, então não precisa preservar os widgets antigos no novo dashboard.

**Publicação:**
4. Em **Configure → Import dashboard JSON**, o operador cola ou importa o arquivo preparado pelo Gemini, **estando no dashboard PROD correto**. Confere a prévia e salva/aplica conforme os controles mostrados pela UI. Não usar `New Dashboard` ou `Clone Dashboard`: o objetivo é substituir o MVP existente, preservando o dashboard-alvo.
5. Se a interface exigir permissões que a conta não possui, **PARAR**; não tentar obter credenciais ou elevar privilégios por meios alternativos. Solicitar a um administrador autorizado que faça a importação, usando o mesmo JSON validado.
6. Em caso de erro de importação, interromper e registrar mensagem. Não tentar modificações improvisadas de queries/JSON para contornar validações.

O Gemini **não deve declarar publicação concluída só porque gerou um JSON**. A operação depende do operador e das permissões da UI.

## 5. Verificação pós-importação sem API

Após o operador confirmar que a UI salvou a importação:
1. Validar visualmente no dashboard PROD: título, grupos, layout, avisos, descrições e funcionamento dos widgets que tenham tráfego.
2. Pelo menu **Configure → Export dashboard JSON**, o operador exporta o dashboard PROD já salvo.
3. O Gemini usa Python **offline** para comparar semanticamente **JSON importado × JSON exportado após a publicação**. Ignorar apenas metadados voláteis e normalizações oficiais comprovadas; registrar qualquer mudança funcional, widget perdido, query modificada ou filtro inesperado.
4. Classificar como `PUBLICAÇÃO CONFIRMADA PELA UI E EXPORT VALIDADO`, `PUBLICAÇÃO VISUAL CONFIRMADA — EXPORT PENDENTE`, `DIVERGENTE`, `NÃO PUBLICADO` ou `RESULTADO INDETERMINADO`. Não afirmar "GET pós-PUT" ou "HTTP 200" nesta via manual.
5. Se houver divergência ou erro, preservar o backup e informar a diferença. **Não restaurar automaticamente**. Se o operador desejar restaurar, o caminho é a **importação manual** do backup no mesmo dashboard, com decisão expressa do responsável.

## 6. Entregas e continuidade no mesmo prompt

Criar **relatório privado de produção** com prefixo `35-` (ou próximo número livre, sem sobrescrever arquivo existente), inventário dos widgets, mudanças de ambiente, avisos, hashes, resultados do validador, instruções de importação e campos a serem preenchidos com as evidências pós-importação.

**Primeira resposta do Gemini:** entregar JSON final **PRONTO PARA IMPORTAÇÃO MANUAL**, link/caminho local e checklist de UI curto. **PARE apenas para o operador realizar a importação manual**, que é a única ação impossível de automatizar sem uma credencial autorizada.

**Quando o operador voltar à mesma sessão com o JSON exportado após a importação:** executar a comparação Python e finalizar **no mesmo relatório** com resultado factual. Não gerar outra cadeia de prompts nem reabrir planejamento.

**PROIBIDO:** tentar recuperar cookies, simular API Keys, automatizar sessão web autenticada contra bloqueio administrativo, executar PUT/POST sem credenciais autorizadas, modificar canônico UAT, alterar outros dashboards, usar PROD para testes destrutivos, fazer deploy/commit/push ou afirmar sucesso sem evidência real.
