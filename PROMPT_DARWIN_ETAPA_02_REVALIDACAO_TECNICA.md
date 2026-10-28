# DARWIN MONITOR — ETAPA 02/04
## Revalidação técnica baseada em evidências | Escopo reduzido de seis indicadores

> **Natureza da etapa:** investigação técnica e diagnóstico. **Não é etapa de implementação, reescrita documental ou arquivamento.**
>
> **Regra de saída:** produzir ou atualizar **somente** `./REVALIDACAO_TECNICA_ETAPA_02.md` na raiz real do projeto Darwin Monitor. Não criar `docs/darwin-monitor/` dentro de `darwin-monitor/`.

## 1. Papel, missão e fontes de verdade

Atue como engenheiro sênior de software, observabilidade e arquitetura de integrações, com postura investigativa e rigor documental.

Sua missão é **revalidar tecnicamente** o material atual do Darwin Monitor para os **seis indicadores aprovados para investigação**, confrontando as alegações documentais com o **código realmente disponível**, integrações e, quando houver acesso autorizado, **evidências de telemetria real no Datadog**.

**Hierarquia de evidências:**
1. **Regra de negócio:** definição validada por Negócio/PO e fontes funcionais originais. O código pode demonstrar comportamento implementado, mas não substitui validação funcional.
2. **Comportamento técnico:** código-fonte efetivamente verificado, na branch/commit identificados; integrações, schemas, contratos e testes.
3. **Disponibilidade operacional:** consultas realmente executadas e resultados observados no Datadog ou em fontes técnicas autorizadas.
4. **Documentação anterior:** hipótese/insumo a ser testado, nunca evidência definitiva por si só.
5. **Inferência:** declarar como hipótese, indicando exatamente o que falta verificar.

Não transforme "poderia funcionar" em "está implementado". Nem um endpoint, nome de log ou widget documentado prova a existência de dados suficientes em produção.

## 2. Raiz do workspace e preflight obrigatório

O agente pode iniciar **já dentro** de `darwin-monitor/`. Antes de executar comandos ou criar arquivos:

1. Verifique o diretório de trabalho efetivo (`pwd` ou equivalente).
2. Localize os arquivos reais `README.md`, `AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md` e pastas `indicadores/`, `dashboards/`, `planos/`.
3. **Não crie** uma estrutura `docs/darwin-monitor/` dentro dessa raiz. Todos os caminhos de escrita devem ser resolvidos em relação ao diretório real confirmado.
4. Leia a **versão corrigida** da auditoria da Etapa 01, incluindo escopo, inventário, fontes, pendências e classificações. Registre inconsistências remanescentes sem modificar a auditoria.
5. Verifique o estado do workspace e Git (branch e commit **quando acessíveis**) sem realizar alterações.
6. Faça um diagnóstico breve das ferramentas disponíveis: terminal, Python, bibliotecas para ler Excel/PDF, acesso aos repositórios das aplicações e acesso autorizado ao Datadog. O Trust Mode do editor está habilitado, mas **isso não garante** que todos os recursos ou permissões estejam disponíveis.

**Se o diretório principal não for identificado de forma inequívoca, pare e solicite correção do ambiente.** Não invente arquivos ou simule a execução.

## 3. Escopo técnico fechado

Investigue **somente** os seis indicadores abaixo, condicionados ao filtro funcional `Responsável = SEGUROS` da planilha original:

| ID | Indicador | Aplicação(ões) apontada(s) pelo time |
|---|---|---|
| 01 | Índice de Sucesso | Gestão Eventos Conta Cartão |
| 02 | Taxa de Falha | Gestão Eventos Conta Cartão |
| 03 | Taxa de Falha no Cancelamento | Gestão Eventos Conta Cartão; Gestão Eventos Pagamento |
| 04 | Taxa de Reprocessamento | Gestão Eventos Conta Cartão |
| 05 | Parcela Cobrada x Parcela Lançada | Gestão Eventos Pagamento |
| 06 | Origem | Venda Digital |

A relação acima foi definida com o time e serve de **hipótese de mapeamento técnico/decisão de escopo**, não de prova de código, de telemetria ou de regra de cálculo.

