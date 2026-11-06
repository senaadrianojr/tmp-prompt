# PROMPT 05C — Reconciliar a Planilha com a Observabilidade (Seguros / Seguros V6)

> **Destino:** nova sessão do Gemini CLI, executada no ambiente corporativo autorizado.
>
> **REPOSITÓRIO PÚBLICO:** este arquivo contém somente instruções metodológicas. Não copie para GitHub, issues públicas, respostas externas ou outros destinos públicos os relatórios, planilhas, código, queries, métricas, nomes internos de serviços, identificadores de widgets, dados de negócio ou evidências extraídas do ambiente privado.
>
> **ESCOPO DESTA ETAPA: AUDITORIA DE ADERÊNCIA E REVISÃO DO PLANO. NÃO CONSTRUIR, REMOVER NEM PUBLICAR WIDGETS.**

## 1. Objetivo: restabelecer a fonte de verdade

Atue como **auditor de requisitos e de observabilidade**, com postura crítica e evidências verificáveis.

O objetivo do Darwin Monitor é **atender aos indicadores atribuídos a Seguros e Seguros V6 nas planilhas de monitoramento**, na medida tecnicamente comprovável dentro da fronteira dos serviços e suas integrações.

**Ordem obrigatória de raciocínio:**
1. **PLANILHA:** qual indicador, objetivo, fórmula, unidade, responsável e critério de negócio foram realmente solicitados?
2. **APLICAÇÕES/CONTRATOS:** quais processos implementam ou encaminham cada etapa? Quem observa o desfecho? Onde estão as fronteiras?
3. **TELEMETRIA REAL:** quais logs, spans, métricas, eventos e campos existem em código e quais foram efetivamente observados no Datadog UAT?
4. **DASHBOARD:** quais widgets atuais atendem, atendem parcialmente, contradizem ou não cobrem o requisito?
5. **AÇÃO PROPOSTA:** **MANTER, AJUSTAR, CRIAR, REMOVER ou INVESTIGAR/INSTRUMENTAR**, com motivos e evidências.

**O dashboard NÃO é a fonte do requisito.** Não limite as propostas aos widgets já existentes. Também não crie widgets apenas para aumentar a quantidade de visualizações: cada um deve responder a uma pergunta operacional vinculada a um requisito ou a uma cobertura parcial explicitamente aceita.

## 2. Contexto e ponto de partida

