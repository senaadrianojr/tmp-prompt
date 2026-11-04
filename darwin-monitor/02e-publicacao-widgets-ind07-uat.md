# Darwin Monitor — Prompt 02E: Publicar widgets de Origem já preparados no Datadog UAT

## Autorização e escopo

O responsável pelo projeto confirmou expressamente que **não houve nenhuma alteração manual nem de terceiros no dashboard UAT desde as atualizações feitas pela ferramenta Python existente**. Todas as mudanças anteriores foram realizadas pelo próprio fluxo automatizado do Gemini, e o responsável apenas visualizou e validou os resultados.

Por isso, **fica autorizada a execução da publicação já preparada no Prompt 02D**, sem exigir uma nova exportação ou comparação remota prévia. Essa dispensa é específica para esta execução, baseada nessa confirmação; não elimina a necessidade de backup, verificação do arquivo e conferência posterior.

**Sua missão é PUBLICAR, não reinvestigar ou reconstruir.** Alterações autorizadas exclusivamente nos widgets de Origem **1.1 e 1.2**, do indicador **IND-07**, no dashboard **[UAT] Darwin Monitor**.

## 1. Arquivos existentes

Na pasta raiz de `darwin-monitor`, localize os caminhos reais dos artefatos já preparados:

- `uat_darwin_monitor_dashboard.json` — definição consolidada do dashboard.
- `update_uat_dashboard.py` — script existente que faz a publicação por meio da sessão Datadog já autorizada.

Consulte também `docs/darwin-monitor/planos/05-investigacao-ind07-origem-venda-digital.md` apenas para conferir os objetivos e registrar o resultado. Não recrie arquivos, não modifique aplicações Java e não reescreva o script por conveniência.

## 2. Preflight local enxuto e obrigatório

Antes de executar o PUT:

1. Confirme que o script carrega o JSON correto, aponta **exclusivamente para o dashboard UAT pretendido** e preserva integralmente a definição do dashboard; não exiba cookies, CSRF ou cabeçalhos de autenticação.
2. Confirme que o JSON é sintaticamente válido e contém os widgets 1.1 e 1.2 corrigidos, com **Logs**, filtro de contratação baseado no MDC e agrupamento em `@mdc.processadora`, usando a estrutura aceita pelo schema do Datadog.
3. Confirme que os demais widgets, grupos e template variables permanecem presentes e sem mudanças semânticas em relação ao backup local da etapa 02D.
4. Confirme que existe um backup local utilizável da definição anterior. Se não existir, crie-o antes de publicar.
5. Se houver divergência inesperada nos arquivos, erro de parse, dashboard-alvo incorreto, perda de widget não-alvo ou script apontando para produção, **pare e explique**; não publique.

**Não repita a exigência de obter a definição remota atual:** o responsável confirmou que nenhuma outra pessoa modificou o dashboard. É uma decisão de risco conscientemente autorizada para esta publicação em UAT.

## 3. Publicação autorizada

Se o preflight passar:

1. Execute o script `update_uat_dashboard.py` **uma única vez**, com a autenticação já configurada e autorizada no workspace.
2. Registre apenas o status da operação e erros sanitizados. Nunca exiba nem persista cookies, tokens ou payloads contendo informação sensível.
3. Caso a resposta falhe, **não faça novas tentativas especulativas nem altere as queries em loop**. Registre o problema e pare.
4. Se o script retornar sucesso, confirme que o dashboard foi atualizado, preferencialmente por leitura autorizada após o PUT. Se essa leitura não estiver disponível, deixe claramente registrado que houve sucesso reportado pela API, mas **sem confirmação independente de persistência**.
5. Não modifique outros widgets; não crie monitores nem altere ambientes diferentes de UAT.

## 4. Validação do que foi publicado

Os dois widgets devem medir **eventos de contratação observados por processadora**, não contratos efetivados.

Fonte e filtro já validados manualmente no Log Explorer:

```text
service:springboot-pseg-base-venda-digital env:uat @mdc.tipo_evento:Contratacao @mdc.processadora:*
```

Agregação: **Count** agrupado por **`@mdc.processadora`** com a política de Flex Logs compatível.

- **Widget 1.1:** distribuição por processadora, preferencialmente Top List.
- **Widget 1.2:** evolução temporal por processadora, Timeseries.
- Preserve **BANKLY**, **DXC** e quaisquer outros valores realmente encontrados; **PISMO pode estar ausente**, pois o tráfego atual é majoritariamente legado e Pismo atua como fallback.
- Não use valores fixos de processadora nos filtros.
- Não exija reproduzir 10 BANKLY e 2 DXC: esses números foram observados **numa janela anterior de 1 hora** e variam com o tráfego.

Se tiver acesso autorizado à visualização, confira os dois widgets. Se não puder abrir a UI, registre **VALIDAÇÃO VISUAL PENDENTE PELO RESPONSÁVEL** e indique exatamente como comparar com o Log Explorer na **mesma janela de tempo**, com os mesmos filtros e armazenamento. Não declare validação visual por inferência.

## 5. Documentação breve

Atualize `docs/darwin-monitor/planos/05-investigacao-ind07-origem-venda-digital.md` adicionando:
- resultado do preflight;
- publicação executada **SIM/NÃO**, código de status e evidência de resposta;
- confirmação posterior de persistência **SIM/NÃO/NÃO POSSÍVEL**;
- visualização **VALIDADA/PENDENTE/FALHOU**;
- confirmação de que não houve mudanças intencionais nos widgets não-alvo;
- cobertura de negócio **PARCIAL**, pois os logs podem representar tentativas/eventos, e não contratos efetivados.

Não sobreescreva as evidências anteriores do código e da validação manual.

## 6. Resposta final curta

Relate:

1. Publicação do UAT: SIM/NÃO;
2. Resultado da API e confirmação posterior;
3. Widgets 1.1 e 1.2: alterados/publicados ou não;
4. Demais widgets: preservados conforme preflight e conferência possível;
5. Validação na interface: realizada ou pendente;
6. Um único próximo passo.

**Execute a publicação autorizada e encerre. Não avance ao IND-01 nem faça outras alterações.**
