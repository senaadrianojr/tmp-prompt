# DARWIN MONITOR — ETAPA 01/04 | PROMPT CORRETIVO
## Revisão da auditoria e correção de classificação documental

### Papel e objetivo
Atue como auditor técnico sênior de documentação, com foco em rastreabilidade, coerência de escopo, referências canônicas e preservação de evidências.

Já foi executada a **Etapa 01 — Auditoria e Classificação Documental**. Foi gerado um arquivo denominado `AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md`, porém a revisão humana identificou inconsistências. Sua missão é **corrigir a auditoria existente**, aproveitando o trabalho válido já realizado.

**NÃO execute a Etapa 02 de revalidação técnica, nem a Etapa 03 de consolidação documental, nem a Etapa 04 de arquivamento.** Esta execução é estritamente corretiva da Etapa 01.

### 1. Confirme a raiz real de execução antes de agir
O agente pode ter sido iniciado diretamente no diretório **`darwin-monitor/`**, e não na raiz de um repositório que contenha `docs/darwin-monitor/`. Na execução anterior, o caminho de saída aparentemente foi concatenado duas vezes, resultando em algo como:

- Incorreto, **se** a raiz atual já é `darwin-monitor/`: `./docs/darwin-monitor/AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md`
- Correto nesse cenário: `./AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md`

Instruções obrigatórias:
1. Confira `pwd` (ou equivalente) e liste os diretórios reais.
2. Identifique a raiz pelo conteúdo, como `README.md`, `indicadores/`, `dashboards/`, `planos/`, `analises-por-indicador/`.
3. Localize o relatório já criado em qualquer dos caminhos candidatos; não presuma qual existe.
4. Se confirmar que o relatório está na subestrutura duplicada, **mova o mesmo relatório** à raiz correta (não deixe duas cópias concorrentes).
5. Se existir outro arquivo no destino, compare antes de agir e interrompa a movimentação se houver conflito de conteúdo; registre o problema para decisão humana, sem sobrescrever.
6. Remova apenas pastas redundantes criadas por engano **se estiverem vazias** depois da movimentação. Nunca apague arquivos de terceiros ou outras evidências.
7. Todos os caminhos do relatório devem ser relativos à raiz efetiva identificada e coerentes com a árvore real.
8. Se não for possível identificar a raiz com segurança, interrompa alterações físicas e relate o bloqueio.

### 2. Escopo fechado e inalterável nesta revisão
O levantamento com o time delimitou a entrega atual a estes **seis indicadores**, sujeitos à conferência da coluna `Responsável = SEGUROS` na planilha original:

| ID | Indicador | Aplicação(ões) informada(s) pelo time |
|---|---|---|
| 01 | Índice de Sucesso | Gestão Eventos Conta Cartão |
| 02 | Taxa de Falha | Gestão Eventos Conta Cartão |
| 03 | Taxa de Falha no Cancelamento | Gestão Eventos Conta Cartão; Gestão Eventos Pagamento |
| 04 | Taxa de Reprocessamento | Gestão Eventos Conta Cartão |
| 05 | Parcela Cobrada x Parcela Lançada | Gestão Eventos Pagamento |
| 06 | Origem | Venda Digital |

- Esta seleção é uma **decisão de escopo** e **não prova** da viabilidade ou da regra de negócio.
- Um indicador não listado **não deve** ter documentação operacional classificada como ativa para a entrega atual apenas por existir em `indicadores/`.
- Se `Responsável` for diferente de `SEGUROS`, classifique como **FORA DO ESCOPO POR RESPONSABILIDADE**.
- Se for `SEGUROS`, porém não estiver na seleção, classifique como **BLOQUEADO/FORA DA ENTREGA ATUAL**, sem afirmar impossibilidade definitiva.
- Quando a coluna `Responsável` não estiver comprovadamente verificada, informe **PENDENTE DE CONFERÊNCIA NA PLANILHA ORIGINAL**. Não invente confirmação.
- Preserve todos os requisitos e fontes originais como referências históricas; não edite planilhas.
- Não acrescente indicadores a esta lista por inferência ou conveniência.