As etapas anteriores fizeram uma auditoria técnica de um indicador de falhas no cancelamento (05A) e o desenho operacional preliminar (05B). Seus relatórios locais estão em **darwin-monitor/planos/**, com prefixos **22-** e **23-**, respectivamente.

A 05B propôs sanear um widget de esgotamento de tentativas, mantendo retentativas e estornos fora desse recorte imediato. **Essa proposta não prova, por si só, atendimento ao indicador da planilha**: há diferença entre falha operacional no processamento do cancelamento e eventual cobrança posterior indevida.

A auditoria anterior também sinalizou inconsistência entre **nome do indicador, objetivo escrito e fórmula** e ausência de uma taxa percentual unificada com numerador e denominador coerentes. Não invente um percentual para contornar esse problema. Entretanto, **não conclua que o objetivo funcional é impossível sem investigar as fontes e os eventos apropriados**.

Os indicadores anteriormente validados e publicados não devem ser refeitos por hábito. **Classifique seu atendimento aos requisitos na matriz**, sem editar ou reabrir publicações concluídas, exceto se aparecer evidência nova e material de lacuna ou divergência; nesse caso, registre recomendação para decisão humana, sem modificar nada.

É indispensável preservar a separação entre:
- tentativa HTTP, erro técnico, log, evento, reentrega e retry;
- proposta, contrato, solicitação e cancelamento únicos;
- sucesso local, confirmação downstream, falha terminal do processamento automático e desfecho de negócio;
- cancelamento, cobrança posterior, estorno/compensação financeira.

## 3. Autorização e limites

**Permitido**
- Ler as planilhas relevantes e seus metadados/células (sem editá-las).
- Ler seletivamente os relatórios 22 e 23, decisões anteriores e trechos de código indispensáveis.
- Ler a configuração local do dashboard e, quando houver acesso autorizado, consultar em **modo leitura** a versão remota atual e a telemetria Datadog UAT.
- Criar **somente um novo relatório Markdown local** em darwin-monitor/planos/, com o prefixo **24-** (ou alternativa não existente).

**Proibido**
- Editar, criar, remover ou publicar widgets, monitores, alertas, grupos, streams ou dashboards.
- Editar JSON canônico, candidato, export, publicador, scripts ou configuração.
- Alterar código Java, testes, instrumentações ou contratos.
- Commit, push, PR, pipeline, build de efeito colateral, deploy ou qualquer escrita remota.
- Executar operações destrutivas, modificar autenticação ou contornar controles.
- Gerar logs falsos, números inventados, dados de teste artificiais apresentados como UAT ou afirmações não verificadas.

**Só o relatório 24 pode ser criado localmente.** Verifique antes se o nome proposto já existe e não o sobrescreva. Publique nada fora do workspace privado. Se não conseguir executar uma consulta, documente a limitação e continue onde for possível.

## 4. Fase A — inventário EXAUSTIVO dos requisitos de Seguros / Seguros V6

Localize a **planilha-mestre de monitoramento** e, quando aplicável, a **planilha auxiliar de planejamento**. Leia todas as abas/linhas pertinentes, não apenas as linhas de indicadores já estudados.

1. Identifique exatamente as colunas de **responsável/área**, nome, objetivo, fórmula, fonte e observações.
2. **Selecione todas as linhas cujo responsável seja Seguros ou Seguros V6**, respeitando a grafia real na planilha. Não presuma que são a mesma área nem aplique filtros por coincidência textual solta. Liste possíveis aliases encontrados e qualquer dúvida de classificação.
3. Numere e preserve cada referência original: arquivo, aba, linha/célula, rótulo de indicador. Se a classificação/ordem IND-xx for histórica e não vier da planilha, explicite a correspondência.
4. Transcreva fielmente os campos necessários do requisito no **relatório privado local**, sem corrigir o original silenciosamente.
5. Compare com a planilha auxiliar, destacando diferenças e conflitos sem substituir automaticamente a fonte principal.
6. Classifique cada requisito em:
   - **CLARO E COERENTE**
   - **AMBÍGUO / PRECISA DECISÃO DE NEGÓCIO**
   - **FÓRMULA INCONSISTENTE**
   - **DEPENDENTE DE OUTRA FONTE/ÁREA**
7. Não eliminar requisitos por considerá-los difíceis; deixar qualquer impossibilidade explicitamente justificada e rastreável.

**Obrigatório:** gerar uma lista completa de requisitos em escopo. Se a planilha estiver inacessível, **não afirmar cobertura completa**; registrar bloqueio de origem e encerrar a conclusão de aderência.

## 5. Fase B — matriz de aderência de todos os requisitos em escopo

Para **cada** linha de Seguros / Seguros V6, comparar o requisito com os componentes reais, a telemetria disponível e os widgets do dashboard.

Use matriz obrigatória com colunas:

| Fonte (arquivo/aba/linha) | Responsável | Indicador | Objetivo de negócio | Fórmula/unidade | Eventos e serviços responsáveis | Evidência código | Evidência UAT | Widgets existentes | Cobertura | Lacuna | Ação proposta | Critério de aceite |

Classifique a **cobertura** unicamente como:
- **INTEGRAL E COMPROVADA**
- **PARCIAL E COMPROVADA**
- **EXISTE MAS NÃO VALIDADA EM UAT**
- **NÃO COBERTO**
- **DEPENDENTE DE INSTRUMENTAÇÃO / OUTRO TIME**
- **INVIÁVEL COM OS DADOS ATUAIS**

Quando houver múltiplos subfluxos, separe o detalhamento e não esconda a cobertura parcial sob status integral.

Classifique a **ação** como **MANTER / AJUSTAR / CRIAR / REMOVER / INVESTIGAR-INSTRUMENTAR**. Pode haver mais de uma ação para o mesmo requisito, discriminadas por widget ou subfluxo.

**Regras decisórias:**
- Widget correto e suficiente: **MANTER**.
- Widget útil mas com filtro, unidade, denominação, cálculo ou descrição inadequados: **AJUSTAR**.
- Indicador viável e sem visualização que realmente o atenda: **CRIAR**.
- Widget enganoso, sem sinal defensável, fora de escopo ou verdadeiramente redundante: **REMOVER** como proposta, **depois** de conferir dependências, outros indicadores atendidos e implicações; não remover automaticamente.
- Telemetria insuficiente: **INVESTIGAR-INSTRUMENTAR**, descrevendo o dado e o ponto necessário, sem alterar aplicação.
- Não transformar uma alternativa operacional parcial em cumprimento integral do objetivo original.
- Preservar funções úteis para outros indicadores; a exclusão deve ser justificada no nível do widget, não baseada na preferência por um layout menor.

Verifique se widgets com nomes semelhantes realmente medem a mesma população e se há widgets compartilhados por mais de um indicador.

## 6. Fase C — retomar o IND-03 sob a ótica do OBJETIVO ORIGINAL

Esta é a prioridade de profundidade nesta sessão. Para os demais requisitos de Seguros / Seguros V6, faça **inventário e classificação fundamentada**, sem repetir auditorias extensas já concluídas.

Analise separadamente:
- **I. Falha no processamento automático de cancelamento** (erro de integração, retry, esgotamento, DLQ, resultado local).
- **II. Cobrança indevida após solicitação de cancelamento** (objetivo de negócio, se efetivamente definido na planilha).
- **III. Estorno/compensação de cobrança anterior** (não equivale automaticamente a I nem a II).

Para **II**, investigue a viabilidade de demonstrar:
1. Qual é o **marco temporal válido**? Solicitação recebida, aceite, cancelamento efetivo ou data de vigência? Não presumir.
2. Quais eventos registram o cancelamento e quais registram uma **cobrança efetivamente realizada**, versus tentativa/agendamento?
3. Existe chave segura para cruzar contrato/proposta/produto e, se necessário, operação financeira? A chave é estável e disponível nos dois lados?
4. Qual fonte tem autoria e visibilidade do lançamento/cobrança posterior? Pertence ao escopo do time ou exige consulta externa/BI?
5. Há carimbos de data confiáveis, possibilidade de múltiplos cancelamentos, parcelamentos, reprocessamentos e idempotência?
6. O Datadog consegue fazer o cruzamento exigido de forma correta, ou seria necessário evento derivado, instrumentação ou apuração em fonte transacional/BI?
7. É possível caracterizar **cobrança indevida** sem regra negocial/financeira adicional? Se não, **não chamar qualquer cobrança posterior de indevida**.
8. A cobertura desejada poderia ser observabilidade parcial (suspeitas para investigação), sem declarar conformidade com o objetivo integral?

Confronte as conclusões dos relatórios 22/23 com essas perguntas. **Não desfaça conclusões válidas; acrescente a análise de lacunas que a 05B não cobriu.**

Para I, determine se o saneamento do widget existente é útil, correto e suficiente **somente para o recorte operacional**, e o que permanece sem cobertura do requisito. Não autorize a construção do widget antes de evidência e decisão humana.

## 7. Fase D — evidência Datadog e comparação com o dashboard real

Caso haja acesso em modo leitura:
- Confira a configuração remota **atual**; não confunda dashboard remoto com JSON histórico.
- Catalogue widgets correspondentes a **todos os requisitos selecionados**.
- Examine queries, variáveis, agregações, descrição, unidade, grupo e fonte de cada widget.
- Em logs UAT, para cada consulta realmente executada registre query literal, janela, ambiente, resultado, identificadores e os limites do dado.
- APM Traces/Metrics com hits/erros HTTP **comprovam chamadas**, não cancelamentos únicos, estornos ou cobrança indevida.
- Logs UAT vazios em janelas curtas não provam ausência de instrumentação ou fluxo.
- Se acesso a logs falhar por credenciais/API, não improvisar um método que contorne controles. Ofereça **até 5 consultas prioritárias para validação manual**, separando candidatas de confirmadas.

Não inventar links de Datadog, IDs de widgets, consultas, erros ou campos. Fontes de código, Datadog e relatórios anteriores precisam aparecer rotuladas.

## 8. Fase E — backlog concreto por indicador e proposta de construção futura

Produza, para cada indicador em escopo:
- **Obrigatório pelo requisito:** resultado integral esperado e critério de aceite verificável.
- **Disponível hoje:** evidência/artefato real e sua unidade contável.
- **Diferença (gap):** informação, formulação ou evento ausente.
- **Ação:** manter/ajustar/criar/remover/investigar.
- **Dependências e risco:** mudança em aplicação, validação de logs, outro time, BI, ambiguidade de negócio.
- **Prioridade:** classifique por ganho de cobertura e facilidade, não pelo número de widgets.
- **Próxima etapa técnica recomendada:** preparar candidato local somente quando houver semântica defensável e validação suficiente.

Separe o backlog em:
- **Aprovável para desenho/construção após revisão humana**
- **Precisa validar telemetria no Log Explorer**
- **Precisa definição de negócio**
- **Precisa instrumentação ou participação de outro time**
- **Não implementar com os dados atuais**

Se um indicador tiver cobertura parcial, descreva explicitamente **qual parte da planilha NÃO está atendida**.

Em especial, forneça uma **decisão clara de suficiência para o IND-03**: o ajuste proposto na 05B atende integralmente, parcialmente ou não atende ao objetivo original? A decisão deve ser fundamentada, não baseada apenas no título do widget.

## 9. Entregável local

Criar **somente** o relatório Markdown, sugerido:

**darwin-monitor/planos/24-reconciliacao-aderencia-planilha-seguros-v6-ind03.md**

Conteúdo mínimo:
1. Resumo executivo, escopo e grau de confiança.
2. Fontes consultadas e limites de acesso.
3. Inventário **completo** das linhas de Seguros / Seguros V6 das planilhas (incluindo divergências documentais).
4. Matriz de aderência requisito ↔ código ↔ logs/métricas ↔ widget ↔ ação.
5. Lista de widgets mantidos, ajustáveis, a criar, a remover ou dependentes — **somente proposta**, sem alterações.
6. Reavaliação do IND-03: falha operacional versus cobrança posterior, incluindo suas lacunas.
7. Evidências Datadog executadas versus consultas apenas candidatas.
8. Backlog priorizado, critérios de aceite e dependências por indicador.
9. Proposta do próximo trabalho incremental, sem mudanças remotas.
10. Lista exata de arquivos criados e declaração de nenhuma outra alteração.

É aceitável que alguns itens permaneçam desconhecidos, **desde que isso seja declarado por linha**. Não declarar “todos os indicadores atendidos” sem validação individual.

## 10. Critérios de aceite e encerramento obrigatório

- [ ] Todas as linhas atribuídas a Seguros / Seguros V6 foram inventariadas a partir da planilha real.
- [ ] Os requisitos foram confrontados com widgets, código e telemetria; fontes e nível de prova diferenciados.
- [ ] Cada indicador tem cobertura classificada, lacuna e ação proposta.
- [ ] Foram propostas criações **quando necessárias**, remoções **quando justificadas**, ajustes **quando úteis** e manutenção **quando já adequado**.
- [ ] O IND-03 foi reavaliado quanto ao objetivo de cobrança após cancelamento, sem confundi-lo com falha técnica nem estorno.
- [ ] Nenhuma fórmula inconsistente foi implementada ou apresentada como válida.
- [ ] Logs não executados não foram declarados validados.
- [ ] Indicadores concluídos anteriormente não foram modificados.
- [ ] Relatório 24 criado e relido, com registro de pendências.
- [ ] Nenhum JSON, script, Java, widget, Git remoto, Datadog, pipeline ou deploy foi alterado.

**Resumo final no terminal:** quantidade de linhas por responsável, classificação de aderência por indicador, principais lacunas, decisão sobre o IND-03, prioridades, caminho do relatório 24, arquivos criados e confirmação de nenhuma alteração remota.

**PARE.** Aguarde decisão humana antes da próxima etapa de desenho/construção do candidato e, em etapa posterior, de eventual publicação controlada. **Nenhuma autorização de publicação é concedida por este prompt.**
