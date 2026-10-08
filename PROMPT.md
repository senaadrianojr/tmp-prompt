Crie uma nova documentação operacional para uso pelo agente de IA Gemini CLI, consolidando a documentação legada de captura/uso de cookies com a arquitetura atual do gemini-auth-sync.

IMPORTANTE:
- Nesta etapa, não altere código Python nem arquivos da extensão.
- Crie apenas o novo guia.
- Não inclua cookies, tokens, API keys, senhas ou qualquer valor real de credencial.
- Exemplos devem usar placeholders fictícios.
- A documentação legada é referência, mas o código atual é a fonte de verdade quando houver divergência.

Fontes que devem ser lidas antes de escrever:
1. O guia legado GUIA_CAPTURA_E_USO_COOKIES.md, se estiver disponível no ambiente.
2. O servidor canônico atual em:
   %USERPROFILE%\Tools\gemini-auth-sync\local_sync_server.py
3. O README atual em:
   %USERPROFILE%\Tools\gemini-auth-sync\README.md
4. Os arquivos atuais da extensão do navegador, apenas para confirmar o comportamento real de sincronização manual/automática e não documentar algo que ainda não esteja implementado.

Crie o arquivo:

%USERPROFILE%\Tools\gemini-auth-sync\GEMINI_PLATFORM_ACCESS_RUNBOOK.md

Objetivo do arquivo:
Ser um runbook orientado ao agente Gemini CLI para que ele saiba preparar autenticação local, validar sessão e consultar plataformas web autenticadas quando eu solicitar tarefas como análise de Datadog, ServiceNow ou outras fontes suportadas.

O guia não deve ser apenas um tutorial para humanos copiarem cookies. Ele deve explicar ao agente COMO agir.

Estrutura mínima esperada:

1. Objetivo e escopo
- Explicar que gemini-auth-sync disponibiliza credenciais de sessão locais para automações pontuais.
- Separar claramente captura/sincronização de credenciais do uso dessas credenciais pelos agentes.

2. Arquitetura atual
Documentar a arquitetura realmente implementada:
Navegador/extensão -> servidor local -> credentials/<source>/ -> Gemini CLI.

Confirmar no código os detalhes reais antes de documentar, incluindo:
- endereço/porta do servidor
- endpoint de sincronização
- diretório base dinâmico
- sources suportadas
- credential_types suportados
- metadata.json
- política de freshness
- sincronização manual/automática atualmente disponível

Não documentar funcionalidades planejadas como se já estivessem implementadas.

3. Procedimento padrão do agente
Definir um fluxo operacional semelhante a:
- identificar qual plataforma será usada
- verificar se o servidor local está ativo
- iniciar o servidor Python quando necessário
- localizar metadata.json da source
- avaliar freshness
- se a credencial estiver ausente ou stale, aguardar/solicitar nova sincronização pelo mecanismo disponível
- nunca inventar credenciais
- ler a credencial somente quando necessário
- validar a sessão na plataforma antes da tarefa principal
- executar a consulta solicitada
- tratar expiração/autenticação inválida de forma clara

Incluir os comandos adequados para iniciar o servidor no Windows/CMD, usando caminhos dinâmicos e sem nome de usuário hardcoded.

4. Estrutura de arquivos
Documentar a estrutura atual de:
Tools/gemini-auth-sync/
  local_sync_server.py
  README.md
  GEMINI_PLATFORM_ACCESS_RUNBOOK.md
  credentials/
    <source>/
      cookies.txt
      token.txt
      api_key.txt
      metadata.json

Deixar claro que arquivos de credencial só existem quando aquele tipo tiver sido sincronizado, se esse for o comportamento atual.

5. metadata.json e freshness
Explicar:
- updated_at
- refresh_after_seconds
- cookie_count
- diferença entre freshness operacional e expiração real da sessão
- como o agente deve decidir se pode reutilizar uma credencial ou se deve solicitar nova sincronização

