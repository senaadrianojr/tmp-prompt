# DARWIN MONITOR — ETAPA 02 | CORREÇÕES PONTUAIS APÓS REVISÃO INDEPENDENTE

## Objetivo
A revisão independente da Etapa 02 concluiu **NÃO APTO**, não por falta de novo discovery, mas porque `REVALIDACAO_TECNICA_ETAPA_02.md` perdeu ou omitiu evidências, manteve classificações tecnicamente injustificadas e não apontou arquivos reais para a Etapa 03.

**Autorizo corrigir somente esse relatório**, aproveitando os documentos e evidências já existentes. Não recomece o discovery nem inicie a Etapa 03.

## Preflight
1. Confirme que a execução ocorre na raiz real de `darwin-monitor/`; não recrie `docs/darwin-monitor/` dentro dela.
2. Leia o relatório atual `./REVALIDACAO_TECNICA_ETAPA_02.md` e o parecer de revisão independente da sessão atual, além de `./AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md` e dos arquivos pertinentes de `indicadores/`, `dashboards/`, `planos/`.
3. Use código-fonte, queries e histórico Git **somente em leitura e de maneira direcionada** para restaurar referências já identificadas; não invente evidências.
4. Se o resumo da sessão anterior contradisser documentos ou código, prevalecem as fontes diretamente verificadas.
5. Não obtenha nem imprima cookies, chaves ou credenciais. Acesso ao Datadog, se imprescindível para sanar uma lacuna, deve ser apenas de leitura e pelo procedimento corporativo já autorizado. Evite consultas repetitivas desnecessárias.

## 1. Corrigir seção 2 — Fontes de evidências utilizadas
Substitua referências vazias/genéricas por informações rastreáveis:
- Repositórios reais de Gestão Eventos Conta Cartão, Gestão Eventos Pagamento e Venda Digital, com paths, classes/métodos e branch/commit quando disponíveis.
- Planilhas lidas: arquivo, aba, linha/célula da coluna `Responsável`, separando `Seguros` de `bvseg`.
- Datadog: dashboard/widget identificável, query completa apenas se realmente encontrada, tipo de sinal e resultado observado quando houver. **Configuração consultada não equivale à execução da query nem à validação dos valores.**
- Documento de suporte, seção ou evidência que ampare cada alegação.

Se não conseguir recuperar um identificador, registre `NÃO COMPROVADO` e a ação necessária. Proibidos placeholders como `****`, `...` como referência, `ID:` vazio ou `widget 0` sem identificação real.

## 2. Corrigir seção 3 — Matriz dos seis indicadores
Trabalhe exclusivamente com:
1. Índice de Sucesso — Gestão Eventos Conta Cartão.
2. Taxa de Falha — Gestão Eventos Conta Cartão.
3. Taxa de Falha no Cancelamento — Gestão Eventos Conta Cartão e Gestão Eventos Pagamento.
4. Taxa de Reprocessamento — Gestão Eventos Conta Cartão.
5. Parcela Cobrada × Parcela Lançada — Gestão Eventos Pagamento.
6. Origem — Venda Digital.

A revisão independente identificou:
- **Índice de Sucesso:** sucesso técnico HTTP não demonstra sucesso funcional; verificar evidência de Evento 31/status 8 no código e disponibilidade de sinal antes de propor instrumentação.
- **Taxa de Falha:** o documento `indicadores/02-recorrencia-taxa-de-falha.md` indica logs existentes (`EVENTO-31|FALHA`). A ausência de widget **não comprova** necessidade de instrumentação; distinguir `DEPENDE DE QUERY/WIDGET` de `DEPENDE DE DADO NOVO`.
- **Taxa de Falha no Cancelamento:** a existência de log de falha ou widget com falhas não comprova uma **taxa** sem numerador, denominador, população elegível, período e tratamento de retries. Se faltar qualquer componente essencial, classificar `MÉTRICA PARCIAL / TAXA NÃO COMPROVADA`.
- **Taxa de Reprocessamento:** DLQ e retries não comprovam taxa de operações reprocessadas. Verificar numerador, denominador, deduplicação e query correspondentes; caso contrário, `MÉTRICA PARCIAL / TAXA NÃO COMPROVADA`.
- **Parcela Cobrada × Parcela Lançada:** manter dependência de decisão humana sobre a responsabilidade `bvseg` e as fontes técnicas, sem confundir bloqueio de escopo com inviabilidade técnica.
- **Origem:** manter pendência de responsabilidade `bvseg` e da definição funcional; não equiparar Origem a MOB e não fixar Pismo/DXC como regra aprovada.

