# PROMPT 06D — Revisão Crítica do Plano e Viabilidade da Correlação por Proposta

> **Executor:** Gemini CLI, exclusivamente no workspace corporativo autorizado.
>
> **NATUREZA DESTE ARQUIVO:** prompt metodológico para repositório **público**. Identificadores internos, nomes de aplicações e atributos, queries, logs, fontes de dados, JSONs, relatórios técnicos, URLs corporativas, IDs de widgets/dashboards, dados de cliente e evidências devem permanecer **somente no workspace e relatório privados**. Não copiar nada disso para GitHub.
>
> **AUTORIZAÇÃO:** somente análise, revisão técnica do diagnóstico anterior, provas de conceito **locais e não destrutivas quando houver evidências já disponíveis**, e planejamento detalhado para futura aprovação. **Nenhuma implementação, alteração de código ou dashboard, publicação, deploy, commit, push ou sincronização de JSON** está autorizada.
>
> **CONTEXTO:** o diagnóstico da etapa anterior concluiu que a chave estruturada de correlação por proposta, embora já instrumentada nos serviços pertinentes, é usada principalmente para rastreamento no stream de logs, sem uso analítico direto nos indicadores. O relatório anterior levantou sugestões P0/P1/P2 e causas documentadas. O responsável solicitou uma **revisão de viabilidade e segurança antes de aprovar qualquer mudança**, para não trocar a subutilização do campo por filtros que ocultem falhas ou métricas matematicamente enganosas.

## 1. Objetivo único

**Transformar o diagnóstico anterior em um planejamento de implementação tecnicamente seguro, rastreável e pronto para aprovação humana, sem executar intervenções.**

Responder, com base no relatório técnico privado da etapa anterior e nos artefatos que ele referencia:

1. Quais propostas P0/P1/P2 são de fato necessárias, seguras e aderentes aos requisitos originais?
2. Que eventos realmente têm a chave estruturada de proposta, em quais fluxos e etapas, e onde a cobertura ainda é desconhecida?
3. Qual é o risco de cada filtro proposto **eliminar justamente ocorrências de falha, descartes, inconsistências ou mensagens sem identificador**?
4. Quais agregações, contagens distintas ou correlações são possíveis no produto de observabilidade usado e com a telemetria atual?
5. O que pode ser melhorado apenas nas consultas/widgets, o que exige métrica/novo evento/código, e o que depende de fonte externa?
6. Em que ordem implementar, testar, comparar e promover as melhorias quando houver autorização posterior?

**Não** refazer o discovery original, não reproduzir integralmente as auditorias anteriores, não transformar ausência de registros em ausência do atributo. Utilizar os achados anteriores como hipóteses documentadas, não como autorização para aplicá-las.

## 2. Fontes e integridade das evidências

Ler seletivamente:
- relatório de diagnóstico produzido pela etapa 06C, dentro da pasta privada de planejamento (confirmar nome, número e integridade; não presumir que seja um arquivo específico se outro nome foi usado);
- decisões originais, relatórios de instrumentação, testes e justificativas citados nesse diagnóstico;
- definição/fórmula de cada indicador selecionado na planilha original;
- dashboard canônico UAT e, preferencialmente, último export/snapshot confiável de PROD que o responsável já possua;
- consultas existentes dos widgets pertinentes, inclusive sua agregação, filtros, grupo, unidade de contagem e janela;
- evidências reais de logs/atributos e exemplos já coletados em UAT/PROD, respeitando as permissões;
- capacidades documentadas da versão/organização do Datadog, quando houver prova ou fonte autorizada.

Verificar fontes e datas antes de concluir; preservar a distinção entre **requisito aprovado**, **decisão posterior**, **evidência de código**, **deploy declarado**, **telemetria observada**, **hipótese** e **proposta nova**.

**Controle de acesso:** a antiga extensão de recuperação de sessão foi bloqueada. Não buscar cookies, tokens ou segredos, não tentar simular API Key, não automatizar sessão por outro meio para contornar restrição administrativa. Se faltar leitura remota, usar os JSONs e as evidências privadas locais já existentes; descrever o que exigirá uma inspeção **manual e autorizada na UI**. Não alegar que executou consulta real se apenas escreveu a query.

## 3. Matriz de cobertura da chave estruturada