6. Regras gerais de segurança
Manter simples e práticas:
- nunca imprimir, ecoar ou registrar credenciais
- nunca incluir credenciais em relatórios
- nunca commitar credentials/
- nunca copiar valores para documentação
- logout pode invalidar a sessão
- usar apenas a sessão do usuário autenticado e suas permissões existentes
- preferir cadeia de certificados corporativa quando necessária
- não usar verify=False como padrão; tratar apenas como fallback diagnóstico quando inevitável

7. Datadog
Consolidar as informações úteis do guia legado e validar o que ainda se aplica.

Documentar:
- como localizar os cookies atuais na nova estrutura
- como validar a sessão
- quando um CSRF token adicional é necessário
- como obtê-lo a partir da sessão autenticada, quando aplicável
- diferença entre consultas de leitura e operações de alteração
- headers adicionais necessários para operações mutáveis, quando aplicável
- troubleshooting de 401/403

Não colocar valores reais de cookies/tokens.

8. ServiceNow
Documentar:
- localização da credencial
- validação de sessão
- uso de g_ck / X-UserToken quando necessário
- diferença entre leitura e operações que alteram dados
- troubleshooting de 401/403/redirect

9. Meu Acesso / SailPoint
Documentar:
- localização da credencial
- forma de validar sessão
- detecção de redirect/login
- principais sinais de sessão expirada

10. Jira / Confluence
Se aparecerem apenas na documentação legada e ainda não estiverem integrados ao gemini-auth-sync:
- manter como seção de integração futura/referência
- não afirmar que a extensão já captura essas credenciais
- registrar somente o conhecimento útil para futura integração

11. Troubleshooting
Consolidar os casos úteis do guia legado, incluindo:
- HTTP 401
- HTTP 403 por ausência de token complementar
- redirect 302/login
- certificado/proxy corporativo
- servidor local indisponível
- arquivo de credencial ausente
- metadata stale

Remover caminhos hardcoded e referências ao ambiente pessoal de outro desenvolvedor.

12. Regra de leitura x alteração
Definir explicitamente:
- por padrão, o agente deve operar em modo de leitura/análise
- consultar dados, logs, traces, métricas, dashboards existentes e configurações é permitido quando necessário à análise
- criar, alterar ou excluir dashboards, monitores, tickets, configurações ou dados externos somente quando o usuário pedir explicitamente

13. Workflow específico para análise no Datadog
Adicionar uma seção orientada a tarefas como:

"Verifique no Datadog como montar o dashboard X com base no relatório Y e gere uma documentação da solução."

O agente deve:
1. Ler primeiro o relatório Y.
2. Extrair indicadores e evidências relevantes, como:
   - service
   - resource
   - endpoint
   - tags
   - IDs/correlação
   - status
   - critérios de sucesso/falha
3. Preparar/validar autenticação do Datadog conforme o runbook.
4. Consultar somente os dados necessários.
5. Correlacionar o conteúdo real do Datadog com o relatório.
6. Para cada indicador, propor quando aplicável:
   - tipo de widget
   - fonte: APM, traces, logs ou metrics
   - query
   - filtros
   - group by
   - agregação
   - janela temporal
   - unidade/formatação
   - critério de sucesso/erro
   - threshold
   - monitor associado
   - justificativa técnica
7. Distinguir claramente:
   - evidência observada
   - inferência
   - recomendação
8. Não criar o dashboard/monitor sem autorização explícita.

14. Qualidade da documentação
- Escrever em português claro e técnico.
- Priorizar instruções operacionais para o agente.
- Evitar texto genérico.
- Usar exemplos fictícios.
- Não reproduzir caminhos antigos de outros usuários.
- Não incluir qualquer credencial real.
- Se houver informação legada duvidosa ou incompatível com o código atual, marcar como legado/depreciado em vez de copiá-la como verdade atual.

Ao concluir:
1. informe o caminho do arquivo criado
2. resuma quais partes do guia legado foram mantidas, atualizadas ou descartadas
3. liste qualquer ponto que não pôde ser confirmado no código atual
4. não altere nenhum outro arquivo sem minha autorização.
