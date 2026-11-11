# PROMPT 06B — Preparar Cópia Completa do Dashboard para Produção com Sinalização de Instrumentação

> **Executor:** Gemini CLI, no workspace corporativo autorizado.
>
> **Segurança do repositório público:** este arquivo descreve apenas métodos. NÃO publicar nele consultas literais, nomes/IDs internos de aplicativos, endpoints, widgets, organizações, branches corporativas específicas, planilhas, relatórios, snapshots JSON, logs, métricas, códigos ou credenciais. Todos os achados concretos e os candidatos ficam SOMENTE no workspace privado.
>
> **Mudança de estratégia deliberada:** um prompt antigo do ciclo 06 previa promoção *seletiva* de widgets e exclusão dos não prontos. Essa estratégia foi substituída por decisão do responsável: **preparar uma cópia COMPLETA do dashboard UAT para PROD**, mantendo a estrutura e os widgets mesmo quando algumas dependências de instrumentação ainda não estão disponíveis, com sinalização visual explícita. Não executar o prompt antigo como se governasse esta etapa.
>
> **Permissão desta etapa:** leitura autorizada, inspeção pontual de código, releases e artefatos privados, preparação de JSON(s) **locais** e relatório técnico. **NÃO criar dashboard remotamente em PROD, NÃO executar PUT/POST/PATCH/DELETE em nenhum Datadog, NÃO alterar UAT, canônico, código, testes, scripts, infraestrutura, Git nem pipelines.** Criação/publicação futura de dashboard PROD exigirá nova autorização expressa e específica.

## 1. Estado concluído e objetivo

O projeto de monitoramento em UAT foi finalizado e o responsável confirmou seu **aceite final de UAT em 11/11/2026**. O relatório privado de encerramento mais recente (prefixo `34-`, confirmar caminho real) registrou: canônico UAT sincronizado; dashboard com grupos e widgets preservados; descrições de todos os widgets de dados; sete indicadores delimitados, com classificação independente de cobertura operacional e aderência de negócio.

Não reabrir auditorias UAT, não modificar os sete indicadores nem tentar cumprir requisitos excluídos.

**Objetivo único:** preparar um **novo dashboard candidato de PRODUÇÃO, com cópia integral da estrutura, grupos e widgets atuais de UAT**, adaptado ao ambiente PROD, mantendo as visualizações que dependem de instrumentação não disponível **visivelmente marcadas como aguardando instrumentação**. A publicação não ocorrerá nesta etapa.

O perímetro continua sendo apenas o das **três aplicações sob gestão da equipe**. Não incluir sistemas externos nem exigir confirmação de desfechos além da transferência de responsabilidade efetivamente comprovada. Distinguir sucesso de etapa local e sucesso integral de negócio sem transformar o desconhecimento de etapa externa em falha do nosso serviço.

## 2. Fontes privadas e linha de base

1. Localizar o canônico UAT sincronizado, relatório de encerramento e último snapshot privado. Fazer **GET somente leitura** do dashboard UAT atual e comparar ao canônico aprovado. Se divergir inesperadamente, registrar e não usar uma fonte de verdade ambígua.
2. Fazer leitura autorizada do ambiente PROD para descobrir se já existe dashboard do mesmo projeto/propósito. **Não sobrescrever nem reutilizar ID de dashboard existente**. Se existir recurso PROD similar, obter snapshot e relatar **conflito/decisão de destino**; não criar versão local pronta para sobrescrevê-lo.
3. Descobrir serviço, ambiente, índices, tags, storage, filtros e variáveis **reais** de PROD; não realizar substituição textual indiscriminada de `uat` por `prod`. O valor efetivo da tag de ambiente precisa ser confirmado.
4. Verificar formatos de dashboard, metadados read-only, URLs e propriedades específicas de UAT que não devam ser clonadas. Manter estrutura e apresentação equivalentes, mas não copiar identificadores administrativos do recurso UAT como destino de PROD.
5. Registrar timestamp, origem e hash de snapshots no relatório privado. **Não consultar Log Explorer automaticamente por ferramentas não autorizadas**, nem contornar limitações de credenciais. Quando uma prova exigir Log Explorer, produzir roteiro/consulta privada e delegar ao usuário.

## 3. Inventário completo por widget e dependências reais

Percorrer **todos os widgets de dados recursivamente**, inclusive aninhados nos grupos, e preservar também notas de cabeçalho e propriedades de apresentação.

Criar matriz privada por widget:
`Grupo | Widget/ID | Indicador associado | Tipo/fonte | Dependências de atributos/telemetria | Código na branch principal? | Deploy em PROD comprovado? | Evidência de PROD? | Estado | Aviso proposto | Ajustes PROD necessários`.

