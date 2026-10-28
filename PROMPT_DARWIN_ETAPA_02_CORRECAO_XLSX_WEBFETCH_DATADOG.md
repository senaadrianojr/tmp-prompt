# DARWIN MONITOR — ETAPA 02 | CORREÇÃO E VERIFICAÇÃO OPERACIONAL

## Diagnóstico obrigatório de XLSX, Python e acesso real ao Datadog (WebFetch + procedimento existente)

> **Escopo desta execução:** corrigir lacunas de verificação da Etapa 02. Não reiniciar a análise do código desnecessariamente e não avançar para as Etapas 03 ou 04.
>
> **Saída autorizada:** atualizar **somente** o relatório existente `./REVALIDACAO_TECNICA_ETAPA_02.md`, a partir da raiz real `darwin-monitor/`. Nada de criar `docs/darwin-monitor/` dentro da raiz.

### 1. Contexto — problemas encontrados na execução anterior

A execução anterior encerrou a Etapa 02 com dois impedimentos **não demonstrados adequadamente**:

1. Para arquivos `.xlsx`, tentou usar `grep` em arquivos binários/ZIP e concluiu que não era possível ler as planilhas. Posteriormente alegou que não poderia usar Python por ausência de bibliotecas externas, sem apresentar testes suficientes do Python, bibliotecas instaladas e alternativa por bibliotecas padrão.
2. Para o Datadog, declarou inexistência de credenciais ou ferramentas autorizadas e não comprovou ter localizado e seguido o procedimento de autenticação **já utilizado anteriormente no projeto**.

Essa conclusão não é suficiente. **Há histórico de acesso real ao Datadog e de leitura de planilhas em atividades anteriores deste ambiente de trabalho.** Isso não garante que o acesso ainda esteja válido, mas obriga a localizar o procedimento, testá-lo adequadamente e registrar o motivo real de qualquer falha.

**Não aceite sua própria descrição anterior de incapacidade como prova.** Uma limitação operacional só pode ser declarada depois de uma tentativa concreta ou de uma proibição explícita do ambiente, ambas documentadas.

### 2. Regras de execução e segurança

- Trabalhe **somente em modo de leitura** nos documentos originais, planilhas, aplicações e Datadog.
- É autorizado executar **comandos locais não destrutivos de diagnóstico e leitura**, inclusive scripts Python inline, para abrir formatos de arquivo e consultar serviços conforme o runbook aprovado.
- Não instale pacotes, altere configurações, inicie mecanismos de autenticação não previstos, modifique aplicações, crie dashboards/monitores ou escreva em bancos.
- Não imprima, copie para relatórios ou exponha cookies, tokens, chaves, headers sensíveis, arquivos de sessão, CPF, contratos reais ou payloads de clientes.
- Não transmita credenciais ou dados internos a serviços de terceiros. Use somente o host oficial/ambiente Datadog corporativo e métodos explicitamente autorizados pelo runbook.
- Não use ferramentas externas para contornar restrições de segurança, autenticação ou proxy. Se algum recurso não estiver autorizado, registre precisamente a restrição.
- **Não faça commit ou push.**
- Não crie relatórios auxiliares, revisões `rev2`, novos prompts, scripts persistentes, cópias de arquivos ou diretórios redundantes.
- Se uma execução exigir aprovação que não foi dada, pare apenas aquele teste, peça a autorização adequada e continue as outras verificações permitidas.

### 3. Preflight: confira o diretório e ferramentas reais

Antes de alegar qualquer limitação:

1. Confirme `pwd`/diretório atual e identifique o projeto pelos diretórios `indicadores/`, `dashboards/`, `planos/`, e por `REVALIDACAO_TECNICA_ETAPA_02.md`.
2. Liste **quais ferramentas o agente realmente possui**, distinguindo `run_shell_command`, `read_file`, `WebFetch` (ou equivalente), ferramentas de busca e quaisquer conectores disponíveis.
3. Execute comandos read-only para detectar o interpretador: `python --version`, `python3 --version` ou `py -V` (conforme o sistema).
4. Teste **de verdade** e registre apenas os resultados não sensíveis:
   - `python -c "import sys; print(sys.version)"`
   - `python -c "import zipfile, xml.etree.ElementTree as ET; print('zip/xml OK')"`
   - `python -c "import importlib.util; print('openpyxl:', bool(importlib.util.find_spec('openpyxl'))); print('pandas:', bool(importlib.util.find_spec('pandas')))"`
