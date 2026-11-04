# Darwin Monitor — Prompt 03: Investigação focada do IND-01 (Índice de Sucesso / Eventos Conta Cartão)

## Missão — somente um indicador

Investigue **exclusivamente o IND-01 — Índice de Sucesso**, na aplicação `sboot-pseg-base-orch-gestao-eventos-conta-cartao`, para identificar **o que podemos monitorar com evidência real na fronteira da nossa aplicação durante o projeto Darwin**.

O IND-07 (Origem) já foi concluído com **cobertura parcial aceita**. Aproveite as lições metodológicas, mas **não o reabra nem altere seus widgets**.

**Nesta execução: somente leitura, investigação, diagnóstico e relatório. É proibido modificar código Java, instrumentação, dashboards, JSON de publicação, scripts de autenticação ou monitores. Não execute PUT de dashboard.**

### Contexto de negócio

O Darwin Monitor é uma linha de base operacional para detectar regressões durante a migração/desmembramento do monólito e as mudanças de integração com Pismo. Algumas etapas do negócio acontecem em serviços de outros times, sobre os quais não temos controle.

**Não precisamos provar a efetivação final de negócio fora da nossa aplicação para entregar observabilidade útil**. Mas também não podemos afirmar que "requisição recebida", "mensagem publicada", "HTTP 200", "retentativa" ou "log de sucesso" significam a mesma coisa.

**Não descarte o indicador por impossibilidade de medir a fórmula integral.** Proponha um widget operacional **PARCIAL**, nomeado conforme o que mede de fato, se houver telemetria confiável de recebimento, encaminhamento, resposta local ou falha.

## 1. Fontes a ler antes de investigar

1. `docs/darwin-monitor/planos/04-inventario-filtrado-indicadores.md` — localize a linha **IND-01**. Confira o texto original na aba **Monitoramento** de `docs/darwin-monitor/Monitoramento Cartões 1.xlsx` se houver dúvida.
2. Planilha auxiliar de indicadores × aplicações, se necessário para localizar componentes; é **pista**, não prova.
3. Repositório atual da aplicação em `repos/sboot-pseg-base-orch-gestao-eventos-conta-cartao` (localize o nome/caminho verdadeiro em vez de presumir).
4. Relatórios históricos e `docs/darwin-monitor/dashboards/03-dashboard-alvo.md` somente para identificar **hipóteses** que precisarão ser verificadas, jamais para reproduzir queries ou declarar sucesso.
5. `docs/darwin-monitor/planos/05-investigacao-ind07-origem-venda-digital.md` apenas como exemplo de **disciplina de evidências**; atributos daquela aplicação não são automaticamente os mesmos desta.

**Requisito esperado do inventário, a confirmar literalmente na planilha**:
- Indicador: Índice de Sucesso.
- Objetivo: monitorar a integridade da integração entre BV e Bankly.
- Fórmula declarada: recorrências recebidas ÷ recorrências enviadas (%).

**Atenção:** essa fórmula pode não representar uma "taxa de sucesso" no sentido convencional. Registre sua interpretação e a ambiguidade, sem invertê-la ou substituir o requisito. Determine quem envia/recebe, em que fronteira, e se ambos os eventos são observáveis e comparáveis.

## 2. Investigação curta, dirigida pelo código

**Ponto de partida sugerido por especialista do time: `SolicitacaoRecorrenciaViaPubSub.java`.** É uma orientação, não um limite.

1. Localize essa classe e seus pontos de entrada. Identifique o fluxo de Pub/Sub efetivamente implementado; procure a rota síncrona de recorrência e colaboradores diretos **somente se forem relevantes para o IND-01**.
2. Siga a execução até os pontos em que a nossa aplicação:
   - recebe a solicitação/evento;
   - valida, rejeita ou descarta;
   - encaminha/persiste/publica/chama um serviço externo;
   - recebe confirmação ou resposta local quando disponível;
   - registra erros, NACK, retry ou outro estado relevante.
3. Distinga caminhos síncronos de assíncronos, contagem por tentativa versus por mensagem única, retries, duplicidade e eventuais rejeições de negócio. **Não conte NACK ou DLQ automaticamente como falha definitiva de adesão.**
4. Localize o ponto exato que **comprova** cada evento candidato. Registre **classe, método, condição de execução, expressão/trecho real, o que comprova e o que NÃO comprova**.
5. Investigue somente os colaboradores necessários para interpretar essa fronteira. **Não vasculhe indiscriminadamente outros repositórios ou sistemas de terceiros.**
6. Registre branch/commit efetivamente verificados; se indisponíveis, declare isso. Código local (inclusive branches de instrumentação) não comprova implantação em UAT.

**Pistas históricas a revalidar, nunca presumir verdadeiras:** `SolicitacaoRecorrenciaRouter.java`, `ValidaEventoGeradoProcessor`, `ObservabilityProcessor`, `[EVENTO-31]`, mensagens antigas atribuídas a sucesso/falha, `cdPropostaSeguro`. Se não houver essas classes/mensagens/atributos, **não os invente nem adapte por semelhança**. Não dependa de números fixos de linha.

## 3. Auditoria da telemetria NO ponto observado

Para cada evento candidato do passo anterior:

- Procure no código o log **literal** emitido ou uma métrica/span efetivamente instrumentados.
- Diferencie `log.info` / `log.error` de exceções lançadas, mensagens de retorno HTTP, strings em comentários e texto produzido por serviços externos. **Só o logger executado é candidato a log.**
- Se houver MDC, localize exatamente onde é populado, quando é removido e a configuração que permite sua emissão. **Não assuma que os atributos desta aplicação estão na raiz ou em `mdc` só porque no venda-digital era `@mdc.processadora`.**
- `cdPropostaSeguro` pode ajudar na correlação de logs/traces, mas **não sugira usá-lo indiscriminadamente como dimensão de métrica de alta cardinalidade**.
- Se não existir log ou outra telemetria suficiente no ponto adequado, **descreva a instrumentação mínima proposta**, com condição, campos estáveis, nível e teste. **Não implemente.**
- Se houver apenas um sinal parcial, **preserve uma proposta de widget parcial**, sem falsificar numerador/denominador.

## 4. Verificação de dados reais em Datadog UAT

**Lição do IND-07:** queries que pareciam corretas nos relatórios antigos falharam; a interface revelou atributos MDC aninhados. Portanto, **não gere queries por dedução**.

Tente leitura **somente pelos meios já autorizados e existentes** de Datadog UAT. A ferramenta Python baseada em cookies/API legada não conseguiu anteriormente comprovar consulta de Logs; **não reescreva a ferramenta, não contorne bloqueios e não tente endpoints aleatórios**.

Se a leitura programática estiver indisponível:

1. Identifique **filtros candidatos de investigação baseados apenas em logs reais encontrados no código**, informando sua origem.
2. Marque esses filtros como **PENDENTES DE TESTE MANUAL**, e escreva um roteiro curto para o responsável testar no Datadog Log Explorer.
3. Peça que o responsável confirme **caminhos reais dos atributos** (inclusive possíveis objetos `mdc`), serviço, ambiente, valores e janelas, antes de produzir a consulta de widget.
4. **Encerre sem afirmar validação no Datadog.** Não substitua evidência manual por suposições.

Se leitura autorizada funcionar, registre fonte (Logs, APM Traces ou Metrics), intervalo, serviço/ambiente, filtro executado, retorno e limitações. Caso haja ausência de resultados, faça **no máximo dois refinamentos fundamentados em novas evidências**, depois pare. Não use métricas APM de erro HTTP como "sucesso de negócio" sem comprovar correspondência.

**Campos e eventos presentes no código não são prova de que chegaram ao UAT.** Nunca declare "query validada" sem execução e evidência, nem confunda uma query de Metrics com o formato de Logs.

## 5. Decisão semântica: integral versus parcial

Compare a regra da planilha com o que a nossa aplicação realmente mede:

- Se ambos numerador e denominador representam os eventos exigidos, com mesma unidade, recorte temporal e tratamento de duplicidade, **proponha a fórmula integral com evidência**.
- Se só observamos recebimento, encaminhamento, retornos locais ou falhas técnicas, **proponha até dois widgets parciais** (por exemplo, volumes recebidos versus encaminhados, ou falhas locais de processamento), **mas apenas se o código sustentar esses eventos**.
- Não afirme que "enviado a Bankly" significa "processado pela Bankly" ou que "status 8" significa sucesso sem confirmar a regra atual.
- Para porcentagens, documente explicitamente numerador, denominador, exclusões e o comportamento com denominador zero. Se as duas contagens não forem comparáveis, **não invente taxa**: prefira volumes separados ou contagem de erros locais.
- Mantenha a conclusão honesta: **INTEGRAL / PARCIAL / AINDA NÃO VALIDÁVEL / BLOQUEADO**, com evidências e pendências específicas.

## 6. Entregável enxuto (uma investigação, um arquivo)

Crie `docs/darwin-monitor/planos/07-investigacao-ind01-indice-sucesso-conta-cartao.md` (ou caminho incremental se esse arquivo já existir). Estrutura:

1. **Requisito original**: nome, objetivo, fórmula e fonte exata da planilha.
2. **Fronteira da aplicação**: representação textual curta do fluxo, condições de sucesso/falha locais e dependências externas.
3. **Pontos comprovados no código**: tabela com classe/método, condição, evento real, interpretação e log/métrica/span observável.
4. **Riscos de contagem**: fluxos paralelos, retries, rejeições, duplicidade, implantação UAT.
5. **Telemetria no Datadog**: consultas realmente executadas e seus resultados OU roteiro de **teste manual pendente**, distinguindo as duas situações.
6. **Cobertura do IND-01**: INTEGRAL/PARCIAL/AINDA NÃO VALIDÁVEL/BLOQUEADO e justificativa.
7. **No máximo dois widgets candidatos**: nomes fiéis, evento contado, filtros comprovados ou pendentes, agregação, limitações.
8. **Instrumentação mínima proposta**, apenas se necessária e **sem aplicar**.
9. **Próximo passo concreto**, apenas um, priorizando obter a evidência que falta.

Use linguagem técnica curta; **não replique páginas de documentos históricos nem despeje o código inteiro**.

Na resposta final, informe somente:
- arquivo produzido;
- estado de cobertura;
- principal ponto do código que comprova o evento;
- disponibilidade da telemetria UAT (**validada / pendente**);
- widget(s) sugerido(s);
- próxima ação para destravar.

**PARE. Não altere `uat_darwin_monitor_dashboard.json`, `update_uat_dashboard.py`, qualquer widget no Datadog, nem avance para IND-02.**