Classificar dependências de:
- **correlação estruturada por proposta**, principalmente onde a consulta, facet, agrupamento ou cálculo realmente necessite do atributo;
- **identificação da processadora/origem** na aplicação de venda digital, quando o filtro/agrupamento utiliza o atributo;
- qualquer outra instrumentação nova efetivamente exigida pelo widget.

**Não supor que todo widget depende de correlação apenas por estar no grupo de recorrência.** Examinar cada query, fórmula, facet e template variable. Não remover filtros necessários nem modificar a semântica para disfarçar ausência de atributo. Tampouco supor que um widget *não* depende da instrumentação apenas porque não menciona o campo diretamente; verificar pré-requisitos da fonte.

### Checagem específica de disponibilidade na aplicação de venda digital

O responsável determinou que a sinalização de pendência referente à **processadora** seja **condicional**. Investigar pontualmente:
1. se o código que emite o campo está na branch principal de referência (e não apenas em uma branch de trabalho);
2. se a versão efetivamente **implantada em PROD** contém esse código;
3. se há evidência de **logs reais de PROD com o atributo e os eventos necessários** para cada widget dependente.

**Código na branch principal NÃO equivale a deploy e deploy NÃO equivale a telemetria observada.** Se o campo está na branch principal e os eventos relevantes estão realmente comprovados em PROD, **não sinalizar como aguardando instrumentação**. Se está na principal mas a publicação/runtime ainda não é comprovado, classificar como **PENDENTE DE COMPROVAÇÃO EM PROD**, não afirmar ausência da instrumentação. Se está ausente da versão implantada, usar **AGUARDANDO INSTRUMENTAÇÃO/DEPLOY**. A ausência de eventos em uma janela sem tráfego conclusivo não prova falta do atributo.

Aplicar raciocínio equivalente às aplicações de eventos de pagamento e de conta/cartão para a correlação por proposta.

## 4. Status, títulos e descrições de PROD

Para cada widget, emitir **um** status fundamentado:
- **OPERACIONAL COMPROVADO EM PROD:** dependência presente e sinal compatível comprovado; título e descrição sem aviso.
- **AGUARDANDO INSTRUMENTAÇÃO/DEPLOY EM PROD:** dependência ausente na versão implantada ou liberação explicitamente pendente; incluir aviso visível.
- **PENDENTE DE COMPROVAÇÃO EM PROD:** código/telemetria/versão não permitem atestar disponibilidade; incluir aviso distinto, sem declarar instrumentação ausente.
- **NÃO APLICÁVEL / DEPENDÊNCIA EXTERNA CONHECIDA:** se um widget já representa apenas sinal operacional parcial, preservar a descrição honesta e o contexto, sem acrescentar falsos bloqueios de instrumentação.

**Padrão visual sugerido:**
- Widget pronto: `<título executivo já aprovado>`;
- Widget esperando implantação: `⚠ <título executivo já aprovado>`, descrição termina com `⚠ Aguardando instrumentação/deploy em PROD: <dependência específica verificada>.`;
- Widget inconclusivo: `⚠ <título executivo já aprovado>`, descrição termina com `⚠ Disponibilidade em PROD ainda não comprovada: <evidência que falta>.`.

**Não alterar títulos de grupos indiscriminadamente**, não transformar aviso em uma afirmação de erro e não trocar "sem dados" por "zero" ou "sucesso". Os avisos **não** são métricas nem fórmulas. Se o tipo de widget não renderizar uma descrição na UI, manter a sinalização no **título**; se o ícone ⚠ tiver problema real de renderização, documentar fallback em texto curto, sem inventar suporte. Evitar prefixar widgets comprovadamente operacionais. Preferir sinalização objetiva a um rótulo genérico "em construção".

Descrição de cada widget continua explicando o que mede e qual objetivo operacional cobre. O aviso de instrumentação é um complemento e não deve apagar essa informação. **Não reclassificar cobertura de negócio** pelo fato de a cópia estar preparada.

## 5. Como construir o candidato completo para PROD

