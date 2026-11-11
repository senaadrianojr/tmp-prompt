# PROMPT 05K — Completar Descrições dos Widgets do Dashboard UAT (Correção Editorial Local)

> **Executor:** Gemini CLI no workspace corporativo autorizado.
>
> **Repositório público:** somente instruções metodológicas. Não publicar aqui JSONs, nomes internos, serviços, queries, IDs, títulos internos, capturas, relatórios, códigos, métricas, endpoints, credenciais ou dados corporativos.
>
> **AUTORIZAÇÃO NESTA ETAPA:** leitura remota autorizada **somente GET**, inspeção dos artefatos privados e criação de **novos arquivos candidatos locais**. **NÃO executar PUT/POST/PATCH/DELETE no Datadog, NÃO modificar o canônico oficial, NÃO modificar aplicações/scripts/pipelines/testes, NÃO fazer commit/push, NÃO tocar em produção.** A autorização de escrita da etapa editorial anterior já foi consumida. Qualquer nova publicação exige nova aprovação expressa após revisão.

## 1. Razão e fato observado pelo responsável

Depois da publicação editorial anterior e da sincronização do canônico, o operador abriu o **editor visual de widgets** e constatou que **diversos campos “Description” permanecem vazios**, inclusive:
- todos os widgets do grupo executivo de origem;
- um widget de volumetria e erros da recorrência de cartão, vinculado ao grupo de sucesso;
- parte dos widgets do grupo de cancelamento e liquidação.

Essa validação visual é suficiente para **reprovar o aceite editorial de completude das descrições**. Não pedir novas capturas ou reauditar logs/código; a ausência do campo já foi confirmada pelo usuário. **A publicação editorial anterior pode ter sido tecnicamente correta e os títulos podem estar corretos, mas a cobertura de descrições está incompleta.**

**Causa provável a confirmar, sem aceitá-la como fato até ler os arquivos:** o processo anterior tratou somente widgets de valor escalar como compatíveis com `description`, deixando sem esse campo widgets de série temporal, lista e ranking.

**Documentação oficial do Datadog:**
- `https://docs.datadoghq.com/dashboards/widgets/configuration/` — Widget descriptions: descrições de widgets de dashboard em geral, exibidas pela interface.
- `https://docs.datadoghq.com/dashboards/widgets/timeseries/` — `description` no schema.
- `https://docs.datadoghq.com/dashboards/widgets/top_list/` — `description` no schema.
- `https://docs.datadoghq.com/dashboards/widgets/list/` — `description` no schema.
- `https://docs.datadoghq.com/api/latest/dashboards/get-a-dashboard/` — schemas e definições por tipo.

**Não perpetuar a alegação de que apenas widgets `query_value` aceitam descrições.** Confirmar o schema do **tipo real** de cada widget e o caminho `definition.description` dentro do JSON da API. A existência do campo visual no editor, confirmada pelo operador, é evidência da funcionalidade do produto. Se houver comportamento diferente em uma versão/API concreta, relatar a divergência com fundamento.

## 2. Escopo estritamente editorial

Objetivo: **todos os widgets de dados do dashboard UAT devem ter descrição legível, vinculada ao indicador correspondente, quando seu tipo efetivamente comportar `description`.**

O dashboard contém grupos e uma nota de cabeçalho; essa nota de texto não deve ser tratada como widget de dados com descrição obrigatória. Não criar notas, grupos, widgets, páginas ou outros artefatos de apresentação para contornar um campo ausente.

Respeitar o recorte já fechado dos indicadores, sem investigar linhas excluídas nem criar métricas. Uma descrição não pode converter contagem de tentativas em sucesso final, DLQ em taxa de reprocessamento, telemetria Kafka em prova de conciliação ou sinais correlatos em métrica de negócio não demonstrada.

**Não modificar**:
- títulos já aprovados de grupos e widgets;
- queries, métricas, fórmulas, aggregations, índices, template variables, filtros, time ranges e precisão;
- IDs, ordem, grupos, tipo, layout, posições e configuração de UI;
- descrição correta que já esteja presente;
- artefatos históricos, canônico oficial e dashboard remoto.

## 3. Inventário integral e diagnóstico preciso

1. Ler o relatório editorial anterior (prefixo `30-`) e o relatório da publicação/sincronização posterior (prefixo `31-`) no workspace privado. Evitar reler o histórico completo.
2. Fazer GET atual do dashboard UAT em leitura, registrar origem, timestamp e versão e salvar snapshot local novo com hash. Confirmar compatibilidade do GET com o canônico sincronizado; se houve alteração remota posterior, **não construir payload sobre base antiga**.
3. Percorrer recursivamente **todos os widgets de dados**, inclusive aqueles aninhados nos grupos. Não examinar somente `query_value` ou o primeiro nível de `widgets`.
4. Para cada widget, criar uma tabela privada com **grupo, ID, tipo, título, presença do campo `definition.description`, conteúdo atual, se o tipo aceita `description` conforme schema e estado**:
   - **PREENCHIDA E ADEQUADA**;
   - **VAZIA / AUSENTE — SUPORTADA**;
   - **PREENCHIDA, MAS ENGANOSA OU VAGA** (revisão humana);
   - **NÃO SUPORTADA** com justificativa específica e documentada.
