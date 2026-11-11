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

O revisor identificou uma **única inconsistência semântica material na descrição do widget principal**: a descrição atual candidata dá a entender que a mensagem **foi efetivamente entregue/descartada na DLQ**, embora o log demonstre apenas emissão do sinal terminal, geração/acionamento de evento de erro e encaminhamento pelo caminho NACK/DLQ — **sem confirmação técnica da entrega física à DLQ**.

Isso contraria o limite de evidência explicitado no relatório privado. É necessário corrigir somente essa formulação, mantendo valor operacional do indicador.

## 2. Mudança local mínima

1. Ler seletivamente o relatório 27 e o candidato reconciliado 05F, identificando no arquivo privado o widget principal de esgotamento já aprovado quanto à query pelo operador.
2. Inspecionar a sua descrição atual e a evidência local que sustenta os termos; não alterar mensagem de log, código ou query validada.
3. Gerar **nova versão local candidata 05G**, a partir da reconciliada 05F, sem sobrescrever a anterior.
4. **Alterar exclusivamente o texto da descrição desse único widget**, usando formulação consistente com:
   - unidade observada: **ocorrências do log/sinal de esgotamento das tentativas do fluxo de cancelamento**;
   - há indicação de evento de erro e acionamento do caminho NACK destinado à DLQ, quando isso estiver fundamentado no código;
   - não comprova efetiva entrega à DLQ, deduplicação por proposta, cancelamento definitivo, nem cobrança indevida posterior;
   - não mistura o fluxo de estorno ou outro fluxo distinto.
5. Não apresentar "mensagens descartadas", "entrega confirmada à DLQ", "contratos únicos" ou taxa de negócio como fatos comprovados sem telemetria específica.
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
7. estado **GO / NO-GO para submeter à autorização humana**, sem tratá-lo como autorização.

## 5. Encerramento obrigatório

Exibir resumo curto e caminhos exatos dos artefatos. **PARE ANTES DE PUBLICAR**. A publicação do candidato final no dashboard UAT continua exigindo autorização **expressa e específica** do responsável, e uma checagem do estado remoto atualizado imediatamente antes de eventual PUT. Produção permanece fora do escopo.