Cada uma das seis linhas deve apresentar: regra funcional, evidência de código, evidência de planilha, evidência Datadog (com ressalvas), numerador/denominador quando aplicáveis, disponibilidade atual, razão da classificação e próximo passo.

**É proibido manter `CALCULÁVEL HOJE` para uma taxa sem demonstrar ambos os componentes do cálculo e a correspondência da população.** Não mantenha categorias otimistas apenas porque foram usadas na sessão anterior.

## 3. Corrigir seção 4 — Plano de alterações da Etapa 03
A seção deve apontar **arquivos realmente existentes**, nunca `****` ou caminhos presumidos.

Para cada alteração, apresente uma tabela com:
- caminho relativo verificado;
- seção do arquivo a atualizar;
- mudança concreta necessária;
- evidência que a sustenta;
- pendência funcional a preservar;
- critério de aceite documental.

Comece pelos documentos dos quatro indicadores da área Seguros, dashboards MVP/alvo e planos de instrumentação relevantes. Mantenha os dois registros `bvseg` como pendências explícitas de escopo para decisão da PO, sem eliminá-los da documentação.

A Etapa 03 é **consolidação documental**, não criação de widgets ou instrumentação em produção.

## 4. Integridade e escopo de escrita
- Modifique **somente** `./REVALIDACAO_TECNICA_ETAPA_02.md`.
- Preserve todas as evidências válidas já existentes; evite substituir conteúdo detalhado por resumo genérico.
- Não altere README, guia de ferramentas, planilhas, código, configurações, dashboards, widgets ou monitores.
- Não crie arquivos adicionais, backups, subdiretórios, revisões paralelas, commits ou pushes.
- Faça um diff final e procure campos vazios/placeholders. Referências desconhecidas devem ser apresentadas como pendências explícitas, nunca inventadas.
- Não repita todo o discovery: use verificações pontuais apenas quando necessárias para corrigir erro material no documento.

## 5. Gate da Etapa 02
Depois das correções, indique uma das seguintes decisões:
- **APTO PARA ETAPA 03:** relatório completo, rastreável, com classificações tecnicamente justificadas.
- **APTO COM PENDÊNCIAS DOCUMENTADAS:** relatório íntegro, mas há decisões funcionais ou fontes não comprovadas claramente identificadas; a consolidação pode registrar o estado atual e o alvo sem inventar.
- **NÃO APTO:** lacunas documentais importantes ainda tornam o relatório insuficiente.

O fato de algumas taxas não serem calculáveis hoje **não impede automaticamente** a Etapa 03 documental; impede apenas declará-las prontas ou implementá-las como se estivessem validadas.

## 6. Resposta final
Informe:
1. O único arquivo atualizado e a confirmação por diff de que os demais permaneceram intocados.
2. Quais evidências foram restauradas e de onde vieram.
3. Classificação **antes → depois** de cada indicador afetado, com justificativa.
4. Numeradores, denominadores e queries realmente confirmados versus pendentes.
5. Arquivos reais previstos para a Etapa 03.
6. Pendências de PO/Negócio, especialmente `Seguros`/`bvseg` e definição de Origem.
7. Resultado fundamentado do gate.

**Não inicie a Etapa 03. Aguarde minha aprovação.**