Para os fluxos efetivamente relevantes (entrada, início, processamento, retorno local, falha de negócio, falha técnica, retry, descarte, cancelamento, estorno e outros encontrados), documentar por evento:

`serviço/fluxo | evento exato | condição de emissão | campo estruturado presente? | amostra/evidência | amostra com campo ausente? | população de referência | risco de perda com filtro | status`.

Usar apenas os estados:
- **COMPROVADO COM CAMPO** — evidência de registros representativos;
- **COMPROVADO SEM CAMPO** — evidência de eventos legítimos sem chave;
- **CONDICIONAL** — variação por rota, erro, versão ou caminho de execução;
- **SEM AMOSTRA SUFICIENTE** — ausência de evidência, sem inferir cobertura;
- **NÃO PERTINENTE** — campo não necessário para esse tipo de evento.

Quando possível, comparar, na **mesma janela temporal, índice, serviço e filtros**: volume bruto, volume com campo, volume sem campo, exemplos de mensagens inválidas, códigos literalmente interpolados ou inconsistências de texto. Nunca transformar um percentual de amostra escassa em alegação de cobertura universal.

Avaliar propagação entre serviços/rotas assíncronas sem pressupor que a chave sobreviva a mudanças de thread, retry, NACK ou troca de contexto. Diferenciar chave em MDC, atributo de span e texto livre.

**Resultado obrigatório:** quais filtros `campo presente` podem ser aplicados sem comprometer o propósito do widget; quais são perigosos; quais permanecem inconclusivos.

## 4. Revisão crítica das propostas P0 anteriores

Reavaliar individualmente as recomendações do relatório anterior, recuperando os **IDs reais dos widgets só no relatório privado**:

**A. Stream de rastreamento:** confirmar se deve ser um stream especializado de propostas com identificador, ou se precisa continuar exibindo logs sem identificador para troubleshooting. Considerar preservação da visão ampla e alternativas não destrutivas; não eliminar silenciosamente eventos de falha.

**B. Indicador de descarte/DLQ de cancelamento:** avaliar se filtrar eventos com chave estruturada oculta descartes reais, inclusive logs malformados ou falhas na interpolação textual. **Nunca tratar descarte sem identificador como inexistente.** Propor divisão/separação de qualidade da instrumentação apenas quando respaldada por eventos.

**C. Indicadores de início e sucesso local:** verificar cobertura efetiva em ambos os lados da contagem, consistência da mesma população, e se o filtro restringe indevidamente sucessos, entradas ou erros. Não criar falsa melhoria de taxa apenas por diminuir numerador/denominador.

Para **cada** P0 original, decidir **MANTER P0 / REBAIXAR P1-P2 / SUBSTITUIR SOLUÇÃO / REJEITAR / PENDENTE DE EVIDÊNCIA**, com justificativa explícita, evidência e critério para avançar. O objetivo NÃO é validar automaticamente a severidade proposta pela etapa anterior.

## 5. Prova de viabilidade das agregações por proposta

Revisar as propostas de top list, correlação de retentativas e contagem de propostas distintas. Quando possível, executar provas **offline e somente leitura**, com amostras de evidência previamente disponíveis (dados adequadamente tratados, sem publicar dados); caso não existam, especificar queries candidatas para execução manual futura e marcar `NÃO EXECUTADO`.

Verificar:
- **Unidade:** log, tentativa, evento, cancelamento, proposta única ou conclusão local;
- **Semântica temporal:** hora do evento vs ingestão, resultados atrasados, janelas diferentes e mudança de bucket;
- **Cardinalidade:** limite de grupos, quantidade de valores únicos, agregações permitidas na visualização, limites de armazenamento/retention;
- **Deduplicação:** reentrega, NACK, retry, mensagens repetidas e log duplicado;
- **População:** numerador e denominador sobre o mesmo universo;
- **Desfecho:** falha transitória vs final, sucesso local vs resultado externo não observável;
- **Operabilidade:** a visualização pode explicar a contagem, ser auditada e ser usada sem perder falhas?

**Atenção às métricas de cardinalidade:** não concluir que a soma de valores distintos por intervalos curtos representa automaticamente o total de propostas distintas em uma janela longa. Diferenciar contagem distinta em uma consulta, métrica gerada de logs, séries por bucket e série dimensional de alta cardinalidade. Demonstrar a matemática ou classificar `NÃO VALIDADO`.

