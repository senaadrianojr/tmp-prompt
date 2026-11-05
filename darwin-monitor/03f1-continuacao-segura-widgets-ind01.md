# Darwin Monitor — Prompt 03F.1: continuar preparação do IND-01 sem executar PUT

## Contexto e correção de rumo

Você estava executando o **Prompt 03F** (preparar localmente widgets A/B/C para o IND-01) e parou ao inspecionar `update_uat_dashboard.py`, porque o script contém operação **PUT que substitui o dashboard remoto**. **Sua decisão de não executá-lo foi correta.** Entretanto, isso **NÃO impede a preparação local dos widgets**. Este prompt complementa e substitui apenas as regras de sincronização/bloqueio do 03F; preserve todos os critérios de semântica, fonte Logs, validação e escopo definidos no 03F.

**Não espere esclarecimento nem rode o script com risco de escrita. Continue em modo exclusivamente local/read-only remoto.**

## Restrições absolutas

- **NÃO executar `update_uat_dashboard.py`** e nem qualquer função/CLI/HTTP que possa gerar PUT, PATCH, POST, DELETE ou persistir alterações no Datadog. Não executar o script com flags imaginárias de `--dry-run` ou `--get-only`.
- Não alterar credenciais, cookies, autenticação, scripts de publicação ou código Java; não executar deploy, merge, push ou reset de branch.
- Somente leitura de endpoints remotos existentes e *já autorizados*, **se** for possível confirmar que a operação executada é GET sem efeito de escrita. Não invente endpoints nem tente contornar restrições de acesso.
- **Não sobrescrever `uat_darwin_monitor_dashboard.json`** com uma cópia possivelmente desatualizada; não tocar nos widgets remotos do IND-07 nem no widget de cancelamento manual.
- Evitar duplicação de `docs/darwin-monitor/` dentro do workspace. O workspace `darwin-monitor/` já contém `planos/`, `dashboards/`, `uat_darwin_monitor_dashboard.json` e `update_uat_dashboard.py`.
- A falta de novos logs depois da implantação recente de `cdPropostaSeguro` **não bloqueia** os widgets A/B/C, que usam logs previamente validados. Correlação e percentual continuam pendentes. Não repetir auditorias ou consultas históricas já validadas.

## 1. Retomar exatamente do estado atual

- Confirme que o backup que você criou (`uat_darwin_monitor_dashboard.json.bak-20261104-prompt03f`, ou nome real verificado) existe e é legível. Não recriar desnecessariamente.
- Identifique se `uat_darwin_monitor_dashboard.json` foi modificado depois do backup e se houve qualquer escrita remota; informe fatos, não suposições.
- Inspecione o JSON local e o script existente **apenas como documentação da estrutura**, sem executar código capaz de publicar.
- Se existir uma função comprovadamente somente de leitura e já autorizada, pode usá-la para obter a definição atual do dashboard e comparar com a cópia local; reporte data, origem e limitações. Se não existir, assinale: `Sincronização remota NÃO verificada; JSON local é apenas um snapshot`. **Não bloqueie a preparação em arquivo separado**.

## 2. Preparar os widgets em artefato isolado (caminho seguro mesmo sem GET)

Faça três definições candidatas **sem alterar o JSON principal nem o dashboard remoto**. Salve em `darwin-monitor/dashboards/ind01-widgets-candidatos-uat.json` (caminho relativo `dashboards/ind01-widgets-candidatos-uat.json`). Use a estrutura comprovada de widgets de **Logs / Count** já presente no JSON local, mas não alegue validação no Datadog se não foi feita.

- **Widget A:** `Processamentos de recorrência iniciados (Pub/Sub)`; `Count` de logs de início do fluxo, unidade = ocorrência de processamento/tentativa (inclui possíveis reentregas), nunca propostas únicas.
- **Widget B:** `Retornos de sucesso local do orquestrador`; `Count` do log de sucesso local, unidade = ocorrência do retorno local, não conclusão do seguro.
- **Widget C:** `Falhas de negócio e retentativas`; DUAS séries/queries separadas com `Count`: `[FALHA-NEGOCIO]` e `[RETRY-PUBSUB]`. Não agrupar por mensagem completa nem chamar retries de falhas definitivas.

Todas as queries devem incluir `service:sboot-pseg-base-orch-gestao-eventos-conta-cartao env:uat`, com os marcadores textuais **já confirmados em UAT** no histórico. Preserve sintaxe, aspas e restrições da fonte Logs validadas nas etapas anteriores. Não usar `@mdc.cdPropostaSeguro:*`, `distinct_count`, percentuais, consultas métricas inventadas ou agrupamento por ID.

O arquivo pode conter **somente o fragmento de widgets candidatos**, com envelope estruturado próprio e metadados explicativos quando isso não invalida a integração. Se o schema do dashboard inteiro não aceita fragmentos diretamente, explique o método de posterior incorporação. **Não apresente o fragmento como JSON publicável diretamente** sem comprovação.

Faça validação sintática local (`json.loads` ou similar), confira que o JSON original está intacto e verifique que os três candidatos não reproduzem duplicações de IND-01 já presentes na cópia local. Se houver widget equivalente preexistente, registre estratégia de atualização futura em vez de duplicá-lo.

## 3. Relatório

Crie (ou complemente se já existir) `darwin-monitor/planos/12-preparacao-widgets-ind01-conta-cartao.md`, exclusivamente dentro da raiz real do workspace. Registre:
- causa da interrupção do 03F: `update_uat_dashboard.py` pode executar PUT; execução deliberadamente evitada;
- evidência de backup, original preservado, eventual inspeção remota somente leitura, e claramente **se a versão local está ou não sincronizada**;
- arquivos de candidatos gerados e sua integridade sintática;
- consulta, tipo, unidade de contagem e limite de cada widget A/B/C;
- não alteração do dashboard remoto nem do código Java;
- pendência de validar `cdPropostaSeguro` **em log posterior ao novo deploy**;
- próximo passo separado: revisar os candidatos e obter autorização explícita antes de incorporar/publicar a definição final, partindo de JSON remoto atualizado.

**Caso o documento 12 ainda não exista, crie-o. Caso exista, ajuste somente esse documento para incorporar a evidência nova. Não alterar os relatórios anteriores.**

## Resposta final objetiva

Informe:
1. `PUT executado: NÃO`; `Dashboard remoto alterado: NÃO (pela execução atual)`;
2. `Backup encontrado: SIM/NÃO` e caminho;
3. `Sincronização remoto-local comprovada: SIM/NÃO`, sem inferências;
4. `JSON principal preservado: SIM/NÃO`;
5. `Três candidatos A/B/C preparados: SIM/NÃO`, e seus nomes;
6. caminho exato do arquivo `dashboards/ind01-widgets-candidatos-uat.json` e do relatório `planos/12-preparacao-widgets-ind01-conta-cartao.md`.

**Objetivo deste prompt: avançar na preparação sem nenhuma escrita remota e sem sacrificar a segurança das alterações manuais.**