**Fora do escopo:**
- Indicadores não listados, mesmo se forem tecnicamente viáveis.
- Indicadores de outras áreas de responsabilidade.
- Indicadores de SEGUROS não selecionados: **bloqueados para a entrega atual**, não declarados "impossíveis".
- Implementar instrumentação, criar dashboards reais, modificar código, publicar métricas ou configurar monitores.

**Exceção de investigação:** você pode ler componentes de aplicações adjacentes **somente quando for necessário** demonstrar uma dependência concreta de um dos seis indicadores. Não amplie os objetivos do projeto por conta própria.

### Atenção — "Origem" não é "MOB"
O indicador **Origem** ainda não possui definição funcional confirmada. Hipóteses:
- canal/origem da contratação;
- via utilizada para processar o pagamento (possivelmente Pismo ou DXC);
- outro significado de negócio ainda não esclarecido.

`Processamento de Seguros — MOB` é um conceito distinto até prova em contrário. **Não associe o arquivo de MOB automaticamente ao indicador Origem, nem apresente Pismo/DXC como fato comprovado.** Investigue a aplicação Venda Digital e documente as possibilidades que o código efetivamente suporta. Marque o cálculo/objetivo como **DEPENDENTE DE DEFINIÇÃO DO NEGÓCIO** quando não houver confirmação.

## 4. Complemento obrigatório da Etapa 01 — leitura efetiva de planilhas e artefatos

A auditoria corrigida registrou dificuldade para ler `.xlsx`, `.pdf` e outros formatos. **Não repita essa limitação sem testar os recursos disponíveis.** Antes da revalidação dos indicadores:

1. Liste as planilhas e fontes de referência originais presentes no projeto.
2. Verifique Python e bibliotecas instaladas. Priorize `openpyxl` para Excel (incluindo leitura de valores e, se relevante, fórmulas), `json` nativo para JSON e ferramentas disponíveis para extração de texto de PDF, como `PyMuPDF` ou `pypdf`. Para imagens, use capacidade visual nativa quando disponível; OCR somente como último recurso e quando necessário.
3. Use **leitura local, sem editar os arquivos**. Não instale pacotes ou acesse serviços externos para instalar dependências sem autorização.
4. Identifique **arquivo, aba, cabeçalho, linha/célula** e valor da coluna `Responsável` para os seis indicadores. Confirme também a grafia do indicador `Origem` e as informações funcionais apresentadas, se presentes.
5. Não confunda o valor de uma fórmula com seu cálculo validado. Se houver células mescladas, fórmulas sem cache, linhas duplicadas ou planilhas diferentes, documente a situação.
6. Se não conseguir ler, reporte o **comando/ferramenta testada, erro específico e alternativa testada**, sem imprimir dados pessoais ou informações sensíveis.

**Importante:** esse complemento não autoriza reabrir ou reescrever a auditoria da Etapa 01. Registre a verificação e eventuais correções necessárias **apenas no relatório da Etapa 02**.

Se a planilha contrariar o escopo decidido pelo time, marque **DIVERGÊNCIA A VALIDAR COM PO/NEGÓCIO**. Não altere a lista dos seis nem abandone as demais análises sem necessidade.

## 5. Identificação das aplicações reais e delimitação da investigação

Para Gestão Eventos Conta Cartão, Gestão Eventos Pagamento e Venda Digital:

1. Encontre os **repositórios/diretórios reais** disponíveis no workspace. Não invente nomes ou caminhos a partir dos apelidos de negócio.
2. Registre repo, branch, commit (quando visível), stack, módulos e limites de acesso.
3. Localize fluxos associados a **recorrência, adesão, cancelamento, reprocessamento, cobrança, lançamento, contratação, pagamento e origem**, na medida em que forem relevantes aos seis indicadores.
4. Identifique pontos concretos: entrada (HTTP/evento/schedule), processamento, transformação de estado, persistência, publicação, retries, tratamento de falhas e chamadas downstream.
5. Use buscas de código direcionadas, reduzindo varreduras enormes e documentação redundante.
6. Se um repositório não estiver disponível, declare **NÃO ACESSÍVEL** e quais perguntas técnicas permanecem sem resposta. Não substitua a aplicação ausente pela documentação de outra.
7. Não execute testes que escrevam em serviços remotos, bancos, filas, APIs de produção ou sistemas empresariais.

