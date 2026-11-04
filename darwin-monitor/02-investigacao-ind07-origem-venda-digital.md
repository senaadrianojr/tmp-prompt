# Darwin Monitor — Prompt 02: Investigação e validação do IND-07 (Origem DXC × Pismo)

## Missão — um indicador, uma aplicação

Você é o investigador técnico de observabilidade do projeto Darwin Monitor. Nesta execução, investigue **exclusivamente o IND-07 — Origem (Contratos DXC × Pismo)**, na aplicação **springboot-pseg-base-venda-digital**. **Não avance para outros indicadores nem outras aplicações.**

Contexto: o projeto Darwin acompanha o desmembramento/migração de partes do monólito venda-digital; no novo cenário, venda-digital passará a atender Pismo. O objetivo da observabilidade é detectar alterações inesperadas nos fluxos sob responsabilidade do time antes, durante e depois da migração. Não é preciso comprovar processamento interno de serviços pertencentes a outros times.

**Regra principal: um indicador parcialmente mensurável não deve ser descartado.** Quando a métrica integral não for demonstrável, proponha um widget de observabilidade parcial (por exemplo, volume de solicitações classificadas por processadora, erros locais de classificação ou ausência de processadora), com nome fiel ao que ele mede, limitações e gap para o KPI original. Jamais apresente esse widget parcial como realização integral de "contratos efetivados".

**Permissão nesta execução: leitura de planilhas, documentos, código e telemetria de UAT. Sem alterações de código, instrumentação, queries salvas, widgets, dashboards, monitores, permissões ou configurações.**

## Fontes iniciais e precedência

1. Leia o inventário produzido pelo Prompt 01 em `docs/darwin-monitor/planos/04-inventario-filtrado-indicadores.md`; localize o **IND-07 (linha 19 da planilha original, conforme o inventário)**. Se houver divergência, confira diretamente `docs/darwin-monitor/Monitoramento Cartões 1.xlsx`, aba **Monitoramento**. Registre nome, objetivo e fórmula exatamente como constam no arquivo real.
2. Use a planilha auxiliar **Monitoramento - Planejamento.xlsx**, aba **Plan1**, somente como pista de aplicação e fluxo. Não a trate como validação do KPI.
3. Investigue o código **real** no workspace de `repos/springboot-pseg-base-venda-digital`. Registre branch, commit e arquivos realmente inspecionados; não presuma que seja a versão de UAT.
4. Use `docs/darwin-monitor/dashboards/03-dashboard-alvo.md` e `docs/darwin-monitor/planos/03-relatorio-validacao-dashboard-uat-e-plano-correcao-gaps.md` **apenas como histórico de hipóteses a verificar**. O fato de um widget ou relatório chamar a query de "validada" não é evidência.

Se os arquivos estiverem em caminhos equivalentes ou com grafia ligeiramente diferente, localize-os e registre os caminhos reais. Se faltar a planilha original, o inventário ou o repositório da aplicação, **não invente conteúdo**: identifique o bloqueio e interrompa as etapas dependentes.

## Passo 1 — Significado exato do indicador

Identifique separadamente:

- **Requisito da planilha**: "Origem"; objetivo "Origem do Contrato"; fórmula/métrica "Contratos DXC x Pismo" (confirme no documento, não use esta frase como substituta da leitura).
- **Unidade de contagem**: tentativa de contratação, proposta, contrato efetivado, evento publicado ou outra unidade? Não assuma equivalência.
- **Momento da decisão**: em qual condição concreta a aplicação sabe se a operação corresponde a DXC ou Pismo?
- **Ponto de medição mais útil para monitoria**: entrada, roteamento, conclusão local, saída ou resposta recebida? Separe as possibilidades e os resultados que cada uma realmente prova.
- **Riscos de duplicidade**: retries, reentradas, fluxos de titular/adicionais, chamadas paralelas e múltiplas mensagens para uma mesma proposta.
- **Mudança de escopo após migração**: a redução de DXC dentro do monólito pode ser **esperada**, não uma falha; não compare volumes absolutos sem ressalvas sobre roteamento, tráfego e fronteiras monitoradas.

Se "contratos" na planilha não puder ser demonstrado pela telemetria disponível, mantenha a oportunidade de entregar **um indicador operacional parcial**, claramente identificado como tal.

