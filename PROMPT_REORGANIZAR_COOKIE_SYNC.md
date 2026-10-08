Quero reorganizar a estrutura local do projeto de sincronizacao de credenciais antes de executar o prompt de criacao do runbook/documentacao.

IMPORTANTE:
- Nao altere a logica funcional da extensao.
- Nao altere o contrato HTTP entre extensao e servidor.
- Nao altere cookies, metadata ou credenciais existentes.
- Nao apague a pasta legada servicenow-cookie-sync.
- Nao crie o GEMINI_PLATFORM_ACCESS_RUNBOOK.md nesta etapa.
- Nao inclua credenciais reais em logs ou documentacao.

Estado atual dentro de %USERPROFILE%\Tools:

bv-cookie-sync\
gemini-auth-sync\
servicenow-cookie-sync\
...outras pastas...

Objetivo final:

%USERPROFILE%\Tools\cookie-sync\
  bv-cookie-sync\
  gemini-auth-sync\

A pasta servicenow-cookie-sync deve permanecer onde esta por enquanto, sem alteracoes.

1. AJUSTAR O SERVIDOR PARA SER PORTAVEL

No arquivo canonico:

%USERPROFILE%\Tools\gemini-auth-sync\local_sync_server.py

remova a dependencia de um caminho fixo como:

Path.home() / "Tools" / "gemini-auth-sync"

e passe a resolver o diretorio base a partir da propria localizacao do arquivo:

BASE_DIR = Path(__file__).resolve().parent
CREDENTIALS_DIR = BASE_DIR / "credentials"

A ideia e permitir mover a pasta gemini-auth-sync para outro diretorio sem precisar alterar o codigo novamente.

Revise o restante do arquivo para garantir que todos os caminhos derivados continuem usando BASE_DIR/CREDENTIALS_DIR corretamente.

Nao altere:
- porta
- endpoint
- SOURCES
- credential types
- metadata
- freshness
- CORS
- X-Sync-Token
- formato dos payloads
- comportamento de escrita

2. ATUALIZAR DOCUMENTACAO TECNICA EXISTENTE

Atualize somente referencias de caminho no README.md do gemini-auth-sync, se necessario, para deixar claro que:

- a pasta gemini-auth-sync pode ser movida
- o servidor resolve sua base pelo proprio __file__
- credentials/ fica sempre relativo ao diretorio do servidor

Nao crie ainda o novo runbook operacional.

3. REORGANIZAR AS PASTAS

Depois de ajustar e validar o servidor, reorganize:

%USERPROFILE%\Tools\bv-cookie-sync
->
%USERPROFILE%\Tools\cookie-sync\bv-cookie-sync

e:

%USERPROFILE%\Tools\gemini-auth-sync
->
%USERPROFILE%\Tools\cookie-sync\gemini-auth-sync

Crie a pasta cookie-sync se ela ainda nao existir.

Nao mover:
%USERPROFILE%\Tools\servicenow-cookie-sync

Nao mover outras pastas de Tools.

4. VALIDACOES APOS A MUDANCA

Confirme que a estrutura final ficou:

%USERPROFILE%\Tools\cookie-sync\
  bv-cookie-sync\
  gemini-auth-sync\
    local_sync_server.py
    README.md
    credentials\

Valide estaticamente que:

- local_sync_server.py continua resolvendo credentials/ dentro da propria pasta gemini-auth-sync
- nenhuma referencia funcional ainda depende de %USERPROFILE%\Tools\gemini-auth-sync
- a extensao continua apontando para http://127.0.0.1:9876/sync
- nenhum arquivo da extensao depende de caminho absoluto para a pasta antiga
- nenhum arquivo de credencial foi apagado ou alterado

5. EXTENSAO NO CHROME

Nao tente reinstalar ou recarregar a extensao automaticamente.

Ao final, apenas me informe que, por ela ter sido movida de pasta, eu devo abrir:

chrome://extensions

remover/recarregar a extensao unpacked usando a nova pasta:

%USERPROFILE%\Tools\cookie-sync\bv-cookie-sync

se o Chrome deixar de reconhecer o caminho anterior.

6. TESTE FINAL

Nao use credenciais reais em testes automatizados.

Se for possivel, faca apenas uma validacao local/estatica de startup do Python e dos caminhos.

Se detectar qualquer risco de sobrescrever credenciais existentes, pare antes da operacao destrutiva e me informe.

Ao concluir, apresente:

1. arquivos alterados
2. pastas movidas
3. estrutura final
4. confirmacao de que BASE_DIR agora usa __file__
5. confirmacao de que credentials/ continua relativo ao servidor
6. qualquer referencia antiga que ainda precise ser corrigida
7. instrucoes curtas para recarregar a extensao no Chrome
8. confirmacao de que servicenow-cookie-sync permaneceu intacta