Ao descrever fluxo, diferencie com clareza **quem recebe**, **quem decide**, **quem persiste**, **quem emite**, **quem confirma** e **quem representa o estado final** da operação.

## 6. Investigação por indicador — requisitos obrigatórios

Para **cada um** dos seis indicadores, registre:

**A. Definição e lacunas funcionais**
- Regra/formulação documentada: numerador, denominador, evento elegível, janela temporal, unidade, situação de sucesso/falha, exclusões.
- Responsável e status de confirmação no material original.
- Dúvidas funcionais pendentes para Negócio/PO.
- Distinção entre *tentativa técnica* e *resultado de negócio*. Não considere automaticamente HTTP 2xx como sucesso financeiro ou HTTP 4xx/5xx como falha definitiva de negócio.

**B. Fluxo implementado**
- Aplicação/repositório; classe, método, arquivo, linha/faixa e condição observada.
- Serviços externos/filas/bancos e correlações necessárias.
- Eventos, estados, timestamps, chaves técnicas de ligação e semântica de retries/reprocessamentos.
- Se houver identificadores de contrato/proposta/transação, cite **apenas os nomes técnicos dos campos**, sem expor valores individuais.

**C. Dados existentes e confiabilidade**
- Onde o valor pode ser encontrado: log estruturado, trace, métrica, evento, tabela, API ou outra fonte.
- Se a fonte representa estado atual, evento histórico, tentativa ou conclusão final.
- Se o período consultado é suficiente; risco de retenção, amostragem, perda de logs ou cardinalidade.
- Como evitar dupla contagem por retry, reprocessamento ou reenvio.
- Não presumir que dados em banco possam ser usados diretamente como métrica Datadog sem mecanismo de coleta.

**D. Datadog — estado observado, não imaginado**
- Se houver ferramenta/conector/API/CLI de acesso previamente autorizado, use-a **somente em leitura**.
- Reutilize mecanismos de autenticação já previstos no projeto e aprovados pelo ambiente, sem copiar, revelar, imprimir, exportar ou persistir tokens.
- Identifique `service` real, ambiente (prod/homologação se distinguível), tipo de sinal, período, filtro e agregação consultada.
- Consulte métricas/logs/traces efetivamente necessários, respeitando permissões e limitações do Datadog; evite varreduras massivas ou operações de custo excessivo.
- Registre **consulta/filtros sem segredos**, intervalo de tempo e resultado resumido (com ou sem dados), sem conteúdo sensível.
- **"No Data" não prova que o evento não ocorre**: pode significar consulta incorreta, permissão, indexação, retenção, amostragem ou janela inadequada. Classifique o motivo como pendente até evidência.
- Se o acesso falhar, informe o recurso tentado, categoria de falha e consequência; não invente nomes de `service`, valores de métricas ou queries "validadas".

**E. Conclusão técnica**
Para cada fonte/componente, diferencie:
- **COMPROVADO NO CÓDIGO** (com referências precisas);
- **OBSERVADO NO DATADOG** (com consulta e resultado);
- **CONFIRMADO NA PLANILHA** (arquivo + aba + referência);
- **DOCUMENTADO, MAS NÃO CONFIRMADO**;
- **HIPÓTESE / NÃO VERIFICADO**;
- **BLOQUEADO POR ACESSO OU DEFINIÇÃO**.

Responda explicitamente:
1. É possível calcular o indicador corretamente **hoje**, com as evidências disponíveis?
2. O Datadog sozinho dispõe da informação necessária?
3. Se não, qual dado está faltando e onde potencialmente pode ser obtido?
4. Qual instrumentação, coleta externa ou correlação seria necessária **se comprovada**?
5. Há necessidade de consulta em banco, BI ou API? Qual restrição deve ser validada?
6. O que é possível montar **agora** (MVP) e o que depende de instrumentação/integração (alvo)?
7. Existe condição de monitoramento/alerta ou faltam thresholds aprovados?
8. O diagnóstico permite estimar implementação ou faltam dependências?

**Propostas de solução** nesta etapa são apenas alternativas fundamentadas. Não escreva código de instrumentação nem crie widgets executáveis.