5. Separar contagem **por tipo de widget e por grupo**; verificar expressamente os três conjuntos apontados pelo responsável. Não afirmar que todas as descrições estão presentes com base em contagem total de strings, notas ou campos `title`.
6. Usar a documentação atual para esclarecer se `timeseries`, `toplist`, `list_stream` e demais tipos efetivos aceitam o campo no nível `definition`. Onde houver dúvida não inferir indisponibilidade: verificar schema oficial ou testes locais seguros.

## 4. Redação das descrições que faltam

Gerar **em português claro**, com 1–3 frases concisas por widget:
1. **O que realmente aparece**, incluindo unidade (volume de eventos, tentativas, operações, séries ao longo do tempo, segmentação por origem etc.) e perspectiva temporal quando relevante.
2. **Por que a visualização importa** para o indicador executivo do seu grupo.
3. **Limite semântico importante**, apenas quando necessário para evitar interpretação equivocada: por exemplo, contagem de mensagens de erro não equivale a taxa de negócio; sucesso de entrega local confirmado pode ser sucesso do componente sem evidenciar processamento externo.

**Princípio de responsabilidade:** a equipe mede os marcos observáveis dentro de suas aplicações. Se há confirmação de transferência de responsabilidade, não desqualificar o sucesso local pela ausência de monitoramento externo. Não afirmar transferência confirmada quando existe apenas log de tentativa.

**Os títulos já aprovados são contexto, não fonte suficiente para afirmar semântica.** Derivar a descrição de query, tipo de agregação, parâmetros reais do widget e matriz privada de aderência, consultadas **pontualmente**. Não inventar volume, taxas, deduplicação, cobertura integral, eventos que não existem ou semântica de negócios fora do escopo.

**Não substituir descrições existentes adequadas.** Se uma descrição existente parecer inadequada, registrar proposta separada, sem alterar automaticamente neste ciclo, a menos que seja um erro material claro e autorizado pelo escopo de correção de descrições.

## 5. Candidato local e teste de não regressão

1. Criar **um novo JSON candidato privado**, baseado no GET remoto mais recente, com adição de `definition.description` apenas nos widgets efetivamente elegíveis com campo vazio/ausente. Nenhum outro path pode variar.
2. Produzir um **diff semântico remoto × candidato** por path/ID. Critério absoluto: **somente adições de `definition.description` em widgets elegíveis**, nenhuma mudança de valor em campo preexistente, nenhuma remoção e nenhuma alteração de definição operacional.
3. Executar validador JSON/schema e dry-run **comprovadamente offline/read-only**; não acionar API de escrita. Se não houver validação segura, parar e registrar lacuna.
4. Conferir que textos não estão em `description` na raiz do widget nem em componente errado quando o editor espera `definition.description`.
5. Construir matriz de aceite: **número total real de widgets de dados = descrições já preenchidas + descrições adicionadas + exceções comprovadamente não suportadas**. Se houver widget elegível com descrição vazia, marcar **NO-GO**.
6. Se a API aceitar o campo mas não for possível provar sua renderização na UI por meio apenas do JSON, registrar como **PENDENTE DE VALIDAÇÃO VISUAL APÓS FUTURA PUBLICAÇÃO**. Não declarar que a interface já foi testada.

## 6. Entregáveis privados

Criar relatório Markdown local com prefixo **`32-`** em `darwin-monitor/planos/`, além do snapshot e do candidato, sem sobrescrever nada existente.

Relatório:
- motivo do retrabalho e hipótese de causa;
- inventário completo por **grupo e tipo**, com títulos/IDs em conteúdo **privado**;
- quantidade real de descrições existentes, faltantes e propostas;
- **texto literal proposto** de cada descrição nova e sua relação com o indicador;
- evidências/documentação de suporte a `description` dos tipos encontrados;
- relatório de diff e validação técnica; zero mudanças funcionais;
- caminhos/hashes e estado do canônico, UAT, produção e scripts;
- eventuais exceções e bloqueios;
- resultado **PRONTO PARA REVISÃO HUMANA / BLOQUEADO**, com publicação remota ainda não autorizada.

## 7. Parada

Mostrar no terminal um sumário: quantos widgets totais, quantos já tinham descrição, quantos ficaram preenchidos no candidato, quantos faltam por restrição efetiva do schema; grupos priorizados, paths do relatório/candidato/snapshot, diffs de regressão e ausência de escrita remota.

**PARE antes de publicar.** Não solicitar outra rodada de screenshots para repetir o diagnóstico. O responsável e o ChatGPT revisarão o Relatório 32 e só então haverá nova decisão específica sobre publicação em UAT e posterior sincronização do canônico.
