# Prompt 05B — Desenho Local Controlado do IND-03 (Falhas no Cancelamento)

> **Uso:** nova sessão do Gemini CLI, executada dentro do workspace corporativo autorizado. Este prompt é público e contém apenas instruções metodológicas. Todos os nomes internos, identificadores de widgets, classes, queries, fluxos, métricas, prints e evidências devem ser lidos e permanecer **exclusivamente nos arquivos corporativos locais**. Não envie relatórios ou dados internos para o repositório público de prompts.

## 1. Papel, ponto de partida e objetivo

Atue como **projetista técnico de observabilidade e revisor crítico**, não como agente de implantação.

A etapa anterior (05A) auditou o indicador **IND-03 — Falhas no Cancelamento** e gerou um relatório técnico local no diretório de planos do projeto (identificado pelo prefixo **22-** e pelo assunto **ind03 / falhas-cancelamento**).

**A etapa 05B deve produzir somente o desenho técnico LOCAL e revisável da cobertura operacional do IND-03**, a partir das conclusões efetivamente demonstradas no relatório 22 e de leitura seletiva das fontes nele referenciadas.

A auditoria anterior apontou, em termos gerais:
- A especificação de negócio possui inconsistências entre nome, objetivo e fórmula. **Não construir percentual agregado sem população, fórmula e desfechos conciliáveis.**
- Cancelamento, retentativa e estorno são operações diferentes; não misturá-los.
- Já existe um widget de falhas de cancelamento com indícios de filtro amplo, possivelmente contaminado por eventos de estorno.
- Há sinais de entrada, falha transitória, esgotamento e conclusão local, mas **a execução de consultas candidatas em logs UAT continua pendente de comprovação**.
- APM registra tentativas HTTP; seus erros **não equivalem** a falhas terminais por cancelamento único.
- Possíveis lacunas de identificador/correlação foram encontradas em código. **Não corrigi-las na 05B**.
- O indicador anterior (IND-02) já foi consolidado, publicado e validado pelo responsável: **não reabrir nem alterar**.

Esses são **achados reportados**, não autorização para assumir que todas as queries funcionam. Leia o relatório e valide o grau de confiança de cada premissa. Não refaça a auditoria inteira.

**Entregável principal:** plano Markdown local com proposta mínima de evolução de widgets e filtros, consultas candidatas separadas das efetivamente testadas, matriz de validação, impactos e decisão para revisão humana. **Nenhuma construção ou publicação está autorizada.**

## 2. Limites operacionais — obrigatórios

**Permitido:**
- Leitura seletiva do relatório 22, das seções relevantes do requisito, da configuração existente do dashboard, das definições dos widgets e de trechos de código apontados pela auditoria.
- Consultas **somente de leitura**, no Datadog UAT já autenticado e autorizado, se realmente acessível. Registrar fonte, query literal, janela e resultado; se indisponível, classificar como pendente.
- Cálculos e comparações locais, sem efeitos colaterais.
- Criar **um** relatório Markdown novo na pasta local de planos do projeto, preferencialmente com prefixo **23-** e nome referente ao desenho do IND-03.

**Proibido:**
- Editar, criar, remover, duplicar ou reorganizar widgets, grupos, streams ou monitores no Datadog.
- Modificar JSON canônico, export, candidato de dashboard ou scripts de publicação.
- Executar publicador, dry-run com efeitos desconhecidos ou qualquer chamada remota de escrita.
- Alterar Java, testes, configuração, instrumentação, logs, identificadores ou outras aplicações.
- Fazer commits, pushes, PRs, pipelines, builds com efeitos colaterais, deploys ou modificações de autenticação.
- Corrigir automaticamente o aparente problema de interpolação de logs ou instrumentação encontrado no relatório 22.
- Introduzir números, consultas, condições de negócio ou resultados fictícios.
- Publicar conteúdo corporativo em GitHub ou outro destino público.

A única escrita permitida é **o novo relatório local**. Se o destino proposto já existir, preserve-o e use nome alternativo; não sobrescreva sem autorização.

## 3. Leituras necessárias — curtas e seletivas

1. Localize o relatório 22 efetivo e leia **resumo, requisitos, sinais, telemetria UAT, inventário de widgets, matriz, recomendações e pendências**.
2. Identifique a **definição atual do widget legado** de falhas no cancelamento (ID, título, tipo, queries, group, filtros, agregações). Diferencie **configuração remota atual** de **cópias locais potencialmente desatualizadas**. Não assuma que o canônico corresponde ao remoto.
3. Identifique os widgets que já cobrem volume de cancelamento, estorno e outros sinais relacionados; evite duplicá-los.
4. Consulte trechos de código **apenas quando necessários para confirmar o texto exato de um marcador, condição de log ou limitação**.
5. Identifique a fonte documental e o nível de confiança de cada conclusão.