### 3. Reclassifique os arquivos indevidamente considerados ativos
A auditoria anterior marcou vários documentos de indicadores **não selecionados** como `MANTER_ATIVO`. Confira os caminhos e conteúdos reais, especialmente os arquivos em `indicadores/` relativos a:
- Cobrança de Seguros Cancelados.
- Cobranças em Duplicidade.
- Data de Vencimento x Postagem.
- Ausência de Evento de Pagamento.
- Conciliação Financeira.
- Estornos Indevidos.
- Processamento de Seguros — MOB.

Para cada documento:
1. Identifique o indicador real tratado no conteúdo, sem confiar apenas no nome.
2. Diferencie documento canônico de indicador da entrega atual, histórico preservado e documento misto.
3. **Documentos exclusivos de indicadores não selecionados** devem ser reclassificados como `CANDIDATO_ARQUIVO` (ou `REFERENCIA_HISTORICA` se forem fonte original de requisitos), não como `MANTER_ATIVO`.
4. **Documentos mistos** devem ser classificados como `REVISAR_PARCIALMENTE`, registrando os trechos relevantes para os seis.
5. Não mova nem altere o conteúdo dos documentos agora. Recomende as ações para Etapas 03 e 04.
6. Identifique quais documentos dos seis indicadores **ainda não existem** ou estão apenas citados indiretamente; não marque a cobertura como completa sem evidência.

Uma classificação de `MANTER_ATIVO` é aceitável para documentos transversais (por exemplo, README/arquitetura), desde que precisem ou não de revisão e que seu papel na documentação **dos seis** esteja explicitamente justificado. Não confunda "arquivo útil como histórico" com "documentação ativa da entrega".

### 4. Não confunda "Origem" com "Processamento de Seguros — MOB"
Foi identificada possível associação indevida de:
`indicadores/03-recorrencia-processamento-seguros-mob.md`
ao indicador `Origem`.

**Não assuma que MOB = Origem.** A hipótese de negócio levantada é que Origem possa representar a via de processamento de pagamento (Pismo ou DXC), mas também existe a possibilidade de significar origem/canal de contratação. Ainda não está definida.

Ajuste o relatório para:
- Tratar `Processamento de Seguros — MOB` e `Origem` como conceitos distintos até evidência que demonstre relação.
- Não apresentar Pismo/DXC como conclusão confirmada.
- Marcar a regra funcional do indicador Origem como **PENDENTE DE VALIDAÇÃO COM NEGÓCIO/PO**.
- Informar se existe documento canônico real do indicador Origem; caso contrário, registrar lacuna documental.
- Deixar qualquer conferência de código, telemetria e integração Pismo/DXC para a Etapa 02.

### 5. Reconciliar inventário e totais sem mascarar divergências
O resumo exibiu **29 arquivos auditados**, mas as classes apresentadas somam **28**:
15 manter ativos + 3 revisar parcialmente + 1 revalidar tecnicamente + 5 candidatos a arquivo + 4 referências históricas = 28.

A correção deve:
1. Realizar nova enumeração **factual** dos arquivos na raiz real do projeto, incluindo subpastas.
2. Explicitar se o relatório de auditoria criado na execução conta ou não no universo inventariado.
3. Diferenciar total de arquivos encontrados, total de arquivos legíveis/analisados, arquivos apenas catalogados e total classificado.
4. Garantir **uma única classificação principal por arquivo** e que a soma das classes coincida com o total de linhas classificadas.
5. Conferir os artefatos não-Markdown: a captura sugere múltiplas planilhas Excel, PDF, JSON e PNG. Não repetir "4 não legíveis" automaticamente: conte os arquivos reais e reporte a limitação para cada um.
6. Não inventar leitura de planilha, PDF ou imagem. Se não houver meio de inspecionar, informar explicitamente o que não foi confirmado.
7. Evitar que um arquivo movido seja contado em duplicidade por causa da estrutura redundante.

A apresentação deve ser auditável: caminhos completos relativos à raiz, contagem por classificação e justificativa objetiva, sem arredondamentos ou números herdados sem conferência.

### 6. Corrigir alegações de validação não realizadas
O relatório informa `SEGUROS (Confirmado documentalmente)` para alguns indicadores, mas também diz que as planilhas originais não tiveram o conteúdo lido.

Reescreva os status de forma precisa:
- **Confirmado na planilha original:** apenas se a célula, aba e arquivo originais foram efetivamente inspecionados.
- **Informado pelo time:** escopo decidido na conversa, sem verificação da planilha.
- **Indicado em documentação secundária:** evidência indireta, citar arquivo/seção.
- **Pendente de validação:** sem fonte primária confirmada.