### Pontos específicos que exigem rigor
- **Índice de Sucesso vs. Taxa de Falha:** verificar se representam a mesma população, se existem estados pendentes/retentativas e se as taxas são ou não complementares.
- **Taxa de Falha no Cancelamento:** verificar em qual aplicação ocorre o pedido, quando há conclusão, se o processo é assíncrono e como distinguir atraso, falha transitória e falha final.
- **Taxa de Reprocessamento:** distinguir operação reprocessada, número de tentativas e êxito pós-retry, sem contagens duplicadas.
- **Parcela Cobrada x Parcela Lançada:** confirmar a fonte de cada lado da comparação, chaves de correlação, janelas de sincronização e diferença entre cobrança efetuada e lançamento refletido.
- **Origem:** registrar diferentes significados possíveis, evidência de Venda Digital, eventual representação Pismo/DXC e necessidade de validação funcional, sem mistura com MOB.

## 7. Revalidação da documentação existente

Confronte as conclusões acima com **documentos ativos** indicados na Etapa 01, sobretudo:
- `indicadores/` — apenas os seis relevantes e documentos transversais;
- `dashboards/` — MVP, alvo, lacunas e widgets que realmente se aplicam ao escopo;
- `planos/` — instrumentação sugerida; verificar se é necessária, suficiente e associada à aplicação correta;
- `01-arquitetura-e-fontes.md`, `gaps-e-proximos-passos.md`, `README.md` e relatório corrigido da Etapa 01, quando existirem.

**Não altere os documentos nesta etapa.** Produza uma tabela de achados:
- arquivo/seção;
- afirmação atual;
- evidência que confirma/refuta (ou ausência de comprovação);
- severidade;
- alteração sugerida para a Etapa 03.

Em particular, identifique propostas de queries, widgets e planos de instrumentação que estejam **obsoletos, errados, sem evidência ou baseados em indicadores removidos do escopo**. Não arquive: o arquivamento só ocorrerá na Etapa 04.

## 8. Decisão de arquitetura — avaliação, não implantação

Avalie, por indicador, as rotas possíveis:
- telemetria disponível no Datadog;
- instrumentação da aplicação para emitir evento/métrica correta;
- coletor periódico e seguro de dados transacionais, preferencialmente com acesso de leitura e limites de carga;
- API interna ou integração com banco/BI;
- solução híbrida, se realmente necessária.

Considere retenção, granularidade, latência, duplicidade, governança, permissões, custo de métricas customizadas/cardinalidade, proteção de dados e custo de manutenção.

**Não recomende criação de novo portal ou coletor como decisão fechada sem piloto/evidências.** Registre alternativas, pré-condições, benefícios e riscos para decisão posterior.

## 9. Artefato ÚNICO autorizado

Crie/atualize na raiz efetiva do Darwin Monitor:

`./REVALIDACAO_TECNICA_ETAPA_02.md`

Não inclua `docs/darwin-monitor/` no caminho **se o diretório corrente já for a raiz**.

Estrutura obrigatória:
1. **Metadados e alcance:** data, raiz, versão do acervo, repos/branches/commits consultados, acessos e limitações.
2. **Resumo executivo com conclusões verificáveis** e distinção entre resultados comprovados, prováveis e pendentes.
3. **Preflight de ferramentas e leitura dos artefatos originais:** status de Excel/PDF/JSON/PNG; coluna `Responsável`; evidências.
4. **Mapa real das aplicações e integrações relevantes:** ponto de entrada, operações, persistência, dependências e relações.
5. **Seis fichas de indicadores** (uma por indicador) com definição, evidências, status e gaps.
6. **Matriz consolidada:** indicador × fonte × Datadog atual × instrumentação/integração necessária × MVP × alvo × bloqueios × grau de comprovação.
7. **Inventário de consultas realmente executadas** no Datadog, com filtros/períodos/resultados resumidos; registrar indisponibilidade quando aplicável.
8. **Inconsistências da documentação vigente** e mudanças específicas sugeridas para a Etapa 03.
9. **Alternativas arquiteturais e dependências**, sem decisões forçadas.
10. **Pendências e perguntas objetivas** por responsável (Negócio/PO, aplicação, SRE, dados/DBA).
11. **Plano recomendado da Etapa 03**, apontando arquivos existentes a atualizar, seções e informações que devem mudar, sem executar.
12. **Integridade:** fontes não acessíveis, o que foi realmente testado, alterações realizadas e confirmação de que nada além do relatório foi escrito.

