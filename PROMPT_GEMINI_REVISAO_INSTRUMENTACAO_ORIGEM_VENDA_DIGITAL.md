Revise e conclua a instrumentação do indicador “Origem” no projeto venda-digital, corrigindo os problemas da implementação atual e preservando os comportamentos existentes.

CONTEXTO

Uma tentativa anterior iniciou essa instrumentação, mas quebrou testes existentes e passou a tentar comentá-los ou alterá-los sucessivamente para contornar as falhas.

Esta é uma nova janela de contexto. Não presuma que as decisões da execução anterior estavam corretas. Inspecione o código, o diff e os testes antes de continuar.

Não é necessário desfazer toda a instrumentação. Preserve o que estiver correto e corrija pontualmente o que estiver problemático.

Durante a implementação foi criado o atributo canalVenda porque canalOrigem já existia e possui outra finalidade. Essa distinção é obrigatória: não reutilize, sobrescreva ou altere a semântica de canalOrigem para implementar este indicador.

OBJETIVO FUNCIONAL

Disponibilizar o canal da venda como atributo estruturado no log de sucesso da contratação, permitindo segmentar esse evento no Datadog.

A implementação deve aproveitar o fluxo de logging existente, preservar os campos atuais e evitar eventos duplicados. Não crie métricas customizadas, dashboards, monitores ou integrações novas.

O documento de referência aponta:
- LogRequest, no módulo domain, como objeto do payload.
- LogMapper como responsável pelo preenchimento.
- VendaDigitalService, no módulo application, como um dos pontos de registro do sucesso da contratação.

Localize essas classes no projeto e confirme o fluxo real. Os caminhos, números de linha, nomes de headers e exemplos de MDC do documento são referências ilustrativas, não evidências de como a aplicação funciona.

1. DIAGNÓSTICO ANTES DAS ALTERAÇÕES

Leia as instruções do repositório e identifique os comandos oficiais de build e testes.

Inspecione:
- Estado do Git, alterações locais e commits relevantes da instrumentação.
- Introdução e utilização de canalVenda.
- Finalidade e consumidores de canalOrigem.
- Entrada da contratação, DTOs, serviços, mappers e saída efetiva do log.
- Assinaturas, construtores, builders, código gerado e mocks afetados.
- Testes alterados, comentados, removidos, ignorados ou enfraquecidos na tentativa anterior.
- Configuração de serialização e logging usada pela aplicação.

Execute os testes relevantes antes de editar e registre as falhas iniciais. Diferencie falha de compilação, regressão funcional, incompatibilidade de mock e problema de ambiente.

Essa execução representa o estado atual, que já pode conter regressões. Não a trate automaticamente como uma baseline saudável. Quando necessário, consulte a versão anterior à instrumentação por meio do histórico, sem sobrescrever o trabalho atual.

Antes de modificar código, apresente um diagnóstico breve com evidências e o plano de correção. Em seguida, prossiga com a implementação sem aguardar confirmação para correções dentro deste escopo.

2. CONTRATO DE canalVenda

Rastreie e documente o caminho real do valor:
entrada → DTO/contexto → serviço → mapper → payload/log.

Confirme:
- De onde o valor vem.
- Quais entradas e fluxos precisam recebê-lo.
- Qual seu tipo e quais valores são aceitos conforme os contratos existentes.
- Como chamadas antigas se comportam quando o atributo não é informado.
- Qual chave efetivamente aparece no JSON final.

O documento original usa canal_origem no exemplo de telemetria, mas a implementação passou a utilizar canalVenda para separar conceitos. Não confunda o nome Java, a chave serializada do novo indicador e o atributo preexistente canalOrigem.

Verifique o contrato já implementado e suas anotações de serialização. Mantenha uma única chave coerente para o indicador e informe explicitamente o nome final. Não crie aliases, campos duplicados ou renomeações adicionais sem necessidade comprovada.

Não invente um header, canal padrão ou origem do dado. APP_Varejo e X-Canal-Origem são exemplos do documento, não contratos confirmados.

Se a aplicação não fornecer informação suficiente para determinar a fonte real, avance nas correções independentes e reporte a lacuna específica. Não atribua um canal fictício para aparentar que a instrumentação está completa.

3. IMPLEMENTAÇÃO MÍNIMA E COMPATÍVEL

Faça a menor alteração coerente que propague canalVenda até o evento de sucesso.

