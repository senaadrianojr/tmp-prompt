# PROMPT — DARWIN MONITOR | ETAPA 02 — REVISÃO FINAL E GATE PARA ETAPA 03

## Missão

Atue como engenheiro sênior de observabilidade, integração de sistemas e auditoria técnica.

**Esta execução não é uma nova investigação do zero.** A Etapa 02 já produziu o relatório `REVALIDACAO_TECNICA_ETAPA_02.md`, houve leitura efetiva de XLSX com Python e o acesso autenticado ao Datadog foi restabelecido. Também foi comprovado, em teste controlado, que o agente consegue consultar widgets e realizar criação e exclusão de um widget de teste.

Seu trabalho agora é **revisar e concluir a Etapa 02**, aproveitando todo o material confiável, validando as pendências realmente relevantes no Datadog e produzindo um diagnóstico completo que permita iniciar a **Etapa 03 — Consolidação Documental**.

**Não execute a Etapa 03 e não implemente os indicadores nesta tarefa.**

## 1. Contexto e limites confirmados

- Projeto/diretório de execução: `darwin-monitor/`. O agente pode já estar posicionado diretamente nessa raiz.
- O relatório da auditoria documental corrigida da Etapa 01 é `AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md`.
- O relatório vigente da Etapa 02 é `REVALIDACAO_TECNICA_ETAPA_02.md`.
- A leitura de planilhas XLSX já foi realizada usando Python e bibliotecas nativas ZIP/XML; **não declare essa capacidade inexistente**.
- O usuário confirmou que foi restabelecido acesso ao Datadog por método corporativo já testado, e que foi possível ler widgets, criar e excluir um widget de teste. **Isso comprova capacidades da sessão anterior, não valida automaticamente os seis indicadores.**
- Não presuma que o mecanismo ainda esteja autenticado; execute uma verificação segura de leitura, com método autorizado, antes de novas consultas.
- Não repita grandes varreduras de código ou do acervo sem uma pendência concreta e identificada.

### Caminhos obrigatórios

Primeiro, confirme o diretório real via `pwd` e estrutura existente.

**Na raiz do Darwin Monitor**, atualize:
- `./REVALIDACAO_TECNICA_ETAPA_02.md` — diagnóstico técnico e gate.
- `./README_FERRAMENTAS_AMBIENTE.md` — somente para documentar/revisar procedimentos efetivamente testados, conforme seção 4.

Não recrie `docs/darwin-monitor/` dentro de `darwin-monitor/`. Não crie relatórios derivados, arquivos com sufixo `rev2`, backups ou históricos de sessão.

## 2. Escopo estritamente limitado a seis indicadores

| Indicador | Aplicação(ões) apontada(s) pela equipe |
|---|---|
| Índice de Sucesso | Gestão Eventos Conta Cartão |
| Taxa de Falha | Gestão Eventos Conta Cartão |
| Taxa de Falha no Cancelamento | Gestão Eventos Conta Cartão e Gestão Eventos Pagamento |
| Taxa de Reprocessamento | Gestão Eventos Conta Cartão |
| Parcela Cobrada × Parcela Lançada | Gestão Eventos Pagamento |
| Origem | Venda Digital |

Este recorte é uma decisão de trabalho tomada com o time. Outros indicadores permanecem **fora da entrega atual ou bloqueados para esta entrega**, sem serem declarados impossíveis para sempre. Não os recoloque no escopo.

### Divergência de responsáveis na planilha

Em uma execução anterior, foram encontrados registros `Seguros` e `bvseg`. Não equipare nem separe automaticamente essas responsabilidades. Verifique no relatório e na fonte original:
- O valor real da coluna `Responsável` de cada indicador (arquivo, aba, linha/célula);
- se existe confirmação funcional de que `bvseg` pertence ao escopo aprovado pelo Negócio;
- quais dos seis têm divergência que exige alinhamento com PO/Negócio.

**Não exclua silenciosamente Parcela Cobrada × Parcela Lançada ou Origem apenas pelo rótulo `bvseg`.** Registre a divergência como decisão humana, mantendo o recorte técnico informado pelo time.

### Indicador Origem

A definição funcional ainda é ambígua: canal/origem da contratação versus via de processamento do pagamento (possivelmente Pismo/DXC). A aplicação Venda Digital foi apontada como fonte candidata.

Não considere `Processamento MOB` equivalente a `Origem`. Não apresente Pismo/DXC como definição aprovada por Negócio até haver confirmação. Examine apenas as evidências técnicas faltantes; preserve a pendência de regra funcional quando necessário.

## 3. Etapa inicial — recuperar o que já foi comprovado