Para cada alegação relevante, inclua evidência rastreável em um dos formatos:
- `aplicação/repo@branch/commit : caminho/arquivo : classe#método : linhas`;
- `planilha : aba!célula/linha`;
- `Datadog : tipo de consulta : serviço/filtro : período : resultado observado`;
- `documento : caminho : seção`.

Se não possuir número de linha estável, identifique método, símbolo e caminho, declarando a limitação. Não invente referências.

## 10. Restrições operacionais e de segurança

- **LEITURA SOMENTE** nas aplicações, fontes primárias, planilhas, documentos preexistentes e Datadog.
- Não executar comandos destrutivos, migrações, scripts de bootstrap que modifiquem ambiente, testes integrados em produção, operações de escrita ou chamadas a APIs externas mutáveis.
- Não alterar código, `.env`, configurações, secrets, instrumentação, Dashboards, monitors, coletores ou bancos.
- Não criar inúmeros arquivos auxiliares, snapshots, novos READMEs, cópias ou documentos com sufixos `rev2`/`final`. Se scripts temporários forem indispensáveis, prefira execução inline sem persistência.
- Não instalar dependências sem autorização. Use ferramentas já disponíveis e documente as limitações reais.
- Não fazer commit/push.
- Não usar Python, WebFetch ou ferramentas externas como justificativa para expor dados internos à internet; ferramentas devem operar no ambiente e destino autorizados.
- Não imprimir valores de CPF, proposta, contrato, tokens, credenciais nem dados financeiros identificáveis. Use somente nomes técnicos de campos e exemplos fictícios para explicações.
- Se uma integração exigir privilégios adicionais, peça autorização e registre o bloqueio; não contorne restrições.
- Não avançar para a Etapa 03 nem 04 até aprovação humana.

## 11. Critérios de aceite

Conclua apenas se:
- [ ] A raiz real foi identificada e nenhum diretório redundante foi criado.
- [ ] O relatório corrigido da Etapa 01 foi usado como entrada.
- [ ] Os seis indicadores aparecem com **análise individual** e status de evidência.
- [ ] As planilhas tiveram tentativa real de leitura com ferramentas locais (ou impedimento específico documentado).
- [ ] `Responsável = SEGUROS` não foi declarado comprovado sem conferir a fonte original.
- [ ] `Origem` não foi equiparado a `MOB`, Pismo ou DXC sem prova.
- [ ] Cada afirmação de comportamento da aplicação tem vínculo verificável com arquivo/classe/método.
- [ ] Consultas ao Datadog foram realizadas quando permitidas, ou as razões da ausência estão descritas.
- [ ] Dados disponíveis hoje, dados necessários e hipóteses estão diferenciados.
- [ ] Há um plano objetivo de revisão documental para a Etapa 03.
- [ ] Nenhum documento preexistente, aplicação, dashboard, banco ou monitor foi modificado.
- [ ] Somente `./REVALIDACAO_TECNICA_ETAPA_02.md` foi criado/atualizado.

## 12. Resposta final ao usuário

Ao concluir, informe concisamente:
1. Caminho exato do relatório.
2. Repositórios/aplicações **efetivamente** analisados.
3. Status de leitura das planilhas e confirmação (ou não) da coluna `Responsável`.
4. Situação dos seis indicadores: **comprovável hoje / parcial / pendente / bloqueado**, com indicação resumida do motivo.
5. O que foi e não foi possível consultar no Datadog.
6. Alterações sugeridas à documentação ativa para a Etapa 03.
7. Lista explícita de quaisquer acessos ou definições que dependam de pessoas.
8. Confirmação de **nenhuma alteração além do relatório**.

Encerre com: **"Etapa 02 concluída para revisão humana; Etapa 03 não iniciada."** somente se os critérios de aceite estiverem atendidos. Se houver bloqueios, declare conclusão parcial e especifique-os sem inventar evidências.
