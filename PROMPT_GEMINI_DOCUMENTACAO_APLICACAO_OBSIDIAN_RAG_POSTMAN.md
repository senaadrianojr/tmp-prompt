# PROMPT — DOCUMENTAÇÃO DE APLICAÇÃO (MARKDOWN, OBSIDIAN, RAG E POSTMAN)

## 1. Parâmetros de entrada

Substitua **somente** os dois valores abaixo antes de executar este prompt:

- **APLICACAO_ALVO:** <diretório+nome-aplicacao>
- **DIRETORIO_DOCUMENTACAO:** <diretório-da-documencao>

Considere APLICACAO_ALVO como o caminho completo da aplicação a analisar e DIRETORIO_DOCUMENTACAO como a **raiz exclusiva** na qual os artefatos de documentação dessa aplicação devem ser criados ou atualizados.

Se DIRETORIO_DOCUMENTACAO já possuir arquivos, preserve a estrutura e o conteúdo válidos e atualize de maneira incremental. Não suponha que a pasta de documentação esteja dentro da aplicação. Não redirecione os artefatos para outro lugar por conveniência.

## 2. Papel e objetivo

Atue como **analista de sistemas, arquiteto de software e documentador técnico**, com atenção especial à compreensão do negócio a partir de evidências concretas do repositório.

**Execute a análise e gere/atualize os arquivos de documentação. Não entregue apenas um plano.**

Produza documentação:
1. De negócio, organizada por **assuntos**, **domínios**, capacidades, regras, processos e integrações;
2. Técnica, contemplando arquitetura real, componentes, contratos, dependências, persistência, segurança e fluxos;
3. Em arquivos **.md (Markdown)**, legíveis para desenvolvedores e compatíveis com **Obsidian**;
4. Estruturada para recuperação e contextualização por **IA/RAG** (Retrieval-Augmented Generation);
5. Acompanhada de uma coleção **Postman v2.1**, em JSON, com os endpoints HTTP realmente expostos pela aplicação e modelos de request body quando existirem;
6. Com um registro explícito de **validações pendentes** para tudo o que não alcançar pelo menos **85% de confiança justificada**, sobretudo regras e interpretações de negócio.

Documente a implementação **como ela é**, não como deveria ser. Não presuma arquitetura, domínio, linguagem, framework, tipo de aplicação ou padrões arquiteturais antecipadamente.

## 3. Regras obrigatórias e limites

- **Somente leitura na APLICACAO_ALVO**: não edite arquivos da aplicação, não refatore, não implemente endpoints, não altere configurações nem instale dependências.
- **Somente escrita em DIRETORIO_DOCUMENTACAO**: crie/atualize os artefatos pedidos sem alterar outros diretórios. Não altere nem recrie uma configuração .obsidian já existente.
- Não execute requisições HTTP de negócio, chamadas a sistemas reais, migrações, testes de integração com efeitos colaterais ou comandos que alterem dados externos. Validações estáticas/offline são permitidas.
- Não leia, revele ou reproduza o conteúdo de segredos, arquivos de credenciais, tokens, senhas, chaves privadas ou dados pessoais reais. Caso apareçam em arquivos de configuração, descreva somente **nomes de parâmetros e sua finalidade**, omitindo valores. Use exemplos sintéticos.
- Nunca trate comentários antigos, nomes de métodos ou documentação legada como prova suficiente quando o comportamento do código divergir. Priorize implementação, contratos e testes atuais; apresente conflitos como pendência.
- **Não invente** endpoints, request bodies, respostas, regras de negócio, nomes de sistemas, campos, status codes, permissões, tabelas ou fluxos.
- Não afirme que algo foi testado ou validado em execução se somente houve inspeção estática.
- Não associe um endpoint a um domínio só por semelhança de nome; examine a responsabilidade e o fluxo efetivos.
- Não faça inferência de comportamento de componentes externos além do contrato observável local.
- Caso haja falha de acesso, arquivos ausentes ou partes não analisadas, sinalize a limitação em vez de afirmar cobertura completa.
- Preserve correções e anotações humanas preexistentes. Em caso de conflito, não sobrescreva silenciosamente: exponha a divergência em VALIDACOES_PENDENTES.md.

## 4. Procedimento de descoberta — antes da escrita

### 4.1 Inventário da aplicação

