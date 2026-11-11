# PROMPT 05H — Publicação Controlada em UAT e Verificação Pós-Publicação (Candidato 05G)

> **Executor:** Gemini CLI, exclusivamente no workspace corporativo autorizado.
>
> **ATENÇÃO — REPOSITÓRIO PÚBLICO:** este arquivo contém **somente instruções sanitizadas**. Não publicar aqui dashboard JSON, relatórios privados, IDs de dashboard/widget, nomes de serviços, consultas, eventos, nomes de organizações, métricas, payloads, capturas, tokens, cookies, códigos internos ou identificadores de clientes.
>
> **AUTORIZAÇÃO HUMANA EXPRESSA E ESPECÍFICA JÁ CONCEDIDA EM 11/11/2026:** o responsável técnico autorizou a **publicação controlada em UAT do candidato final da etapa 05G, restrita ao único widget alvo descrito no Relatório privado 28**. Essa autorização **não abrange** produção, outros widgets, protótipos isolados, modificação automática do canônico, Java, scripts, pipeline, infraestrutura ou Git. **Não solicitar a mesma autorização novamente se todas as pré-condições forem cumpridas.** Se alguma condição falhar, PARE sem escrita remota e devolva a decisão ao responsável.

## 1. Missão e documentos privados de referência

Executar um único ciclo controlado: **GET remoto atualizado → guarda de concorrência e comparação com estado aprovado → backup imutável → preparação do payload estritamente equivalente ao diff aprovado → dry-run seguro → uma publicação em UAT → GET e verificação semântica pós-PUT → relatório privado → parada**.

Ler **apenas** o indispensável no workspace privado:
1. Relatório técnico final da etapa **05G**, arquivo com prefixo `28-` em `darwin-monitor/planos/`;
2. JSON candidato final reconciliado da etapa **05G**, identificado pelo Relatório 28 (não confundir com versões 05E e 05F);
3. Snapshot/backup remoto da etapa 05G e relatório anterior quando necessários à guarda de concorrência;
4. Script local de publicação e suas opções, **somente para análise e execução segura**, sem editá-lo.

Obter **exclusivamente de fontes privadas locais** os identificadores exatos do **dashboard UAT alvo** e do **único widget** autorizado. Conferir identidade, ambiente e organização de destino. Se houver ambiguidade, interromper. Nunca inventar identificadores nem extraí-los do repositório público de prompts.

**Não reabrir auditorias, nem reconsultar Log Explorer, nem exigir novos prints.** As duas consultas do fluxo principal e de controle foram validadas pelo operador em UAT nas etapas anteriores. Não alterar a query aprovada por conveniência.

## 2. Escopo exato da escrita autorizada

- O único alvo alterável é o widget já delimitado na etapa 05G.
- Em relação ao **estado remoto atual**, o diff intencional aprovado do widget envolve **somente `definition.title`, `definition.description` e a query de busca correspondente** conforme a estrutura real do JSON. Não mudar `id`, tipo, grupo, layout, agregação, variáveis, visualização, precisão, escala, time range, ordenação, qualquer outro campo ou widget.
- **Nenhuma** integração de protótipos de retentativas ou sucessos locais.
- **Nenhuma** alteração no arquivo canônico local, nos candidatos históricos, scripts, aplicações, testes, pipelines, Git ou produção.
- Quando a API do Datadog exigir `PUT` do **dashboard inteiro**, isso **não amplia a autorização**: construir o corpo a partir do **GET remoto mais recente**, aplicando **exclusivamente as três alterações permitidas no widget alvo**. Não enviar como substituição direta uma versão antiga do dashboard, nem depender de um `PUT` integral que possa reverter edições manuais. Só enviar quando o diff semântico do payload contra a versão remota recém-lida provar que apenas o widget autorizado difere.

## 3. Pré-flight obrigatório, antes de qualquer PUT

1. Fazer **GET autenticado autorizado** do dashboard UAT. Registrar timestamp de leitura, ID, ambiente e metadados de alteração (ex.: `modified_at`, quando fornecido) somente no relatório privado.
2. Comparar o estado atual com o **snapshot remoto validado na 05G**, distinguindo campos persistentes de metadados puramente transitórios conhecidos. Se **qualquer** alteração legítima, manual ou imprevista surgiu desde o snapshot, **NÃO PUBLICAR**. Criar snapshot novo e relatório do conflito, indicar os paths exatos e retornar para reconciliação/revisão humana.
3. Salvar um **novo backup local, imutável e identificável por data/hora** do remoto lido agora. Calcular e registrar checksum do backup e do candidato, sem copiar conteúdo sensível para o repositório público.
4. Confirmar que o candidato 05G tem integridade e estrutura esperadas, que o widget alvo está presente e que não há IDs duplicados. Validar organização/destino e variáveis de template utilizadas.
5. Gerar um **payload temporário em memória ou arquivo temporário local privado** a partir do GET remoto mais recente com somente a substituição dos três atributos autorizados. Fazer diff semântico remoto × payload e payload × candidato 05G, justificando diferenças de representação inevitáveis da API. **Aceite: somente um widget modificado, apenas os três campos permitidos; todos os demais campos persistentes preservados**. Se não for possível provar, **STOP/NO-GO**.
6. Verificar **antes** o comportamento efetivo de quaisquer scripts: o modo dry-run não pode chamar métodos de escrita, alterar canônico, disparar deploy, nem ocultar modificações. Rodar validação sintática e dry-run offline apenas se comprovadamente seguros. Não alterar os scripts para contornar verificações.
7. Executar **GET adicional imediatamente antes do PUT** e comparar o estado persistente/versão/metadados com aquele usado para formar o payload. Se houver diferença, **STOP**. Se o Datadog/API disponibilizar mecanismo de concorrência otimista (`ETag`/`If-Match` ou equivalente), utilizá-lo **somente quando suportado e comprovado**. Na ausência de suporte, não fingir exclusão mútua: minimizar o intervalo entre GET e PUT e registrar o risco residual.
8. Assegurar que credenciais/autenticação já estejam disponíveis de forma autorizada. **Não** tentar bypass de autenticação, desabilitar TLS, buscar cookies fora das rotas aprovadas nem imprimir segredos.