Não propor inserir identificador individual de proposta como dimensão/tag de métrica Micrometer com cardinalidade potencialmente ilimitada. Se houver necessidade de contadores de negócio, propor desenho de baixa cardinalidade e preservar a rastreabilidade detalhada em logs/spans.

## 6. Critérios especiais por indicador

Confrontar os indicadores selecionados com suas decisões já aprovadas:
- **Sucesso:** não apresentar taxa por proposta sem prova do fluxo de entradas e desfechos locais comparáveis;
- **Falha:** não reintroduzir divisão sintética já rejeitada nas decisões anteriores; discriminar negócio, técnico e retentativa;
- **Cancelamento:** preservar semântica de falha de cancelamento, não confundir NACK bem-sucedido com cancelamento de negócio concluído;
- **Reprocessamento:** diferenciar volume absoluto de DLQ e número/taxa de reprocessamentos efetivos;
- **Demais indicadores:** documentar por que a correlação não é necessária ou permanece fora de alcance, sem forçar alteração.

Manter o recorte de responsabilidade dos serviços: um handoff local bem-sucedido pode ser comprovado sem que o sucesso final do parceiro seja observável. Necessidades de reconciliação externa devem ser assumidas como dependências, não preenchidas com conjecturas.

## 7. Plano de implementação futura — pronto para aceite, mas sem executar

Produzir um plano **enxuto e priorizado**, mapeando oportunidades nas três categorias:

- **P0 — Correção necessária**, apenas para defeito ou lacuna frente a decisão original com impacto comprovado;
- **P1 — Evolução de valor claro** com sinais/viabilidade demonstrados;
- **P2 — Dependência arquitetural, nova instrumentação, reconciliação ou evolução opcional**.

Cada item precisa conter:
`prioridade revisada | widget/indicador | decisão original | problema observado | mudança proposta | query/agrupamento conceitual | dados adicionais necessários | impacto esperado | risco | prova de viabilidade | critério de aceite | rollback planejado | dependências`.

Agrupar alterações por finalidade, não por dezenas de micro-prompts. Propor **um ciclo de implementação controlado em UAT**, com candidato local, backup/diff, validação funcional de contagens e casos sem chave, revisão humana e, **somente após autorização futura**, promoção a PROD com verificação visual e backup. Não gerar nem modificar o JSON candidato nesta etapa.

Separar claramente:
1. ajustes de queries e descrições com base em sinais existentes;
2. novas visualizações realmente justificadas;
3. propostas que exigiriam eventos/métricas adicionais ou outro time;
4. investigações ainda necessárias por falta de evidência.

Criar matriz de **critérios de aceite e testes negativos**: logs sem chave não sumirem indevidamente, descartes não serem ocultados, retentativas não inflarem propostas únicas, taxa não somar buckets como entidades distintas, população e resultado local corretamente definidos.

## 8. Entregável privado e resposta final

Criar **um relatório Markdown privado**, usando o próximo número livre em `darwin-monitor/planos/`. Nome sugerido: `38-revisao-viabilidade-plano-correlacao-propostas.md`; não sobrescrever arquivos preexistentes.

Estrutura:
1. Resumo decisório;
2. Evidências conferidas e limitações de acesso;
3. Matriz de cobertura por evento e fluxo;
4. Decisão individual sobre os P0 originais;
5. Provas de viabilidade e/ou planos de prova pendentes para agregações;
6. Matriz dos indicadores e impacto real;
7. Priorização revisada P0/P1/P2;
8. Desenho futuro e critérios de aceite;
9. Plano de execução **não autorizado ainda**;
10. Riscos, dependências, questões sem resposta;
11. Verificação de integridade: nenhum widget/query, código, dashboard UAT/PROD ou canônico modificado.

No terminal, apresentar de forma clara:
- **O que de fato conseguimos aproveitar agora** da instrumentação;
- **Quais filtros do plano anterior NÃO devem ser aplicados cegamente**;
- **Quais provas/consultas foram realmente executadas** e quais só foram propostas;
- **Quais decisões de escopo/prioridade foram alteradas**;
- **O que depende de aprovação humana futura**;
- caminho do relatório privado.

**PARE** após entregar o relatório e o plano. **Não implemente nada, não publique, não modifique dashboard/código, não faça commits, não obtenha credenciais por meios alternativos.**