5. Se não existir Python funcional, teste recursos read-only adequados ao formato realmente presentes (por exemplo, PowerShell com suporte .NET ZIP/XML, ou outro interpretador), sem concluir indisponibilidade antes de verificá-los.
6. Caso algum comando falhe, registre a ferramenta usada, comando **sanitizado**, código/erro, e o que isso significa; não invente retorno de execução.

**O Trust Mode habilitado no editor não prova nem impede, por si só, a disponibilidade das ferramentas.** Confirme o que funciona no runtime real.

### 4. Verificação XLSX — sem dependências externas, quando necessário

Priorize a leitura das planilhas de referência presentes na raiz ou subpastas do projeto, especialmente:
- `Monitoramento - Planejamento.xlsx` (se existir com esse nome);
- `Monitoramento Cartões 1.xlsx` (se existir);
- outros arquivos Excel identificados na auditoria.

**Obrigatório:**
1. Localize os caminhos reais. Não presuma nomes nem que todas as planilhas possuam o mesmo formato.
2. Se `openpyxl` estiver instalado, utilize `load_workbook(..., read_only=True, data_only=True)` e, quando necessário, uma segunda leitura com `data_only=False` para diferenciar fórmulas de valores em cache.
3. **Se `openpyxl` não estiver instalado, use a biblioteca padrão do Python**: um arquivo `.xlsx` é um contêiner ZIP de XML. Use `zipfile` e `xml.etree.ElementTree` para ler as abas e células.
4. Demonstre efetivamente `zipfile.is_zipfile(caminho)`, liste os membros XML relevantes e leia `xl/workbook.xml`, `xl/_rels/workbook.xml.rels`, `xl/worksheets/*.xml`, `xl/sharedStrings.xml` (quando existir) e valores inline. Faça a resolução correta das relações `r:id` para nomear abas. Respeite células vazias, referências A1, merges e índices da tabela `sharedStrings`.
5. Não imprima todas as linhas indiscriminadamente; extraia apenas cabeçalhos e células necessárias aos seis indicadores e à coluna `Responsável`.
6. Informe **arquivo, aba, linha/célula do indicador, linha/célula do responsável, valor observado e ambiguidade**, se houver.
7. Se `Origem` não constar claramente na planilha, registre esse resultado sem inventar equivalência com `MOB` ou Pismo/DXC.
8. Se o XLSX for criptografado, corrompido ou não tiver dados exigidos, prove a condição por leitura/erro específico. Não instale dependências sem autorização.
9. Se já existir outro procedimento de leitura de planilha que tenha funcionado anteriormente, localize-o e avalie sua reutilização antes de declarar impossibilidade.

**É proibido repetir `grep arquivo.xlsx` como método de extração estruturada.**

A ausência de `openpyxl`/pandas **não** é evidência suficiente de impossibilidade: tente o caminho ZIP/XML padrão. Se houver falha no parser, registre o motivo técnico e a localização exata da falha.

### 5. Datadog — reutilização obrigatória do acesso já implementado

**Acesso Datadog já foi objeto de investigação e execução em atividades anteriores.** Você deve procurar e seguir o mecanismo existente, em vez de presumir que não há credenciais ou recursos.

#### 5.1 Descobrir o runbook e os artefatos existentes

Procure no workspace e nas áreas locais autorizadas:

- `GEMINI_PLATFORM_ACCESS_RUNBOOK.md`;
- README e instruções do projeto de sincronização/autenticação associado ao Gemini;
- pasta anteriormente referenciada como `%USERPROFILE%\Tools\cookie-sync\gemini-auth-sync\\` (verifique o caminho real do ambiente);
- arquivos de **metadados de validade da sessão**, quando previstos no runbook, sem revelar seus conteúdos sensíveis;
- documentação antiga do Darwin Monitor sobre execução de consultas reais no Datadog;
- registros/relatórios anteriores de acesso bem-sucedido e quais ferramentas foram efetivamente utilizadas.

**Não presuma que a documentação antiga está correta ou que a sessão antiga continua válida.** Identifique a sequência exata permitida pelo runbook atual e o método de autenticação. Não extraia credenciais para fora do fluxo autorizado.

#### 5.2 WebFetch obrigatório como caminho a VERIFICAR