Se conseguir ler os arquivos originais **apenas para conferência documental**, sem alterar nada, valide a coluna `Responsável` e cite arquivo/aba/linha. Se não conseguir, mantenha pendência explícita para a equipe, **sem travar** a revisão documental restante.

Também revise frases genéricas como "ausência de versionamento": se Git for utilizado, o problema a documentar é a **falta de definição de documento vigente versus histórico**, não necessariamente a inexistência de versionamento.

### 7. Rever recomendações de documento canônico e arquivamento
- Reavalie a relação entre `analises-por-indicador/`, `indicadores/`, `dashboards/` e `planos/`.
- Não recomende arquivar uma **pasta inteira** só porque seu nome sugere análises intermediárias. Analise seus arquivos individualmente e separe evidências únicas ainda úteis.
- Não trate automaticamente `rev2`, `revalidacao` ou o arquivo mais recente como a verdade. Compare alegações, escopo e evidências.
- O snapshot JSON e outras fontes técnicas podem ser úteis ao diagnóstico: registre sua função e o motivo real para recomendação de arquivo; não arquive por extensão ou antiguidade.
- Documento canônico deve refletir os seis indicadores da entrega e ter seu papel claro. Apresente candidatos, não declarações definitivas sem validação.
- O arquivo original de requisitos, mesmo fora do escopo atual, precisa continuar acessível.

### 8. Resultado e limites de escrita
**Único artefato de conteúdo autorizado:** atualizar o arquivo `AUDITORIA_ETAPA_01_ESCOPO_DOCUMENTAL.md` existente, no caminho correto identificado na raiz do projeto.

Pode corrigir a **localização física** do próprio relatório e limpar exclusivamente diretórios redundantes vazios criados por engano. Fora disso:
- Não criar novos documentos, sub-relatórios, cópias `rev2` ou backups.
- Não editar, mover ou excluir arquivos documentais existentes além do próprio relatório.
- Não alterar código das aplicações, queries reais, Datadog, dashboards, monitores, infraestrutura ou banco.
- Não fazer commit/push sem autorização específica.
- Não iniciar a Etapa 02.
- Nunca incluir credenciais, tokens ou dados pessoais no relatório.

**Estrutura esperada do relatório corrigido:** preservar seções relevantes da auditoria existente, corrigindo metadados, resumo, escopo, inventário, documentos canônicos, inconsistências, plano para Etapas 02–04, decisões humanas e conferência de integridade. Acrescentar a explicação de como o total de arquivos foi reconciliado.

### 9. Critérios de aceite
A correção só está pronta se:
- [ ] A raiz real foi comprovada e não restou uma cópia concorrente do relatório em diretório redundante.
- [ ] O relatório é único e está no local correto.
- [ ] As contagens do inventário batem com as classificações e os arquivos realmente encontrados.
- [ ] Os seis indicadores foram tratados como escopo corrente, com confirmação de `Responsável` devidamente qualificada.
- [ ] Documentos exclusivos de indicadores excluídos não figuram como operacionalmente ativos.
- [ ] `Origem` não está equiparado a `MOB` sem prova; regra de negócio continua pendente.
- [ ] Há distinção clara entre documentação vigente, histórica e candidata a arquivo.
- [ ] Todos os arquivos não lidos estão identificados sem conclusões indevidas sobre seu conteúdo.
- [ ] Nenhum documento operacional foi modificado, arquivado ou apagado.
- [ ] O relatório corrigido aponta objetivamente o que deverá ser validado na Etapa 02.

### 10. Resposta final exigida
Ao terminar, apresente:
1. Diretório raiz confirmado e caminho final do relatório.
2. Total de arquivos encontrados, inspecionados e classificados, com soma demonstrada.
3. Lista objetiva das classificações corrigidas (antes -> depois, com caminhos).
4. Situação da confirmação na planilha original, em especial `Responsável` e `Origem`.
5. Eventuais conflitos/pendências ainda não resolvidos.
6. O que foi alterado e o que permaneceu intocado.
7. Declaração explícita: **"Etapa 01 corrigida; aguardando aprovação humana para iniciar a Etapa 02"**, somente se os critérios acima forem cumpridos. Caso contrário, informe quais critérios falharam sem alegar conclusão.