## Passo 2 — Rastrear no código somente o fluxo relevante

Comece por **VendaDigitalService.java**, especialmente os métodos `solicitarSeguro` e `solicitarSeguroTitularEAdicionais`, **se existirem no código atual**. Siga apenas as chamadas necessárias para encontrar o ponto em que a origem/processadora é decidida e, se acessível, o desfecho local da operação.

Pistas adicionais, **não fatos estabelecidos nem limites de investigação**:
- `CartQuinaDto`, informações de processadora, descrições, `isPismo()` ou equivalentes.
- `LogMapper.java`, métodos de extração da processadora ou formatação de eventos.
- Campos de telemetria `processadora`, `tipo_evento`, `canalOrigem` e `canalVenda`. **Não confunda `canalOrigem` com `canalVenda`; confirme seus significados reais**.
- Possível fallback de DXC em contratos legados: verifique a regra no código. **Não substitua ausência de valor por "DXC" sem prova de que essa inferência é válida em todos os casos alcançados**.

Para cada condição relevante, registre:
- repositório, branch/commit, classe, método e pequeno trecho ou expressão observada;
- o que a condição comprova;
- se ela ocorre antes ou depois de confirmação local de sucesso;
- pontos distintos para DXC, Pismo, processadora desconhecida e erro;
- eventuais dependências de serviços externos; **observe apenas nossa fronteira**.

Evite uma busca ampla e irrestrita por toda a solução. Use a entrada indicada e siga os componentes efetivamente chamados. Se não encontrar o caminho relevante após verificar a entrada e seus colaboradores diretos, registre a lacuna em vez de propor outra arquitetura por suposição.

## Passo 3 — Auditar a instrumentação existente

No ponto concreto de decisão/evento, identifique se já existem:
- log **realmente emitido** com a processadora e o tipo de evento (diferencie mensagem literal, argumentos interpolados e campos estruturados);
- campos de contexto MDC, métricas, eventos ou atributos de span;
- informação suficiente para distinguir **solicitação recebida**, **operação roteada**, **operação concluída localmente**, **erro local** e **contrato efetivado**;
- lógica de limpeza do MDC e contexto assíncrono, quando pertinente;
- um identificador de correlação para investigação, **sem sugerir identificadores únicos de clientes/propostas como dimensão de métrica de alta cardinalidade**.

Uma chamada a um `logger` pode provar a existência de emissão potencial no código, **não a chegada do registro ao Datadog**. Não use como query uma mensagem apenas inferida de nome de método ou comentário.

Se **não houver log/métrica no ponto certo**, proponha a instrumentação mínima e não invasiva: arquivo/método, condição exata de emissão, nível, nome do evento, campos estáveis e de baixa cardinalidade, motivo e teste proposto. **Não altere o código.** Evite dados pessoais ou identificadores sensíveis em mensagens de log.

## Passo 4 — Comprovar a telemetria em Datadog UAT (somente leitura)

Identifique o mecanismo **já existente** de acesso ao Datadog por ferramenta Python/cookies no workspace. Reutilize sua capacidade de **consultar**, sem reconstruir o utilitário, sem imprimir/exportar cookies/tokens e sem executar operações de escrita (POST/PATCH/PUT/DELETE ou equivalentes). Não mude dashboards para testar.

1. Confirme organização e ambiente de UAT a partir da sessão acessível, sem registrar identificadores internos no relatório público.
2. Pesquise pelos **elementos literais/atributos encontrados no código**, iniciando pelo serviço Datadog real; confirme se o nome do serviço e o ambiente de fato existem.
3. Faça buscas exploratórias curtas nos Logs Explorer; se não houver dados, identifique se isso decorre da janela sem tráfego, permissão, indexação/armazenamento, ausência de emissão ou impossibilidade de distinguir — **não conclua pela inexistência do evento**.
4. Avalie APM traces e métricas somente se constituírem fonte relevante **e o atributo requerido estiver de fato disponível naquela fonte**. Uma tag em span não é automaticamente dimensão de métrica APM.
5. Registre período investigado, fonte, filtro usado, quantidade de resultados ou erro retornado e como a observação sustenta (ou não) o uso no widget.
6. Não declare a query "validada" porque retorna algum número. Ela precisa representar a unidade de contagem proposta e a processadora corretamente.
7. Se UAT não tem ocorrências disponíveis, classifique como **não validada empiricamente por falta de eventos observáveis**, e indique como realizar um teste controlado depois; não fabrique amostras.