Preserve:
- Semântica de canalOrigem.
- Regras e resultados da contratação.
- Campos existentes do log.
- Comportamento das chamadas que não enviam canalVenda.
- Contratos externos não relacionados ao novo atributo.

O novo campo de telemetria não deve tornar obrigatória uma informação opcional para consumidores antigos, nem provocar falha de contratação quando estiver ausente.

Analise todos os pontos de chamada antes de alterar assinaturas. Evite espalhar mudanças desnecessárias por serviços e testes.

Não adicione sobrecargas ou caminhos alternativos apenas para contornar testes quebrados. Se forem necessários por compatibilidade real, justifique e verifique sua delegação.

Não introduza MDC automaticamente. Primeiro confirme a arquitetura existente. Se MDC for necessário, garanta limpeza/restauração do contexto e isolamento entre requisições; examine também propagação assíncrona, se houver. Não use estado global mutável para armazenar o canal.

Confirme que o evento representa sucesso real segundo o fluxo existente, sem duplicar contagens nem registrar sucesso antes da confirmação correspondente.

Não amplie o payload com dados pessoais, credenciais ou conteúdo integral da requisição.

4. REGRAS OBRIGATÓRIAS PARA OS TESTES

É proibido fazer a suíte passar por meio de:
- Comentários, exclusão ou desativação de testes.
- @Disabled, @Ignore, exclusões de execução ou filtros que ocultem regressões.
- Remoção ou enfraquecimento de assertions relevantes.
- Captura de exceções ou retornos artificiais para esconder erros.
- Configuração global permissiva de mocks para mascarar incompatibilidades.
- Atualização indiscriminada de expectativas para reproduzir um comportamento incorreto.

Revise as alterações anteriores nos testes. Restaure a cobertura indevidamente removida ou desativada pela instrumentação, preservando mudanças legítimas e trabalho não relacionado.

Testes podem precisar de ajustes legítimos, como a atualização de um argumento de mapper ou de um fixture. Cada ajuste deve continuar verificando o comportamento original e ter uma causa concreta.

Para cada falha:
1. Leia a mensagem e a stack trace.
2. Identifique a causa raiz e a relação com o diff.
3. Corrija a produção ou o teste conforme o contrato esperado.
4. Execute novamente o teste afetado.
5. Só então amplie a execução.

Se uma correção não resolver a falha, reavalie a hipótese antes de alterar outros arquivos. Não faça correções em massa por tentativa e erro.

5. VALIDAÇÃO

Cubra, com testes pertinentes:
- Propagação correta de canalVenda até o payload.
- Preservação independente de canalOrigem.
- Compatibilidade com ausência de canalVenda e chamadas antigas.
- Serialização real da chave e do valor.
- Preservação dos campos existentes.
- Ausência de novo evento duplicado.
- Ausência de vazamento entre requisições, caso exista contexto compartilhado/MDC.

Não valide apenas o getter de LogRequest: confirme o caminho que efetivamente emite o log. Se houver um serviço intermediário de logging, identifique até onde o repositório permite comprovar a entrega do atributo.

Execute os testes focados, os dos módulos impactados e a suíte completa aplicável, além dos checks exigidos pelo projeto.

Não use skipTests ou equivalentes como evidência de validação. Se houver impedimento de ambiente, informe o comando, a causa e o que permaneceu sem verificação.

6. LIMITES E ENTREGA

Não faça reset, clean, checkout ou restauração em massa. Não descarte alterações locais, não faça refatorações fora do escopo e não altere dependências ou configurações de build apenas para silenciar falhas.

Não faça commit, push ou deploy nesta tarefa.

Ao terminar, apresente:
- Diagnóstico dos problemas encontrados.
- Correções realizadas e arquivos afetados.
- Fonte real e percurso de canalVenda.
- Nome exato do atributo no JSON final.
- Evidência de que canalOrigem manteve sua finalidade.
- Testes restaurados ou ajustados e justificativas.
- Comandos executados e resultados, incluindo falhas pendentes.
- Exemplo sanitizado do payload produzido, fiel à serialização validada.
- Procedimento de validação após deploy, usando os nomes reais dos campos.

Diferencie validação local de confirmação no Datadog. Não afirme que o dado está disponível no Datadog sem observar um evento real após deploy.

Considere a tarefa concluída quando a instrumentação estiver coerente com o fluxo real, as regressões causadas pela mudança estiverem corrigidas e a cobertura existente estiver preservada.