**A autorização existente vale apenas quando TODOS os checks acima passarem.** Se algum falhar, o procedimento termina sem PUT e com relatório de bloqueio, sem pedir autorização genérica para “forçar”.

## 4. Publicação remota controlada — somente UAT

Se, e somente se, todos os gates forem aprovados:

1. Exibir no terminal uma **prévia sanitizada** com destino UAT, widget autorizado, número real de widgets preservados, checksum do snapshot, número de campos propostos e status do dry-run.
2. Executar **uma única operação de escrita** usando o caminho oficial já autorizado e validado para a API do dashboard (preferir o script corporativo existente se houver garantia de que ele não substitui outros dados e não atualiza o canônico; caso contrário, abortar e informar o bloqueio). A escrita deve atingir **exclusivamente o dashboard UAT e o diff aprovado**.
3. Registrar no workspace privado horário, status HTTP/resultado do comando e identificação da operação, **sem armazenar tokens ou conteúdo confidencial em lugares públicos**.
4. Em timeout, erro 5xx ou retorno ambíguo, **não repetir o PUT automaticamente**. Fazer GET para identificar se a alteração foi aplicada, se houve divergência ou se o estado é indeterminado; classificar e interromper sem nova escrita. Não tentar “corrigir” indiscriminadamente.

**Não execute rollback automático não autorizado:** se o estado posterior divergir do previsto, preserve backup, diffs e diagnóstico e solicite decisão específica para qualquer nova escrita de recuperação.

## 5. Verificação obrigatória pós-publicação

Após a única escrita autorizada:
- Fazer novo **GET** do dashboard UAT.
- Comparar semanticamente o remoto depois do PUT com o payload esperado, a partir do snapshot imediatamente anterior. Validar que o alvo contém **exatamente a query, título e descrição aprovados no candidato 05G**.
- Verificar presença, IDs e conteúdo funcional de **todos os demais widgets**, grupos, layouts, notas, filtros, variáveis e propriedades remotas manuais previamente preservadas; não assumir números históricos se os dados atuais forem diferentes.
- Distinguir normalizações automáticas do servidor de mudanças funcionais; documentar qualquer diferença inesperada e classificar como **SUCESSO**, **PARCIAL/DIVERGENTE**, **FALHA** ou **INDETERMINADO**. Em caso de diferença não esperada, não declarar sucesso.
- Registrar necessidade de **validação visual do operador** na UI do Datadog. Confirmação técnica por GET **não substitui** a verificação visual final.
- **Não** atualizar automaticamente o JSON canônico local após o PUT, mesmo com sucesso. Qualquer sincronização do canônico é uma decisão separada.
- Não publicar em produção nem iniciar um segundo ciclo de ajustes.

## 6. Entrega local e relato ao responsável

Criar somente **novos arquivos locais privados** para execução/auditoria, sem sobrescrever relatórios 22–28 ou candidatos 05E/05F/05G:
- Novo relatório conciso em `darwin-monitor/planos/`, com prefixo **29-** (exemplo `29-publicacao-controlada-uat-ind03.md`), com resultados **factuais**, não presumidos;
- Snapshot pré-PUT imutável, checksum e caminhos;
- Caso o procedimento tenha sido publicado, snapshot pós-PUT, diff/resultado de verificação e registro de resposta;
- Se bloqueado, relatório de **NO-GO** apontando qual pré-condição falhou; nenhum PUT é permitido nesse caso.

Relatório mínimo:
1. Escopo de autorização e ambiente/destino reais;
2. Snapshot anterior, último GET, metadados, checksums e guarda de concorrência;
3. Diff semântico **remoto inicial × payload autorizado** (paths modificados apenas do widget alvo);
4. Resultado dos testes e validações seguras;
5. Se houve PUT: horário, status, resposta, GET posterior e diff semântico **antes/depois**, com distinção entre sucesso técnico e validação visual;
6. Preservação dos demais widgets e configurações;
7. Exceções, bloqueios, riscos e ações pendentes;
8. Lista exata dos arquivos locais criados, declaração sobre canônico/produção/código/versões históricas **intactos**.

## 7. Parada obrigatória

Informar no terminal, objetivamente: **PUBLICADO E VERIFICADO POR GET / NÃO PUBLICADO (NO-GO) / RESULTADO INDETERMINADO**, dashboard UAT e widget alvo reais (somente no terminal privado), paths do relatório/snapshots, divergências detectadas e o que o operador deve conferir visualmente.

**PARE.** Esta autorização **não é** permissão para modificar um segundo widget, alterar o canônico, publicar em produção, executar novo PUT para reparo, incorporar protótipos ou iniciar trabalhos fora do recorte aprovado.
