# PROMPT 05G — Correção Semântica Pontual e Gate Final do Candidato UAT

> **Execução:** Gemini CLI no workspace corporativo autorizado.
>
> **Atenção:** este repositório é público. O prompt é metodológico e sanitizado; não publicar aqui nomes de aplicações, IDs, marcadores, queries, payloads, relatórios, planilhas, JSONs, métricas, código, capturas ou dados internos.
>
> **Autorização:** apenas leitura autorizada e criação de novos artefatos **locais**. **NÃO publicar no Datadog, NÃO executar escrita remota, NÃO alterar canônico, scripts, Java, testes, infraestrutura, Git nem os protótipos isolados.**

## 1. Decisão da revisão humana

A etapa 05F foi concluída. O relatório privado de prefixo `27-` e o candidato reconciliado 05F demonstram, segundo a execução documentada:
- validação declarada pelo operador de dois filtros distintos (fluxo principal e fluxo de controle) com sinais recentes e campo de correlação;
- reconciliação do candidato local com snapshot remoto em leitura;
- preservação dos widgets fora do alvo e das alterações legítimas feitas diretamente na UI;
- execução de dry-run offline; dois protótipos permaneceram separados.

**Não repetir auditorias anteriores, não solicitar prints, não revalidar as consultas manualmente, não criar widgets nem ampliar o escopo.**

O revisor identificou uma **única inconsistência semântica material na descrição do widget principal**: a redação atual afirma que mensagens **foram efetivamente descartadas/entregues na DLQ**, mas o sinal descrito no relatório não é, por si só, um comprovante de recepção final na DLQ. É necessário adequar a **descrição ao marco de responsabilidade efetivamente observável**, sem desvalorizar a conclusão bem-sucedida de uma etapa própria do ecossistema quando essa conclusão estiver comprovada.

## 1.1. Regra vinculante — fronteira de responsabilidade e sucesso local

**A observabilidade deste projeto cobre somente as aplicações que integram o ecossistema monitorado pela equipe.** Serviços externos ou aplicações de outros times **não entram automaticamente no escopo**. Se, no futuro, o responsável ampliar essa fronteira, rever os widgets sob autorização específica; não antecipe essa ampliação.

A pergunta de cada widget é: **a aplicação sob nossa responsabilidade executou com sucesso a sua obrigação até o ponto de transferência de responsabilidade tecnicamente verificável?**

- **Sucesso local comprovado:** se a aplicação deve encaminhar/delegar um comando, evento ou mensagem e há evidência de que essa transferência foi aceita/concluída conforme o contrato de integração (por exemplo, ACK de publicação, resposta de aceite ou outro sinal inequívoco), a etapa própria pode ser classificada como **sucesso**, ainda que a execução posterior seja assíncrona e não observada.
- **Tentativa de delegação, sem confirmação:** log de intenção, acionamento do caminho de erro ou solicitação de envio, isoladamente, **não deve ser chamado de entrega confirmada**; distinguir isso de uma confirmação de transferência. Se houver comprovação em outra etapa do mesmo fluxo, usá-la.
- **Desfecho externo desconhecido:** não significa **falha** do nosso ecossistema nem exige monitoramento da outra aplicação. Registrar como fora da fronteira observada, e não como incompletude da tarefa **local**.
- **Falha funcional do fluxo observado:** esgotamento de tentativas de cancelamento continua sendo um **sinal de falha de cancelamento**, mesmo que o tratamento técnico de erro ou a delegação da mensagem tenha sido executado corretamente. Não reclassificar falha de negócio como sucesso apenas porque o encaminhamento de erro funcionou.
- **Critério de cobertura:** um widget pode cobrir **integralmente o objetivo operacional da equipe na sua fronteira de responsabilidade**, embora não demonstre o resultado final de negócio fora dela. Manter as duas avaliações separadas e não diminuir artificialmente a cobertura operacional por depender de sistemas externos.

**Não inserir ressalvas genéricas e desnecessárias em títulos e descrições.** A descrição deve declarar positivamente o evento ou marco local medido; acrescentar ressalva apenas quando necessária para evitar interpretação errada. Não exigir confirmação do processamento na aplicação de destino se o sucesso contratado da nossa aplicação se encerra na transferência comprovada.

## 2. Mudança local mínima