**Limite de tentativas:** comece com uma consulta exploratória ampla sobre o serviço/ambiente e refine com **até duas consultas diagnósticas baseadas em novos fatos**. Se a hipótese não for sustentada, interrompa a tentativa e explique a lacuna. Não entre no ciclo de gerar queries alternativas por tentativa e erro.

Se o utilitário atual não suportar leitura confiável da fonte necessária, **pare nessa etapa e documente a limitação**; não modifique scripts nem contorne controles corporativos.

## Passo 5 — Proposta do(s) widget(s) e de query(s)

Escolha o menor conjunto que entrega valor operacional real. Dê prioridade à **representatividade e confiabilidade** sobre a aparência do dashboard.

- **Atendimento integral**, somente se houver prova de que o evento representa a unidade contratual exigida.
- **Atendimento parcial**, se só houver telemetria de entrada, distribuição de roteamento, solicitações ou erros locais. Proponha um nome explícito, como "Solicitações observadas por processadora (DXC × Pismo)" — **apenas se for isso que a evidência prova**.
- **Gap de instrumentação**, se o dado importante pode ser emitido pela aplicação mas não está disponível na fonte.
- **Dependência externa**, se a confirmação de contrato requer outro sistema; descreva a fronteira e entregue, quando possível, widget parcial do nosso lado.

Para cada widget candidato, registre **fonte de dados**, **nome**, **evento contado**, **filtros e agrupamentos reais**, **tipo do widget**, **regra de deduplicação ou ressalva sobre duplicações**, **query exata quando verificável**, **status de teste** e **limitação semântica**.

**Não invente queries.** Se não for possível confirmar sintaxe, campos ou retorno, forneça a lógica de filtragem esperada e marque a query como **PENDENTE**, sem apresentá-la como executável.

Para comparações durante a migração, recomende medidas úteis e coerentes com a cobertura disponível (distribuição por processadora, evolução do volume **por fronteira**, taxa de erros de roteamento ou classificação). Diferencie claramente mudança esperada de tráfego de possível regressão.

## Passo 6 — Registrar o resultado e encerrar

Crie **um único Markdown local**, em `docs/darwin-monitor/planos/05-investigacao-ind07-origem-venda-digital.md` (ou caminho equivalente existente). Se o arquivo já existir, preserve-o e crie versão incremental; não sobrescreva sem autorização.

Estrutura obrigatória e concisa:

1. **Escopo e versões**: indicador, fontes, branch/commit e limitações da comparação código × UAT.
2. **Regra e fronteira**: definição do KPI original, evento observável, diferença entre tentativa/roteamento/sucesso local/contrato efetivado.
3. **Evidência de código**: tabela classe/método → condição observada → significado → instrumentação existente/ausente.
4. **Evidência Datadog UAT**: fonte, intervalo, filtros testados e resultado. Escreva "NÃO EXECUTADO" quando aplicável.
5. **Conclusão de cobertura**: **INTEGRAL / PARCIAL / NÃO VALIDADA / BLOQUEADA**, com justificativa; diferencie "código existente" de "telemetria comprovada".
6. **Widget(s) recomendados**: no máximo 2, apenas com fundamento; inclua consulta **validada** ou condição para validá-la.
7. **Gap(s) e instrumentação proposta**: apenas se necessários, sem implementação.
8. **Teste controlado sugerido**: como confirmar DXC e Pismo em UAT, sem uso de dados reais sensíveis, indicando quem precisa disponibilizar tráfego de teste.
9. **Próxima ação concreta**: uma única ação de menor custo para destravar a implementação.

Ao terminar, responda no chat **somente**: caminho do arquivo, estado de cobertura, evidência mais importante, widget principal sugerido, query validada (SIM/NÃO) e próximo passo. **Não crie widgets nem execute outro indicador nesta sessão.**

### Regra de qualidade final

Se o KPI completo não puder ser medido, **não descarte o indicador**: apresente explicitamente o que a nossa aplicação permite monitorar hoje e o que falta para atender o requisito original. Não chame uma métrica parcial de "100% validada", nem use ausência de dados como motivo para inventar eventos ou queries.