1. Identifique a ferramenta **`WebFetch`**, `web_fetch` ou equivalente realmente disponibilizada ao agente; descreva os parâmetros e restrições relevantes.
2. Verifique se essa ferramenta **suporta a sessão autenticada corporativa** para o domínio oficial do Datadog usado pelo time. Não presuma que um fetch HTTP público herda cookies de navegador ou aceita headers/cookies personalizados.
3. Se o WebFetch suportar o método aprovado, faça uma **requisição somente leitura** a uma URL/end-point previamente validado no runbook, preferencialmente uma consulta pequena e sem dados pessoais.
4. Se WebFetch não puder autenticar, registre um teste seguro, ou a restrição de ferramenta observável, e **passe ao procedimento alternativo já documentado**, como `run_shell_command` + cliente HTTP local/servidor de sessão, **somente se previsto e permitido**.
5. Não encaminhe cookies/chaves em parâmetros de URL, não use proxy público, sites de diagnóstico de terceiros ou WebFetch sem suporte seguro à autenticação para transmitir segredos.
6. Se WebFetch for ausente/incompatível, **isso não encerra o diagnóstico**: localize e execute a via anteriormente comprovada, respeitando a segurança.
7. Se o runbook estiver ausente, registre os locais procurados e peça o caminho correto ao usuário; não invente autenticação nem tente burlar controle de acesso.

**Exigência central:** executar tentativas **reais e auditáveis**, não uma justificativa genérica. É aceitável concluir que o acesso está indisponível HOJE quando o bloqueio for comprovado (por exemplo, sessão expirada, 401/403, rota não acessível, WebFetch não autenticável, execução bloqueada ou ferramenta inexistente), mas não alegar "não existem credenciais" sem consultar os mecanismos já implementados.

#### 5.3 Verificação mínima de Datadog real

Após autenticar de modo autorizado, faça consultas **somente leitura**, com janela limitada e menor volume possível, para verificar:

- services reais de **Gestão Eventos Conta Cartão**, **Gestão Eventos Pagamento** e **Venda Digital**; descubra os nomes de `service` efetivamente utilizados, sem inventá-los;
- existência de métricas/APM/spans/logs associados aos seis indicadores;
- disponibilidade de eventos/status/campos necessários, evitando concluir que um simples log de erro prova a taxa calculável;
- se há informação em produção, homologação ou ambos, somente quando o ambiente for identificável;
- queries/filtros e janela temporal (sanitizados), retorno observado e eventuais `No Data`.

Se a API de consulta utiliza POST para **pesquisa**, isso continua sendo operação logicamente de leitura, mas só a execute se tal endpoint for expressamente permitido pelo procedimento de acesso. **Não use endpoints de criação/edição.**

`No Data` **não significa** "evento inexistente": diferencie falta de cobertura, filtros, retenção, indexação, permissão, amostragem e janela temporal.

Para cada tentativa registre: ferramenta, tipo de consulta, ambiente/serviço, janela, código HTTP ou status equivalente, resumo do resultado, e evidência de sucesso/falha **sem segredos**.

Se um mecanismo já foi usado com sucesso anteriormente, compare o procedimento anterior e o atual: o que mudou (tempo da sessão, permissão, rede, ambiente, arquivo/caminho de autenticação, parâmetros de ferramenta)? Evite atribuir falhas automaticamente ao ambiente.

### 6. Revisão crítica das conclusões anteriores sobre os seis indicadores

A resposta anterior declarou quatro indicadores `Comprovável hoje` mesmo sem consulta real ao Datadog. Essa formulação é ambígua e potencialmente incorreta.

Reavalie individualmente, mantendo como hipóteses técnicas a investigar:

| Indicador | Resultado anterior | Verificação exigida |
|---|---|---|
| Índice de Sucesso | Evento 31 / status 8 no código | Validar definição, população/denominador e telemetria observável |
| Taxa de Falha | Evento 31 / status 10 e logs de erro | Distinguir falhas técnicas, definitivas e casos pendentes/retry |
| Taxa de Falha no Cancelamento | Log de máximo de tentativas | Confirmar evento de cancelamento, denominador, estado final e cobertura |
| Taxa de Reprocessamento | DLQ, PubSubRouter e retentativas | Separar operações afetadas e tentativas; evitar dupla contagem |
| Parcela Cobrada x Parcela Lançada | Parcial, com consulta downstream | Confirmar fonte de ambas as parcelas e necessidade real de correlação |
| Origem | Pismo/DXC no código, regra pendente | Não equiparar automaticamente MOB, canal de venda e via de pagamento |