**Não reproduza longos relatórios nem refaça análises já encerradas.** Se o relatório 22 não existir ou não puder ser lido, registre impedimento e pare sem inventar proposta fundamentada.

## 4. Pacote mínimo de desenho para o IND-03

Trabalhe **na ordem a seguir**, sem aplicar as modificações.

### 4.1 Prioridade 1 — saneamento conceitual do widget existente

Determine se o widget atual pode ser corrigido **apenas por desenho de filtro/título/descrição** para exibir exclusivamente um sinal observável de **esgotamento de tentativas de cancelamento**, excluindo estorno e reprocessamentos de natureza diferente.

Entregue:
- Estado atual constatado e fonte (remoto, export ou canônico).
- Problema semântico e risco de contaminação/deduplicação.
- Título proposto que expresse a **unidade realmente contada** (por exemplo, ocorrência ou mensagem de esgotamento, não necessariamente proposta única).
- Descrição curta e explícita sobre o significado do indicador e suas limitações.
- Query **candidata**, baseada em marcador literal e campo reais, com explicação de cada filtro e exclusão; preservar variáveis/template do dashboard quando aplicável.
- Possibilidade de haver **múltiplas emissões do sinal terminal** por reentrega, reprocessamento de DLQ ou caminhos paralelos: não declarar risco nulo sem evidência.
- Comparação da semântica do widget antes/depois, sem números inventados.
- Evidência exigida antes de futura publicação.

**Se o filtro exclusivo não puder ser demonstrado**, proponha que o widget seja marcado como pendente no plano, em vez de aprovar um filtro apenas plausível.

### 4.2 Prioridade 2 — retentativas como pressão operacional

Avalie se o sinal de NACK/retry acrescenta uma pergunta operacional útil **não respondida por widgets existentes**.

Esclareça a unidade (tentativa, mensagem, chamada externa) e os riscos de multiplicação. Não o chame de quantidade de cancelamentos com falha.

Inclua uma proposta de widget **somente se houver marcador real, separação segura de estornos e utilidade operacional comprovável**. Se a evidência ainda for insuficiente, registre como opção condicionada à validação de logs, não como item aprovado.

### 4.3 Escopo financeiro — somente decisão de fronteira

Verifique se existe widget específico para estorno e se a proposta de criar outro seria duplicação.

**Não criar proposta automática de novo widget de estorno na 05B do IND-03.** Documente apenas a separação conceitual e uma recomendação de tratamento posterior no indicador/escopo financeiro correspondente, caso haja lacuna real.

### 4.4 Volume/entrada, sucesso local e APM

Não duplicar widgets já existentes para contabilizar solicitações ou volume.

Se algum sinal de APM puder apoiar a investigação ou um tooltip/descrição, explicite que reflete **tentativas HTTP**, não falhas terminais nem contratos únicos. Um percentual de erros técnicos em UAT não deve ser rotulado como taxa de falha de cancelamento.

Sucesso HTTP local ou emissão de evento pelo orquestrador não deve ser apresentado como resultado de negócio confirmado por toda a cadeia sem evidência de reconciliação.

## 5. Validação efetiva das consultas — sem inventar dados

Para cada query candidata (principal e eventual de retry), siga:

1. Confirme marcador/campo, serviço e escopo de fluxo **no código ou fonte observada**; não suponha atributo, prefixo ou status.
2. Se houver acesso de leitura ao Log Explorer, execute a query exatamente, em janela indicada, e registre:
   - data/hora e fuso do acesso;
   - intervalo e ambiente selecionados;
   - query literal;
   - quantidade de resultados, quando retornada;
   - se havia tráfego representativo;
   - evidências de que cancelamento foi incluído e estorno excluído;
   - disponibilidade real da correlação estruturada, quando aplicável.
3. Se a query retornar zero, classifique **SEM EVIDÊNCIA NA JANELA**, e **não** como validada nem como evento inexistente.
4. Se não puder acessar os logs por limitações de API/sessão, registre **NÃO EXECUTADA — AGUARDA VALIDAÇÃO HUMANA**. Uma query sintaticamente plausível não é prova de funcionamento no Datadog.
5. Se a interface permitir apenas validação manual pelo operador, forneça um roteiro curto de no máximo 3 a 5 consultas prioritárias, com o que confirmar visualmente em cada uma.
6. Se houver alteração de schema, campo ausente ou lacuna de MDC, documente como **dependência de instrumentação**, separada da proposta de dashboard.

**Não use contagem de logs como equivalente a cancelamentos únicos.** Caso os resultados mostrem comportamento inesperado, registre a inconsistência e não aprove a query.