Investigue, na medida em que existirem e estiverem acessíveis:
- Estrutura de diretórios, módulos, subprojetos, camadas e pontos de entrada;
- Manifestos e arquivos de build, dependências e versões de linguagem/framework;
- Configurações e perfis por ambiente, sem capturar valores sensíveis;
- Controllers/routers/handlers, filtros/interceptors/middlewares, contratos de entrada e saída;
- Serviços, casos de uso, componentes, jobs, schedulers, eventos, consumers e publishers;
- Entities, modelos, repositories/DAOs, migrations, queries e recursos de persistência;
- Clientes HTTP/SOAP/gRPC, mensageria, chamadas externas e tratamento de falhas;
- Testes unitários/de contrato e mocks que comprovem comportamento;
- Swagger/OpenAPI, coleções Postman e outras documentações existentes, sempre confrontados com o código.

Identifique explicitamente o que **é interno**, o que é **exposto externamente**, o que é integração de **entrada** e o que é integração de **saída**. Diferencie comportamento de código de configuração condicional por perfil, feature flag ou módulo.

### 4.2 Mapeamento de negócio

A partir das evidências, extraia:
- **Domínios**: responsabilidades, entidades/conceitos relevantes, fronteiras observáveis e atores;
- **Assuntos**: atividades/capacidades de negócio que atravessam um ou mais domínios;
- **Regras de negócio**: condições, pré-condições, decisões, cálculos, validações, restrições e consequências;
- **Processos e jornadas**: gatilhos, fluxo principal, alternativas, erros, transições de estado e efeitos colaterais;
- **Integrações**: sistemas envolvidos, direção, protocolo, dados trafegados, autenticação, tratamento de falhas, retries e idempotência **somente se comprovados**;
- **Relacionamentos**: assunto → domínio → regra → componente → endpoint/evento → integração/dados.

Se um módulo misturar responsabilidades, descreva a situação real sem rebatizar ou reorganizar o código. Só classifique a arquitetura (por exemplo, monólito tradicional, monólito modular, microsserviços) quando existirem evidências suficientes. O objetivo é documentar, não propor uma reestruturação.

## 5. Critério de evidência e confiança mínima de 85%

Atribua confiança **por afirmação importante, principalmente de negócio**, nunca apenas uma nota global para toda a aplicação.

Use a seguinte orientação para graduar a confiança; essas faixas são **heurísticas de rastreabilidade, não probabilidades estatísticas de verdade**:

| Faixa | Caracterização | Tratamento |
|---|---|---|
| 95–100% | Regra/contrato explicitamente implementado, com fluxo examinado e corroboração independente (testes, contrato consistente, múltiplos pontos do código) | Documentar como **confirmado por evidência estática**, nunca como validado por negócio |
| 85–94% | Evidências diretas e coerentes no código, com contexto suficiente e sem contradição relevante | Documentar, indicando a fonte e eventual ressalva |
| 60–84% | Indícios parciais, ambiguidades, inferências de intenção, falta de trecho do fluxo ou contradição possível | Registrar em **VALIDACOES_PENDENTES.md**; se necessário, mencionar apenas como hipótese claramente rotulada e ligada à pendência |
| Abaixo de 60% | Apenas nome, suposição, comentário isolado ou especulação | **Não afirmar como regra**; registrar a dúvida apenas quando for relevante |

Regras adicionais:
1. **85% não é permissão para adivinhar**: uma regra de negócio sem suporte concreto continua pendente.
2. Separe **comportamento técnico comprovado** de **significado/intenção de negócio**. O código pode demonstrar uma validação sem esclarecer por que a empresa a exige.
3. Evidências independentes têm mais peso do que repetições do mesmo código ou comentários derivados dele.
4. Se fontes relevantes entrarem em contradição, não marque como confirmado, ainda que exista muito código.
5. Uma regra abaixo de 85% deve gerar uma entrada de pendência com pergunta objetiva e evidências existentes.
6. Não confunda a confiança heurística do agente com **homologação humana**. Use status separados, como: confirmado_por_codigo, inferido_com_evidencia, pendente_validacao_negocio, validado_por_responsavel (somente com prova de validação humana).
7. Descreva as decisões e os critérios que justificaram a confiança quando ela afetar documentação de negócio.

Para cada regra importante, registre: **ID estável**, descrição, contexto/domínio, condição e resultado, exceções conhecidas, evidências (caminhos de arquivos, símbolos e linhas quando viáveis), confiança, status e vínculos com endpoints/processos.

## 6. Estrutura de saída

Produza uma estrutura lógica como a seguinte, **somente para temas efetivamente encontrados**. Não crie arquivos vazios, nem diretórios artificiais:

~~~text
<DIRETORIO_DOCUMENTACAO>/
├── README.md
├── INDICE_RAG.md
├── GLOSSARIO.md
├── VALIDACOES_PENDENTES.md
├── postman_collection.json
├── arquitetura/
│   ├── VISAO_GERAL.md
│   └── COMPONENTES_E_FLUXOS.md
├── assuntos/
│   └── <assunto-identificado>.md
├── dominios/
│   └── <dominio-identificado>.md
├── integracoes/
│   └── <integracao-identificada>.md
├── api/
│   └── ENDPOINTS.md
├── dados/
│   └── MODELO_DE_DADOS.md
└── operacao/
    └── CONFIGURACAO_E_EXECUCAO.md
~~~

Se a estrutura existente já usar **assuntos/**, **dominios/**, **integracoes/**, **modernization/** e arquivos de índice, respeite essa organização. Não sobrescreva ou remova conteúdos alheios ao escopo, como análises de modernização. Prefira enriquecer arquivos existentes coerentes a criar duplicatas.

### 6.1 README.md

Inclua:
- Identificação da aplicação obtida da própria fonte analisada, propósito e limites do conhecimento;
- Visão geral da arquitetura **observada** e responsabilidades;
- Navegação por assuntos, domínios, integrações, APIs, dados e operação;
- Como ler o material em editor Markdown comum, Obsidian e pipelines RAG;
- Data da análise, branch/commit SHA **quando disponíveis**, escopo efetivamente inspecionado e lacunas;
- Status da documentação (gerada por análise estática, aguardando possíveis validações humanas).

### 6.2 Assuntos e domínios

**Assuntos** devem explicar processos e necessidades de negócio de ponta a ponta: objetivo, atores, entradas/saídas, regras, decisões, fluxo nominal e exceções, sistemas relacionados, pontos de entrada e links para os domínios.

**Domínios** devem descrever capacidades e conceitos do domínio: glossário local, responsabilidades observadas, entidades, regras com IDs, estados, transições, serviços participantes, dependências e limites identificáveis.

Inclua diagramas **Mermaid** de sequência, fluxo ou relacionamento apenas quando forem sustentados por rastreamento efetivo do código. Identifique o que não foi possível rastrear. Diagrama não substitui descrição textual.

### 6.3 Integrações, API, dados e operação

**Integrações:** origem/destino, operação, protocolo, contratos existentes, parâmetros relevantes, tratamento de erro, timeouts/retries quando observáveis, configuração por ambiente (sem valores sensíveis), componentes consumidores/provedores e referências ao código.

**API:** inventário rastreável dos endpoints HTTP, método, rota completa (incluindo prefixos globais conhecidos), finalidade observável, controller/handler, request params/path params/headers, body, respostas e autenticação **quando comprovados**. Diferencie status codes explicitamente produzidos de respostas possíveis por mecanismo global. Aponte lacunas.

**Dados:** estruturas persistidas e relacionamentos verificáveis; não represente como esquema de banco confirmado aquilo que é apenas entidade ou modelo de código. Mencione migrations/queries como evidência.

**Operação:** requisitos para build/start e configuração observável, perfis e dependências de ambiente; comandos somente quando sustentados pelos arquivos disponíveis e sem necessidade de segredos.

### 6.4 GLOSSARIO.md

Monte glossário padronizado de siglas, entidades, termos de negócio e expressões técnicas específicas da aplicação, com:
- Definição sustentada ou marcação explícita de pendência;
- Sinônimos/termos correlatos quando comprovados;
- Referências cruzadas aos assuntos e domínios;
- IDs de pendência para definições ambíguas.

Não invente expansão de siglas desconhecidas.

## 7. Compatibilidade com Obsidian e preparação para RAG

Todos os arquivos de conteúdo devem ser Markdown UTF-8 e ter organização consistente. Use recursos portáveis como títulos hierárquicos, tabelas simples, listas, blocos Mermaid e links relativos Markdown. Adicione **wikilinks Obsidian** como apoio quando útil, mantendo também navegação compatível com leitores Markdown comuns.

Na abertura de cada arquivo documental (exceto JSON), inclua **YAML frontmatter válido** com metadados pertinentes. Campos sugeridos:

~~~yaml
---
id: dominio-exemplo
titulo: "Título legível"
tipo: dominio
tags:
  - documentacao
  - negocio
status: confirmado_por_codigo
confianca: 91
fontes_codigo:
  - "src/.../Classe.java#metodo"
atualizado_em: "AAAA-MM-DD"
---
~~~

O bloco é apenas um **modelo**: substitua dados por fatos reais e não use o exemplo como fonte de informação. Para documentos agregadores, é aceitável omitir confianca quando não houver uma afirmação única à qual a nota se aplique. Se houver múltiplas regras com níveis de confiança diferentes, documente cada uma individualmente no corpo.

**RAG — regras de redação e indexação:**
- Use **IDs estáveis**, títulos descritivos e uma ideia central por seção.
- Faça seções suficientemente **autocontidas** para recuperação isolada; repita de modo sucinto o contexto que seria perdido ao dividir em chunks.
- Para cada seção relevante, explicite domínio/assunto, objetivo, termos de busca, fonte e links para conceitos correlatos.
- Evite blocos enormes sem subtítulos, parágrafos dependentes de “conforme acima” e siglas não definidas.
- Adicione metadados úteis para filtro: tipo, tags, domínio/assunto, status, confiança (quando aplicável), referência de código e data/commit da análise.
- Em **INDICE_RAG.md**, crie catálogo pesquisável de documentos com ID, título, assunto/domínio, resumo conciso, tags, caminho relativo, status e principais referências. Inclua um mapa de navegação entre regras, endpoints e integrações.
- Evite criar um arquivo JSON de embeddings, índices vetoriais ou configurações dependentes de ferramentas não disponibilizadas. Prepare os textos para ingestão RAG; **não afirme que uma base vetorial foi criada**.
- Nunca copie segredos, payloads reais com dados pessoais ou trechos extensos de código-fonte para melhorar “buscabilidade”.

**Rastreabilidade obrigatória:** para decisões, fluxos, integrações e regras centrais, use referências auditáveis como caminho relativo da origem + nome de classe/função + intervalo de linhas quando puder obter esse intervalo com confiabilidade. Não invente linhas nem gere links para arquivos inexistentes.

## 8. Coleção Postman obrigatória

Gere **DIRETORIO_DOCUMENTACAO/postman_collection.json** como JSON válido no formato **Postman Collection v2.1** (schema oficial v2.1). Caso não existam endpoints HTTP, gere coleção válida com itens vazios e registre essa constatação no README, sem fabricar rotas.

Descubra os endpoints **pela implementação atual**, conferindo anotações/roteamento, configuração de prefixos, controllers e contratos. Use Swagger/OpenAPI e coleções antigas como fontes complementares, não como substitutos à implementação.

Para **cada endpoint HTTP efetivamente exposto**:
1. Identifique método e caminho, incluindo path params, query params e headers conhecidos;
2. Registre nome, descrição objetiva, referência para o handler e domínio/assunto quando houver evidência;
3. Organize a coleção em pastas por domínio/recurso/controller conforme a organização real e a melhor legibilidade;
4. Use variável de coleção **baseUrl** em lugar de hosts fixos. Use placeholders para tokens e identificadores. Não inclua credenciais ou dados reais;
5. Quando existir **request body**, inclua exemplo JSON sintético coerente com DTO/schema/model de entrada: estrutura aninhada, tipos, enums e campos relevantes. Diferencie obrigatório/opcional **somente se demonstrável** por validações/contratos;
6. Quando não existir body, não gere body fictício. Para outros content types (multipart, form-urlencoded, XML etc.), modele a modalidade correta; não force JSON;
7. Inclua parâmetros de URL e headers apenas quando forem conhecidos ou convencionais e necessários ao request. Marque hipóteses relevantes como pendências, sem apresentá-las como garantias;
8. Descreva respostas e códigos observados no contrato; exemplos de response body são **opcionais** e nunca devem ser inventados como respostas garantidas;
9. Preserve separação entre rotas expostas e handlers internos, consumers de mensageria, jobs e clients HTTP. **Não coloque esses últimos na coleção como endpoints da aplicação**;
10. Se rota, verbo, perfil de exposição ou payload não puderem ser determinados com segurança, registre em VALIDACOES_PENDENTES.md. Nunca complete com suposição silenciosa.

**Validação offline da coleção:** verifique, com meios locais disponíveis e sem chamadas à aplicação, que o JSON é sintaticamente válido, que o formato contém info, item e variable conforme aplicável e que cada request possui método/URL coerentes. Quando existir ferramenta de validação da Collection v2.1 acessível sem instalação ou rede, utilize-a; caso contrário, declare a limitação. Não afirme validação integral do schema se fez apenas parse JSON.

## 9. VALIDACOES_PENDENTES.md — arquivo obrigatório

Crie o arquivo mesmo que inicialmente não haja dúvidas. Se nenhuma pendência for encontrada, registre que **nenhuma foi identificada dentro do escopo analisado**, sem afirmar certeza absoluta.

Cada pendência deve ter um registro com:
- **ID estável**: VP-001, VP-002 etc.;
- **Título / assunto / domínio** afetado;
- **Pergunta clara para pessoa responsável por negócio ou tecnologia**;
- **O que o código demonstra** (fatos observáveis);
- **O que não foi comprovado** (interpretação, condição ausente, trecho não acessível ou conflito);
- **Fontes de evidência**: caminhos, símbolos e linhas disponíveis;
- **Confiança estimada (%) e justificativa**;
- **Impacto na documentação, nos fluxos ou na coleção Postman**;
- **Responsável sugerido por papel**, sem inventar nomes de pessoas;
- **Status**: pendente, em_validacao, resolvida;
- **Local da documentação relacionada**, incluindo IDs de regra/endpoints, quando houver.

Classifique também por prioridade/risco quando apropriado. Não marque uma dúvida como resolvida sem nova evidência ou validação humana rastreável. Evite duplicatas: reutilize IDs preexistentes e atualize registros sem apagar histórico útil.

## 10. Ordem de execução

Siga esta ordem:
1. Validar acesso e identificar os dois diretórios; localizar estrutura documental preexistente.
2. Inventariar os módulos e os principais elementos de negócio/técnicos da aplicação.
3. Rastrear fluxos e relacionamentos com evidências do código e testes.
4. Classificar evidências e identificar pendências abaixo de 85% **antes de escrever conclusões assertivas**.
5. Criar/atualizar documentação .md por domínio, assunto, integração, arquitetura, API, dados e operação, conforme aplicável.
6. Montar GLOSSARIO.md e validar consistência terminológica, IDs e links.
7. Gerar ou atualizar postman_collection.json a partir do inventário de endpoints.
8. Gerar/atualizar VALIDACOES_PENDENTES.md com todas as lacunas significativas.
9. Consolidar README.md e INDICE_RAG.md, garantindo navegação e rastreabilidade.
10. Realizar checagens offline de integridade e emitir um resumo final de execução.

Quando já existir documentação, confronte-a com a versão atual do código e:
- Preserve estrutura, IDs e informações humanas verificadas;
- Atualize partes comprovadamente desatualizadas;
- Não duplique páginas sobre o mesmo conceito;
- Registre conflitos e mudanças relevantes;
- Evite substituir por um texto genérico uma documentação preexistente mais detalhada.

## 11. Checklist de qualidade — obrigatório antes de encerrar

- [ ] Nenhum arquivo da APLICACAO_ALVO foi modificado.
- [ ] Todos os arquivos novos/alterados estão em DIRETORIO_DOCUMENTACAO.
- [ ] Nenhuma regra de negócio ambígua foi afirmada como fato sem evidências suficientes.
- [ ] Regras com confiança abaixo de 85% foram encaminhadas a VALIDACOES_PENDENTES.md.
- [ ] Assuntos, domínios e integrações têm relações e links navegáveis.
- [ ] Documentos .md têm títulos, IDs, metadados úteis, fontes e estrutura adequada ao Obsidian/RAG.
- [ ] Frontmatters YAML e links locais foram verificados no que for tecnicamente possível.
- [ ] O inventário de endpoints foi confrontado com a implementação.
- [ ] postman_collection.json é JSON válido e inclui body sintético coerente quando aplicável.
- [ ] Nenhum segredo ou dado pessoal real foi copiado para documentos ou coleção.
- [ ] O README informa o escopo, as áreas não inspecionadas e a natureza estática da análise.
- [ ] Não foram descritos como implementados recursos apenas planejados.
- [ ] Nenhum arquivo ou conteúdo humano relevante foi sobrescrito sem cuidado.

## 12. Relatório final obrigatório

Depois de **criar os arquivos**, responda em português brasileiro com um resumo objetivo contendo:

1. Caminho absoluto da documentação produzida;
2. Lista de arquivos criados, atualizados e preservados;
3. Quantidade de assuntos, domínios, integrações, regras e endpoints identificados, **sem afirmar completude onde não houve cobertura**;
4. Total de pendências, principais riscos e respectivas perguntas que precisam de validação;
5. Situação da coleção Postman: total de requests, presença de modelos de request body e validações offline executadas;
6. Cobertura, limitações de acesso, fontes não analisadas e versão/commit usados (se conhecidos);
7. Confirmação de que **nenhum arquivo da aplicação foi alterado**.

**Importante: o resultado esperado são os artefatos efetivamente gravados em DIRETORIO_DOCUMENTACAO, não uma sugestão de documentação futura.**
