# Prompt — Diagnóstico de acesso operacional ao Datadog pelo Gemini CLI

## Contexto

Ao solicitar validação real de um widget do Datadog, você declarou não ser capaz de executar o procedimento de acesso descrito no `GEMINI_PLATFORM_ACCESS_RUNBOOK.md`, embora tenha informado possuir ferramentas como `read_file` e `run_shell_command`.

Sua conclusão de incapacidade operacional **não foi demonstrada**.

Você informou possuir `run_shell_command`, portanto deve investigar a possibilidade de executar clientes HTTP locais antes de declarar que não consegue acessar o Datadog. **Não presuma que a disponibilidade de execução de shell garante acesso autenticado**: é preciso comprovar a compatibilidade do mecanismo de autenticação, as permissões e a conectividade.

## Diagnóstico solicitado

Execute o seguinte diagnóstico, registrando evidências verificáveis:

1. Verifique se `run_shell_command` está funcional e se `curl` ou Python estão disponíveis.
2. Releia o `GEMINI_PLATFORM_ACCESS_RUNBOOK.md` e identifique o mecanismo exato de autenticação previsto.
3. Verifique as configurações e eventuais restrições de acesso à rede, execução de processos, leitura dos arquivos autorizados e políticas do ambiente.
4. Diferencie autenticação de navegador/sessão web de autenticação exigida pela API oficial do Datadog. **Não assuma que cookies substituem API Key e Application Key**; determine qual fluxo o runbook efetivamente documenta.
5. Se o procedimento documentado for compatível e estiver permitido, execute uma consulta autenticada **exclusivamente de leitura**, utilizando somente os mecanismos já autorizados.
6. Nunca exponha cookies, tokens, chaves, credenciais, cabeçalhos sensíveis ou conteúdo de arquivos de autenticação na resposta, nos logs ou nos artefatos. Não introduza novos mecanismos de autenticação nem tente contornar bloqueios corporativos.
7. Caso a consulta falhe, informe o comando ou procedimento utilizado de forma sanitizada, o erro obtido, a etapa em que ocorreu e a restrição específica encontrada, omitindo informações sensíveis.
8. Se não for possível realizar o teste com segurança ou autorização, descreva com precisão o impedimento constatado e quais evidências faltam para a validação.

## Critérios de conclusão

- Não apresente limitações presumidas como limitações comprovadas.
- Diferencie claramente o que foi **lido na documentação**, o que foi **testado**, o que foi **confirmado** e o que permanece **hipótese**.
- Não declare que o widget está validado apenas com base em análise estática de código-fonte ou na existência de logs esperados.
- Não modifique arquivos, código, configurações do Datadog nem reclassifique o widget como validado sem evidências reais.
- Se houver conexão de leitura comprovada, descreva quais verificações adicionais seriam necessárias para confrontar a consulta do widget com dados reais.

## Entrega

Ao concluir, informe objetivamente:

1. **Ferramentas disponíveis e testadas**;
2. **Mecanismo de autenticação documentado e sua compatibilidade**;
3. **Resultado do teste de acesso** (com evidências não sensíveis);
4. **Possibilidade de validar o widget diretamente no Datadog**;
5. **Impedimento concreto**, caso não seja possível.

O objetivo é esclarecer se a restrição é de ferramenta, de rede, de permissão, de autenticação ou apenas uma suposição equivocada sobre suas capacidades.