Antes de novas consultas:

1. Leia os relatórios corrigidos das Etapas 01 e 02, o README raiz, o guia de ferramentas caso exista e os documentos vigentes relevantes em `indicadores/`, `dashboards/` e `planos/`.
2. Separe as conclusões anteriores em **COMPROVADO NO CÓDIGO**, **OBSERVADO NO DATADOG**, **CONFIRMADO NA PLANILHA**, **INFORMADO PELO TIME**, **HIPÓTESE** e **PENDENTE**.
3. Faça uma lista pequena dos testes faltantes em cada um dos seis indicadores. Reaproveite consultas e evidências já validadas, sem fingir que resultados históricos representam necessariamente o estado atual.
4. Identifique quais repositórios, branches, commits, arquivos, classes e métodos sustentam afirmações relevantes. Se a referência anterior não for rastreável, marque para checagem dirigida.
5. **Não reexecute todo o discovery** quando já houver evidência suficiente para responder a uma pergunta.

## 4. Acesso real ao Datadog e registro da metodologia

### 4.1 Método já recuperado

O usuário recuperou uma sessão anterior em que houve:
- leitura de `cookies.txt` pelo procedimento de autenticação corporativa;
- execução de uma requisição HTTP GET autenticada ao endpoint interno `/api/v1/legacy/current_user`, com cabeçalho de cookies produzido pelo mecanismo autorizado;
- retorno positivo no teste de sessão;
- consultas posteriores a dashboards/widgets;
- criação e exclusão bem-sucedidas de um widget **de teste** em execução já autorizada.

Use essa informação apenas para **identificar e reproduzir o mecanismo previamente permitido**. Localize o runbook real `GEMINI_PLATFORM_ACCESS_RUNBOOK.md` ou o procedimento atualizado documentado no projeto, consulte suas instruções e respeite as permissões vigentes.

**Não use a API pública do Datadog como substituto obrigatório:** ela pode exigir API Key e Application Key, indisponíveis neste cenário. **O endpoint `legacy/current_user` é somente uma prova de sessão**, não uma fonte automática dos dados dos indicadores.

- Verifique sessão com uma consulta pequena e de leitura.
- Não imprima, transcreva, salve em Markdown, logs ou respostas nenhum conteúdo de `cookies.txt`, tokens, cabeçalhos `Cookie`, chaves ou valores de sessão.
- Não envie cookies a serviços externos que não sejam os destinos autorizados pelo runbook.
- `WebFetch` pode não compartilhar cookies ou headers com o cliente HTTP local. Teste a compatibilidade real com o fluxo autorizado; **não force** WebFetch quando seu contrato não permitir a sessão.
- Se necessário, use a ferramenta HTTP local já autorizada e comprovada anteriormente. Não invente autenticação nem contorne controles de SSO, proxy ou segurança.
- Faça somente consultas de leitura nesta execução. **Não crie, altere ou apague novos widgets**, mesmo que essa capacidade já tenha sido comprovada.
- Se um endpoint interno mudar, não tente descobrir rotas protegidas por tentativa e erro em massa; consulte procedimentos autorizados e registre o bloqueio específico.

### 4.2 Consulta dos dados — não basta listar widgets

Após confirmar acesso, valide o que for relevante para cada indicador:
- Dashboard e widget existentes, se houver; identificação do dashboard/widget e definição da consulta usada;
- tipo de dado consultado (logs, APM traces/spans, métricas, eventos) e filtros reais;
- período e ambiente consultados, índices/serviços, operações/resources e tags observadas;
- resposta efetivamente obtida (valor agregado, presença/ausência de dados, erro ou resultado vazio) sem divulgar dados individuais;
- correspondência da query com numerador, denominador, estados, retentativas, janelas e regras do indicador;
- limitações de retenção, amostragem, acesso, granularidade, ausência de tags e outras restrições;
- diferença entre **widget configurado**, **widget que retorna dados** e **indicador de negócio calculado corretamente**.

`No Data` não prova inexistência de evento. Um widget renderizado não comprova, por si só, a correção da métrica. Consultas de logs sem dados financeiros não permitem afirmar resultados de cobrança.

Se uma consulta exigir outra fonte (banco, BI ou API de aplicação), **documente a dependência**; não suponha que o Datadog consiga obtê-la sozinho.

### 4.3 Guia persistente na raiz

Se o mecanismo tiver sido **efetivamente verificado**, crie ou atualize `./README_FERRAMENTAS_AMBIENTE.md` na raiz real:

- ferramentas, versão/ambiente e pré-requisitos efetivamente observados;
- passos sanitizados, **reprodutíveis e comprovados** para ler XLSX usando Python, inclusive alternativa ZIP/XML;
- onde encontrar o runbook de autenticação existente;
- como validar sessão e executar consultas Datadog de leitura pelos mecanismos autorizados;
- como consultar dashboards, monitores e widgets; distinguir o que foi testado de fato do que ainda não foi;
- mencionar que a criação/exclusão do widget de teste foi comprovada em execução anterior e **exige aprovação específica para novas escritas**;
- testes mínimos no começo de cada nova sessão, erros comuns e limitações verificadas;
- data/contexto dos testes, distinção entre **funcionou antes**, **confirmado agora** e **não testado**;
- links internos para o relatório Etapa 02 e runbook existente.

**Nunca** registre cookies, conteúdo de sessão, tokens, credenciais, dados pessoais, chaves ou técnicas de contorno de segurança. Não duplique arquivos secretos nem altere o runbook original.

Se o guia já existir, atualize-o preservando material válido e corrigindo informações desatualizadas. Se o acesso falhar nesta sessão, preserve o fato de que já funcionou antes, mas registre o bloqueio **atual** com evidências e sem alegar confirmação presente.

## 5. Fechamento da análise dos seis indicadores

Produza uma ficha concisa e comprovável para **cada** indicador:

1. **Objetivo, fórmula e responsável:** o que é confirmado pelo Negócio versus o que ainda é hipótese.
2. **Fonte técnica:** aplicação, entrada/evento, estados, persistência, downstream, identificadores de correlação e rastreabilidade em código.
3. **Datadog atual:** serviço, sinal, query/widget, período, resultado, limitações e disponibilidade comprovada.
4. **Qualidade da medição:** numerador/denominador, unidade, janelas de apuração, possíveis duplicidades, retries, atrasos e status intermediários.
5. **MVP agora:** o que pode realmente ser apresentado com dados disponíveis, mesmo que parcial, com ressalvas claras.
6. **Alvo:** o que só será possível após instrumentação, coleta externa ou definição funcional.
7. **Mudança necessária:** aplicação afetada, informação/evento a emitir ou consultar, vínculo com o indicador, evidência do gap e pré-condições.
8. **Monitor/alerta:** viabilidade e necessidades de thresholds definidos pelo Negócio.
9. **Conclusão e próximo responsável:** situação técnica e pendências de PO/Negócio, Engenharia, SRE ou Dados/DBA.

### Cuidados específicos por indicador

- **Índice de Sucesso:** confirmar o que constitui sucesso final, universo elegível e denominador. Não confundir status técnico ou HTTP 2xx com sucesso de negócio.
- **Taxa de Falha:** separar falha definitiva, transitória, pendente e retentativa; verificar se compartilha a mesma população do Índice de Sucesso.
- **Falha no Cancelamento:** distinguir solicitação recebida, tentativa, conclusão e erro final entre Conta Cartão e Pagamento; verificar se há evento de cancelamento suficiente para o denominador.
- **Taxa de Reprocessamento:** separar número de reprocessamentos, operações únicas reprocessadas e resultado pós-retry; evitar contagem duplicada.
- **Parcela Cobrada × Parcela Lançada:** localizar as duas fontes (ou reconhecer ausência), chave de correlação, prazo de conciliação e necessidade de banco/BI/outros sistemas.
- **Origem:** procurar evidências reais em Venda Digital para origem do contrato ou rota Pismo/DXC, mantendo a definição pendente até validação funcional.

Para cada indicador atribua **duas classificações separadas**:
- **Evidência técnica:** `COMPROVADA`, `PARCIAL`, `NÃO VERIFICADA` ou `IMPEDIDA`, com fonte.
- **Disponibilidade operacional:** `CALCULÁVEL HOJE`, `MVP PARCIAL`, `DEPENDE DE INSTRUMENTAÇÃO`, `DEPENDE DE FONTE EXTERNA` ou `DEPENDE DE DEFINIÇÃO FUNCIONAL`.

Não classifique como `CALCULÁVEL HOJE` sem dados reais, regra suficiente, numerador, denominador, janela e tratamento adequado das contagens. Uma conclusão pode ter múltiplas dependências; registre a principal e as secundárias.

## 6. Inventário das instrumentações e definições de dashboards

Sem implementar nada, consolide **somente o que está fundamentado**:

**Instrumentações por aplicação:** para cada alteração necessária, registre nome do indicador, aplicação/repo, ponto no código, evento/campo/métrica ausente, justificativa técnica e grau de certeza. **Não propor instrumentação se um sinal existente já permite cálculo confiável**.