## 6. Matriz de decisão — anti-duplicação e baixo risco

Para cada opção (saneamento do widget, retry, novos widgets potenciais), registre:

| Opção | Pergunta operacional | Sinal real | Widget existente? | Unidade | Evidência UAT | Risco de duplicação | Decisão |
|---|---|---|---|---|---|---|---|

Classifique a decisão exclusivamente como:
- **PROPOR PARA 05C — SUJEITO À APROVAÇÃO HUMANA**
- **CONDICIONAR À VALIDAÇÃO UAT**
- **NÃO CRIAR — COBERTURA EXISTENTE**
- **ADIAR — OUTRO ESCOPO / INSTRUMENTAÇÃO**
- **DESCARTAR — SEM BASE CONFIÁVEL**

A classificação **PROPOR PARA 05C** exige, no mínimo, semântica defensável, query fiel a sinais reais, evidência de escopo e ausência de duplicação óbvia. A validação humana segue obrigatória. Se UAT não tiver logs representativos, explicitar a validação faltante e os critérios de desbloqueio.

## 7. Plano de verificação e publicação futura (não executar)

Descreva o roteiro de aceite que uma **etapa separada 05C** deverá seguir, **sem executar agora**:

- Obter um **novo snapshot/export remoto confiável** imediatamente antes de qualquer alteração, sem presumir atualidade dos arquivos históricos.
- Conferir identidade, localização e tipo dos widgets e preservar mudanças manuais.
- Preparar candidato JSON **somente na etapa autorizada**, com mudança mínima e diff semântico limitado aos widgets aprovados.
- Checar preservação dos grupos, widgets, métricas, streams e indicadores anteriores; nenhuma alteração colateral.
- Confirmar comportamento do script de validação/dry-run no código antes de executá-lo.
- Verificar queries e contagem na UI com os mesmos filtros, variáveis de ambiente e janelas.
- Exibir diff/impacto e solicitar **autorização expressa** antes de qualquer escrita remota.
- Após eventual publicação autorizada, validar visualmente o dashboard remoto e registrar evidências.

Não trate essas ações futuras como permissão antecipada.

## 8. Entrega local obrigatória

Crie somente um relatório Markdown local em **darwin-monitor/planos/**, preferencialmente:

**23-desenho-local-ind03-falhas-cancelamento.md**

Estrutura mínima:
1. Escopo e restrições da etapa 05B.
2. Fontes consultadas e classificação (remoto, arquivo local, código, inferência).
3. Resumo dos achados herdados do relatório 22 (sem reauditar).
4. Inventário dos widgets correlatos, com fonte e estado de atualização.
5. Proposta mínima para o widget existente: antes/depois, título, descrição, unidade e query candidata.
6. Avaliação da utilidade de retries/NACKs.
7. Separação de cancelamento, estorno e indicadores financeiros.
8. Testes de consultas UAT (executadas versus candidatas).
9. Matriz de decisão e riscos de contagem/deduplicação.
10. Critérios de aceite e bloqueios para a etapa 05C.
11. Resumo para revisão humana.
12. Controle de alterações locais e confirmação de ausência de modificações remotas.

Se não houver acesso ao remoto ou tráfego de logs, entregue um plano **condicionado**, claramente identificado, sem travar o progresso conceitual.

## 9. Critérios de aceite e parada

Antes de finalizar, confirme explicitamente:
- [ ] Relatório 22 lido e contexto do IND-02 preservado.
- [ ] Estado de cada widget referenciado rastreado à fonte; remoto e canônico não confundidos.
- [ ] Proposta separa cancelamento de estorno.
- [ ] Nenhuma taxa percentual sem base compatível.
- [ ] Nenhum log, retry, HTTP hit ou evento foi rotulado como cancelamento único sem prova.
- [ ] Cada consulta está marcada como executada/validada, executada sem evidência ou apenas candidata.
- [ ] Não foram propostos widgets duplicados sem justificativa excepcional.
- [ ] Relatório 23 foi criado e relido.
- [ ] Nenhum código Java, teste, script, JSON ou Datadog foi alterado.
- [ ] Nenhum commit, push, PR, pipeline ou deploy foi realizado.

No resumo final no terminal, informe: **(A)** decisão sobre widget existente; **(B)** retry e escopo financeiro; **(C)** evidências efetivamente obtidas e lacunas; **(D)** nome/caminho do relatório; **(E)** lista exata de arquivos criados/alterados; **(F)** confirmação de ausência de escrita remota.

**PARE após entregar o relatório 23.** Aguarde a revisão humana. **Não inicie a etapa 05C nem publique nada.**