1. Ler seletivamente o relatório 27 e o candidato reconciliado 05F, identificando no arquivo privado o widget principal de esgotamento já aprovado quanto à query pelo operador.
2. Inspecionar a sua descrição atual e a evidência local que sustenta os termos; não alterar mensagem de log, código ou query validada.
3. Gerar **nova versão local candidata 05G**, a partir da reconciliada 05F, sem sobrescrever a anterior.
4. **Alterar exclusivamente o texto da descrição desse único widget**, usando formulação consistente com:
   - unidade observada: **ocorrências do sinal de esgotamento das tentativas do fluxo de cancelamento**;
   - descrever **positivamente o marco operacional no perímetro da equipe** (emissão/sinalização do evento de erro, NACK ou transferência de responsabilidade), com a intensidade que a evidência permitir;
   - se a transferência ao próximo componente for tecnicamente comprovada, **não negar o sucesso local** só porque o processamento posterior está fora do escopo; se houver apenas intenção de encaminhamento, não chamar de entrega confirmada;
   - não confundir sucesso do tratamento técnico de erro com sucesso do **cancelamento** em si;
   - não misturar estorno, outro fluxo, propostas únicas ou resultados financeiros não medidos.
5. Não apresentar "mensagens descartadas/entregues à DLQ", "contratos únicos" ou taxa de negócio como fatos comprovados sem evidência adequada **para esse marco específico**. Evitar também ressalvas de fim a fim que desqualifiquem indevidamente a execução local bem-sucedida. Se pertinente, deixar os limites de desfecho externo no relatório técnico, sem sobrecarregar o widget.
6. Preservar exatamente **a query, o título, o ID, agregação, apresentação, metadados, templates, grupos, posições, variáveis e todos os demais widgets** do candidato 05F. Se perceber outra mudança necessária, **não fazer**; registrar separadamente para decisão humana.

## 3. Conferência de segurança e atualização remota

- Confirmar que o candidato 05F foi baseado em snapshot remoto; verificar se existe alteração remota posterior **somente por GET autorizado**, salvando backup local novo caso haja um novo snapshot.
- Se a configuração remota mudou, **não presumir equivalência**, não usar arquivo antigo para sobrescrever edição nova: reportar divergências e, se seguro, criar uma nova cópia reconciliada **local** com apenas a mudança de descrição.
- Não executar PUT/POST/PATCH/DELETE remoto nem qualquer publicador.
- Verificar se a variável de ambiente/template usada pela query está presente, tem sintaxe correta e usa o mesmo filtro de ambiente validado pelo operador. Essa verificação é estática; não alegar que o Log Explorer foi executado pelo Gemini.
- Executar testes JSON/schema e o dry-run **somente após comprovar que a ferramenta não causa escrita remota nem modifica canônico**.
- Demonstrar diff semântico **base remota mais atual × candidato final**, com **somente o ajuste já planejado do widget principal** em relação ao remoto (query/título/descrição) e, **em relação ao candidato 05F, exclusivamente sua descrição**. Se houver mais diferenças não autorizadas, parar e classificar NO-GO.
- Não alterar nem incluir os dois protótipos isolados.

## 4. Relatório privado enxuto

Criar arquivo novo em `darwin-monitor/planos/`, prefixo **28-**, sem sobrescrever relatórios anteriores. Registrar:
1. inconsistência encontrada e descrição **antes/depois**;
2. caminho do novo JSON candidato e da base usada;
3. origem e horário do último GET/snapshot (quando disponível) e local do backup;
4. diff semântico remoto × candidato e 05F × 05G, verificações e contagens de widgets;
5. confirmação de preservação de todas as alterações remotas não relacionadas;
6. confirmação de zero escrita remota, zero modificações do canônico/código/scripts e dois protótipos isolados;
7. estado **GO / NO-GO para submeter à autorização humana**, sem tratá-lo como autorização;
8. registro explícito da **fronteira de responsabilidade da equipe**, distinguindo sucesso/falha do processo local de resultados posteriores em outras aplicações.

## 5. Encerramento obrigatório

Exibir resumo curto e caminhos exatos dos artefatos. **PARE ANTES DE PUBLICAR**. A publicação do candidato final no dashboard UAT continua exigindo autorização **expressa e específica** do responsável, e uma checagem do estado remoto atualizado imediatamente antes de eventual PUT. Produção permanece fora do escopo.