**Classificações independentes e obrigatórias:**
- `COMPROVADO NO CÓDIGO` (caminho, classe, método e linhas, quando disponíveis);
- `CONFIRMADO NA PLANILHA` (arquivo, aba, linha/célula);
- `OBSERVADO NO DATADOG` (consulta real, período e resultado);
- `DOCUMENTADO, NÃO CONFIRMADO`;
- `HIPÓTESE / PENDENTE`;
- `BLOQUEADO POR DEFINIÇÃO, ACESSO OU FONTE`.

Para dizer **"indicador calculável hoje"**, não basta encontrar um evento ou log no código: demonstre definição funcional suficiente, numerador/denominador, evento elegível, tratamento de retries e acesso a dados com cobertura adequada. Se apenas o comportamento no código for comprovado, use `CÓDIGO IDENTIFICADO — DISPONIBILIDADE OPERACIONAL NÃO COMPROVADA`.

Não declare complementaridade entre índice de sucesso e taxa de falha sem validar população, status intermediários e fórmula.

### 7. Atualização controlada do relatório da Etapa 02

Atualize **apenas**:

`./REVALIDACAO_TECNICA_ETAPA_02.md`

Não crie subdiretórios ou arquivos separados.

Preserve evidências corretas já presentes. Atualize:

1. **Preflight real de ferramentas:** comandos de diagnóstico executados e resultados sanitizados.
2. **Verificação XLSX:** caminho, abas e células relevantes, ou erro técnico reproduzível; responsabilidade `SEGUROS` confirmada ou pendente.
3. **Procedimento Datadog:** runbook localizado, método previsto, tentativas WebFetch e fallback autorizado, resultado real e bloqueio concreto quando houver.
4. **Matriz dos seis indicadores:** separar comportamento de código, disponibilidade observada, regra funcional e viabilidade de cálculo/monitor.
5. **Correções das conclusões anteriores:** antes/depois e justificativa.
6. **Pendências de acesso/decisão humana:** o que precisa de PO/Negócio, SRE/Observabilidade ou donos das aplicações.
7. **Registro de integridade:** o que foi consultado e confirmação de que nenhum outro artefato foi alterado.

Não altere o relatório da Etapa 01: se uma informação anterior precisar de correção, registre a necessidade no relatório da Etapa 02 para tratamento posterior.

### 8. Critérios de conclusão — não declarar sucesso prematuramente

A etapa corretiva só pode ser considerada concluída para revisão humana quando:
- [ ] Ferramentas foram efetivamente verificadas, não apenas descritas.
- [ ] Houve tentativa de leitura XLSX por meio adequado, incluindo ZIP/XML nativo na ausência de `openpyxl`.
- [ ] `Responsável` foi conferido na fonte ou o bloqueio foi demonstrado por erro concreto.
- [ ] `GEMINI_PLATFORM_ACCESS_RUNBOOK.md` e o mecanismo anterior foram procurados/inspecionados, ou a ausência foi documentada com caminhos testados.
- [ ] WebFetch foi verificado quanto a existência, compatibilidade e acesso autenticado permitido.
- [ ] A via alternativa anteriormente usada foi testada quando autorizada e tecnicamente aplicável.
- [ ] O Datadog foi consultado de fato **ou** existe evidência precisa da impossibilidade atual, sem acesso não autorizado.
- [ ] As conclusões de "comprovável hoje" foram reavaliadas com linguagem rigorosa.
- [ ] Somente o relatório existente da Etapa 02 foi atualizado.

Se uma verificação não estiver disponível, **não finja execução ou êxito**. Declare `ETAPA 02 — CORREÇÃO PARCIAL`, exponha o impedimento comprovado e solicite a informação/autorização específica. Não avance para a Etapa 03.

### 9. Resposta final obrigatória

Apresente:
1. Comandos de verificação de Python e seus resultados, de forma sanitizada.
2. Caminho das planilhas e status da leitura; colunas/células confirmadas.
3. Local do runbook encontrado (sem segredos), ferramentas de acesso ao Datadog testadas, inclusive WebFetch.
4. Se conseguiu consulta real ao Datadog: qual recurso (sem dados pessoais), quando/janela e qual resultado; se não, **qual comando/chamada falhou e por quê**.
5. Situação revisada de cada um dos seis indicadores, distinguindo código, Datadog e definição funcional.
6. Caminho do relatório atualizado, limitações que permanecem e arquivos que ficaram intocados.

**Não conclua com "não consigo acessar" baseado apenas em suposições sobre dependências ou segurança. Teste o que for autorizado, registre evidências e, somente então, conclua.**