**Dashboard atual (MVP):** widgets que podem funcionar com a telemetria atual e suas limitações; referencie queries testadas e evidência de resultado.

**Dashboard-alvo:** widgets, métricas e monitores que passarão a ser possíveis após as adequações necessárias, sempre identificando dependências de aprovação, banco/BI e regras funcionais.

**Gestão de documentação:** para cada documento ativo relacionado aos seis, informe se deve permanecer, ser corrigido, consolidado ou arquivado futuramente. Atenção a arquivos de `indicadores/`, `dashboards/`, `planos/`, README, arquitetura e gaps. **Não arquivar nem editar esses arquivos agora.**

## 7. Gate de aprovação da Etapa 02

A passagem para a Etapa 03 significa **suficiência de evidências para consolidar a documentação**, não que os seis indicadores estejam implementados ou sem impedimentos.

Ao final, responda objetivamente:

| Verificação de saída | Critério |
|---|---|
| Escopo | Os seis indicadores constam da matriz, sem expansão e com divergências `Seguros`/`bvseg` registradas |
| Ferramentas | Acesso/limitações de Python, XLSX e Datadog foram comprovados e registrados, sem segredos |
| Código | Alegações de comportamento têm referências rastreáveis em aplicação/arquivo/classe/método |
| Datadog | O que foi consultado é comprovado; as ausências e limitações têm causa ou hipótese explicitada |
| Indicadores | Cada um tem situação atual, MVP, alvo, dependências e pendências identificados |
| Instrumentação | Necessidades propostas possuem justificativa e aplicação responsável, sem mudanças especulativas |
| Documentação | Existe plano por arquivo para a Etapa 03, com consolidação do vigente e tratamento do histórico na Etapa 04 |
| Segurança | Nenhum código, banco, dashboard, monitor ou configuração real foi alterado nesta execução |

**Decisão final obrigatória:**
- `APTO PARA ETAPA 03` quando o relatório estiver consistente e as incertezas estiverem explicitamente documentadas, ainda que existam indicadores bloqueados ou instrumentações futuras;
- `APTO COM PENDÊNCIAS DOCUMENTADAS` quando houver lacunas que possam ser tratadas como hipóteses explícitas na consolidação, sem transformar hipóteses em verdades;
- `NÃO APTO` quando faltar informação essencial sobre escopo, fluxo ou fontes para sequer redigir documentação atual e alvo confiável.

Para cada pendência, informe **o que falta, quem decide, qual documento/indicador é afetado e se impede ou não a Etapa 03**.

Não force classificação "apto" somente para encerrar a tarefa.

## 8. Escrita estritamente controlada

**Arquivos autorizados nesta execução:**
1. `./REVALIDACAO_TECNICA_ETAPA_02.md` — atualizar no lugar, sem perder evidências válidas;
2. `./README_FERRAMENTAS_AMBIENTE.md` — criar/atualizar guia persistente somente com procedimentos comprovados.

Não modificar `AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md`, documentos de `indicadores/`, `dashboards/`, `planos/`, README geral, código, Datadog, configurações ou artefatos de origem. Não executar Git commit/push sem autorização. Não gerar outros arquivos auxiliares, especialmente versões paralelas.

O relatório `REVALIDACAO_TECNICA_ETAPA_02.md` deve conter:
1. resumo executivo e estado do gate;
2. fontes de evidências usadas, incluindo código/branch/commit, planilhas e consultas reais Datadog;
3. matriz atual dos seis indicadores;
4. detalhes e gaps por indicador;
5. inventário de queries/widgets testados com período/resultado resumidos;
6. instrumentações e fontes externas necessárias, fundamentadas;
7. quadro de MVP versus dashboard-alvo;
8. divergências funcionais (inclusive `Origem` e `bvseg`) e decisões humanas;
9. **plano de alterações da Etapa 03 por documento e seção**;
10. limitações técnicas e conferência das modificações efetuadas.

## 9. Entrega final ao usuário

Ao terminar, apresente em português:

1. Confirmou acesso ao Datadog **nesta sessão**? Qual foi a evidência real, sem expor credenciais?
2. Quantos dos seis indicadores estão calculáveis hoje, parciais ou dependentes de adequações? Não force números se não houver validação.
3. Quais instrumentações/fontes externas foram **fundamentadamente** identificadas?
4. O que está pendente de PO/Negócio, sobretudo `Origem` e `Seguros` vs `bvseg`?
5. Quais arquivos foram atualizados e onde estão?
6. Qual foi a decisão do **Gate Etapa 02 → Etapa 03**, com justificativa?

**Aguarde aprovação do usuário. Não execute a Etapa 03 nesta sessão.**