1. Partir do snapshot/canônico UAT consistente e preservar **todos** os grupos, widgets, notas, tipos, layout, posições, configurações de exibição e descrições.
2. Preparar novo título de dashboard que identifique claramente **PROD**, distinta da instância UAT; não copiar ID ou URL do dashboard de origem como destino.
3. Adaptar serviço/ambiente/index/storage/template variables para **PROD** somente com os valores comprovados nas configurações reais. Para consultas sem certeza sobre equivalente PROD, manter candidato local em estado **NÃO PUBLICÁVEL — FILTRO A CONFIRMAR**; não inventar. Jamais permitir `env:uat` ou links de UAT inadvertidamente.
4. Aplicar avisos **somente** aos widgets classificados como pendentes. O candidato deve permanecer estruturalmente completo; não excluir widgets pendentes nem inventar valores/consultas temporárias, não reconfigurar grupos para ocultá-los.
5. Se PROD já possui dashboard de mesmo projeto, documentar sua existência e definir candidato de **nova cópia independente** (ou conflito a decidir); não construir payload que sobrescreva dashboard existente.
6. Separar do JSON candidato o relatório de status/pendências; não inserir metadados não suportados no schema do Datadog.
7. Produzir um único **JSON candidato completo, local e não publicado**, além de um snapshot de base e relatório. Se houver valores de PROD não confirmados que impeçam montagem válida, produzir inventário e candidato explicitamente bloqueado, com os pontos exatos que precisam ser resolvidos.

## 6. Validação manual necessária e economia de consultas

O Gemini não executa Log Explorer de modo confiável. **Não pedir novamente verificações já realizadas em UAT.**

Gerar **no máximo cinco consultas/validações prioritárias de PROD** para o usuário, agrupando verificações por atributo/serviço de forma a cobrir o maior número de widgets. Para cada uma, registrar exclusivamente no relatório privado query literal, env e serviço reais, janela indicada, campos esperados e o efeito da evidência na classificação dos widgets.

Distinguir:
- prova de presença de atributo numa ocorrência;
- prova de cobertura de todos os eventos de que uma visualização depende;
- ausência de tráfego e ausência real de instrumentação.

Se já houver evidências privadas confiáveis de PROD, **reutilizá-las** em vez de exigir execução duplicada. A falta de prova deve gerar **status de pendência**, não alegação de ausência.

## 7. Testes locais e critérios de aceite

Rodar somente validações comprovadamente offline/read-only:
- JSON e schema válidos; dashboard novo sem copiar identificador administrativo de UAT;
- grupos/widgets de dados com a **mesma contagem** que a origem UAT, salvo diferença técnica obrigatória e documentada; mesmo layout e ordem;
- títulos/descrições preservados, exceto avisos justificados;
- queries e fontes adaptadas sem perda de semântica, com `env` e indices efetivos de PROD;
- nenhuma dependência silenciosa de ambiente UAT;
- diff semântico UAT × candidato PROD com justificativa **campo a campo** para transformações de ambiente, identidade da cópia e avisos;
- matriz de elegibilidade/status coerente com os avisos realmente incluídos;
- preservação do canônico UAT, do dashboard remoto UAT e de qualquer dashboard PROD preexistente;
- **zero PUT/POST/PATCH/DELETE remoto, zero alterações em código/Git/scripts**.

Se alguma validação não puder ser feita, registrar **NO-GO para publicação** e a pré-condição específica. Não executar scripts de publicação nesta etapa.

## 8. Artefatos privados e parada

Criar em `darwin-monitor/planos/` relatório novo de prefixo **35-**, com nome semelhante a `35-preparacao-clone-completo-prod-e-pendencias-instrumentacao.md`. Confirmar antes o prefixo disponível e não sobrescrever relatórios existentes.

Entregar:
1. **JSON candidato PROD integral local** e checksum;
2. **snapshot UAT base** e, se houver, snapshot de dashboard PROD preexistente;
3. **matriz de todos os widgets**, dependências, prova de branch principal, release e telemetria PROD;
4. enumeração de widgets **operacionais**, **aguardando implantação** e **pendentes de comprovação**, com títulos/descrições propostos;
5. pacote de até cinco verificações humanas em PROD, apenas se necessário;
6. diff semântico e resultados de teste seguro;
7. **relatório 35**, com paths reais, conclusões, riscos, bloqueios e recomendação: **PRONTO PARA REVISÃO / PREPARAÇÃO BLOQUEADA**.

No resumo final mostrar as contagens reais de widgets em cada estado, status da instrumentação por aplicação, se a cópia completa está estruturalmente pronta e quais condições faltam para o próximo gate.

**PARE. NÃO PUBLICAR EM PRODUÇÃO NESTA ETAPA.** Após revisão do resultado, o responsável poderá autorizar especificamente a **criação de uma nova cópia em PROD** e exigir GET pós-publicação, conferência de títulos/avisos e verificação visual, além de decidir sobre remoção futura dos avisos quando houver implantação e telemetria comprovadas.
