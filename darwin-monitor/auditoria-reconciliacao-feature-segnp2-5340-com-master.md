# Gemini CLI — Auditoria e reconciliação seletiva da feature com a master

> **Uso:** executar este prompt separadamente em cada repositório de aplicação que contenha a branch indicada.  
> **Branch de referência:** `master`  
> **Branch de trabalho:** `feature/SEGNP2-5340`  
> **Objetivo:** manter exclusivamente o delta mínimo necessário à instrumentação, com testes funcionando.
>
> Este documento contém apenas instruções operacionais genéricas. Não envie código, dados internos, logs ou credenciais dos repositórios corporativos para este repositório público.

## 1. Objetivo

Analise profundamente as branches `master` e `feature/SEGNP2-5340`.

Torne a `feature/SEGNP2-5340` o mais próximo possível do estado atual da `master`, **preservando exclusivamente as alterações de código, configurações, dependências e testes comprovadamente necessárias à instrumentação implementada na feature**.

A `master` é a fonte de verdade para qualquer código, dependência, configuração ou comportamento não diretamente relacionado à instrumentação.

**Critério central de sucesso:**

> Se removermos conceitualmente da feature todas as alterações necessárias à instrumentação, o conteúdo restante deverá ser equivalente ao da master.

Isso inclui arquivos Java, testes, `pom.xml`, configurações e demais arquivos versionados.

**Execute a reconciliação; não encerre o trabalho após apenas apresentar um planejamento.**

## 2. Regras inegociáveis

1. Nunca modifique a `master`.
2. Nunca descarte alterações sem determinar sua finalidade e verificar a relação com a instrumentação.
3. Preserve o comportamento funcional da `master`, exceto pelo mínimo estritamente necessário à instrumentação.
4. Não introduza refatorações, melhorias de estilo ou mudanças arquiteturais fora do escopo.
5. Não preserve alterações da feature apenas porque já existiam.
6. Analise as diferenças por **trecho**, não apenas por arquivo. Um arquivo contendo instrumentação pode ter alterações não relacionadas que devem voltar ao estado da master.
7. Não faça merge indiscriminado nem substitua classes inteiras da master por versões antigas da feature.
8. Não altere versões de bibliotecas sem justificativa técnica demonstrável.
9. Não remova, ignore ou desabilite testes para obter resultado positivo.
10. Não altere assertions apenas para ocultar regressões.
11. Não execute `git reset --hard`, `git clean -fd`, force push ou operações destrutivas.
12. Não faça commit nem push automaticamente.
13. Não publique código, arquivos internos, logs, dados ou credenciais corporativas em repositórios externos.

**Bloqueio de segurança:** se houver mudanças locais não commitadas, conflitos ou qualquer risco de perda de trabalho, interrompa a etapa de modificação e explique o impedimento.

## 3. Fase 1 — Auditoria do Git

Identifique:

- Branch atual e working tree.
- Branches locais e remotas disponíveis.
- Referência mais recente acessível da `master`.
- Merge-base entre as branches.
- Commits exclusivos da feature.
- Arquivos alterados, adicionados e excluídos.
- Diferenças efetivas de conteúdo entre as versões atuais da master e da feature.

Atualize referências remotas somente por operações não destrutivas, quando possível. **Registre os SHAs usados** e não presuma que uma referência local desatualizada representa a master atual.

Compare tanto o histórico desde o merge-base quanto os conteúdos finais das duas branches. **Não dependa apenas de `git diff master...feature`**, pois essa visão isolada não demonstra todas as diferenças atuais em relação à master.

Antes de modificar qualquer arquivo, crie uma **branch local de backup** apontando para o HEAD original da feature, sem modificá-lo. Não avance antes de identificar e proteger o estado original.

## 4. Fase 2 — Identificação do escopo real da instrumentação

Inspecione o código para identificar as alterações que implementam observabilidade; podem incluir:

- Métricas Micrometer, contadores, timers e tags.
- Instrumentação de sucesso, falha, cancelamento ou reprocessamento.
- Campos ou atributos necessários às métricas, logs e correlação.
- Tratamentos indispensáveis ao registro correto dos resultados.
- Testes associados à instrumentação.
- Dependências realmente necessárias à instrumentação.

**Não presuma que esses elementos existem. Confirme pelo código.**

Classifique cada alteração:

| Classe | Significado | Tratamento |
|---|---|---|
| **A — Instrumentação obrigatória** | Produz ou registra diretamente a observabilidade | Preservar |
| **B — Dependência técnica** | Indispensável para compilar, executar ou testar a instrumentação | Preservar apenas o mínimo necessário |
| **C — Não relacionada** | Código, configuração, dependência, refatoração ou teste sem relação necessária | Substituir pelo estado da master |
| **D — Duvidosa** | Finalidade ou necessidade não demonstrada | Investigar; se continuar ambígua, não descartar silenciosamente e solicitar decisão |

## 5. Fase 3 — Reconciliação dos arquivos Java

Para cada arquivo Java diferente:

1. Compare a implementação atual da master com a feature.
2. Identifique os trechos específicos de instrumentação.
3. Preserve o código funcional da master.
4. Incorpore somente a instrumentação e suas dependências técnicas mínimas.
5. Elimine divergências não relacionadas herdadas da feature.
6. Revise imports, assinaturas, construtores, injeções e compatibilidade entre classes.
7. Confirme os pontos corretos de execução da instrumentação.

**Se uma classe evoluiu na master, adapte a instrumentação à implementação atual dessa classe. Não reverta a evolução da master.**

Preserve a semântica das métricas: início não equivale a conclusão; sucesso não equivale a solicitação; falha precisa ser registrada no ponto correto. Evite contagens duplicadas e alterações silenciosas do fluxo de negócio.

Quando houver auxiliares de observabilidade, valide seu **uso efetivo no fluxo**, e não somente a existência da classe. Preserve a limpeza do contexto de correlação (por exemplo, MDC) após o processamento quando aplicável.

## 6. Fase 4 — Reconciliação de todos os pom.xml

**Etapa crítica: a configuração da master tem precedência, exceto pelo que for comprovadamente indispensável à instrumentação.**

Analise individualmente dependências adicionadas/removidas, versões, propriedades Maven, plugins, `dependencyManagement`, perfis, configuração de compilação e testes, repositórios e demais elementos.

- **Dependência não relacionada:** restaurar exatamente a versão da master, incluindo versão, escopo e exclusões.
- **Dependência nova exclusiva da instrumentação:** preservar somente se houver uso comprovado e inexistir dependência equivalente já fornecida pela master.
- **Dependência comum com versões diferentes:** priorizar a versão da master. Divergência somente mediante incompatibilidade real demonstrada, com evidência técnica.
- **Plugins e configurações Maven alterados:** seguir a master, salvo necessidade indispensável para compilar, executar ou testar a instrumentação.
- **Dependências removidas na master:** não reintroduzir apenas porque existem na feature. Investigar se a instrumentação de fato precisa delas.

**Resultado esperado:** minimizar o diff de todos os `pom.xml`. Se a instrumentação já for suportada pelas dependências da master, os `pom.xml` finais devem ser idênticos aos da master.

## 7. Fase 5 — Outros arquivos e configurações

Aplique o mesmo princípio a:

- `application.yml`, `application.properties` e variantes por ambiente.
- XML, recursos e configurações de logging.
- Arquivos e configurações de testes.
- Scripts e configurações de build.

Preserve apenas as diferenças necessárias à instrumentação. Não altere endpoints, credenciais, URLs, parâmetros operacionais ou regras de negócio sem necessidade comprovada.

## 8. Fase 6 — Execução da reconciliação

Depois da classificação e da proteção do estado original:

1. Reconcilie a feature usando a master como referência.
2. Preserve a instrumentação e as dependências técnicas mínimas.
3. Use o código atual da master para todo o restante.
4. Resolva conflitos semanticamente, não apenas textualmente.
5. Revise arquivos parcialmente modificados e elimine trechos fora do escopo.
6. Remova apenas arquivos exclusivos da feature comprovadamente desnecessários.
7. Não deixe modificações adicionais fora do objetivo.

**A compilação bem-sucedida não basta:** o diff final deve demonstrar apenas a instrumentação necessária.

## 9. Fase 7 — Validação técnica e testes

Identifique primeiro a versão de Java, o Maven e os módulos/profiles pertinentes.

Valide:

1. Integridade do diff: `git diff --check`.
2. Compilação do código principal.
3. Compilação dos testes.
4. Execução dos testes unitários existentes.
5. Execução dos testes específicos da instrumentação.
6. Build completo quando viável.

Em projetos Maven, utilize comandos equivalentes a:

```bash
mvn -B -ntp clean test
```

E, quando aplicável:

```bash
mvn -B -ntp verify
```

Adapte aos módulos, profiles e configuração real do repositório. Não use `-DskipTests` ou `-Dmaven.test.skip=true` para declarar validação concluída.

Se testes falharem, identifique a causa, investigue se a falha já existe na master e corrija regressões causadas pela reconciliação. Preserve as asserções e execute novamente os testes afetados e a suíte pertinente.

Se rede, dependências corporativas, infraestrutura externa ou configuração local impedirem a execução, registre exatamente o bloqueio. **Não declare aprovação de testes que não foram executados.**

## 10. Fase 8 — Auditoria final do diff

Compare o resultado com **o mesmo SHA da master registrado no início**.

Para cada arquivo ainda diferente, responda:

1. Por que continua diferente?
2. Qual trecho exato pertence à instrumentação?
3. A diferença é realmente necessária?
4. Algum código atual da master foi substituído indevidamente?
5. Há dependência ou configuração desnecessária?
6. Os testes pertinentes cobrem a alteração?

Não considere a tarefa concluída com diferenças não justificadas. O objetivo é o **menor diff funcional possível** contra a master.

## 11. Relatório final obrigatório

Produza relatório em Markdown contendo:

### A. Referências utilizadas

- Repositório e branch de trabalho.
- SHA da master de referência.
- SHA original da feature.
- Nome da branch local de backup.

### B. Alterações preservadas

Tabela: arquivo, trecho/alteração de instrumentação e justificativa.

### C. Alterações alinhadas à master

Tabela: arquivo, divergência removida e motivo.

### D. Dependências Maven

Tabela: dependência/configuração, estado original da feature, estado na master, estado final e justificativa de qualquer diferença preservada.

### E. Validação

Comandos realmente executados, resultado de cada um, quantidade de testes, falhas e impedimentos.

### F. Diff residual

Todos os arquivos ainda diferentes da master e justificativa individual.

### G. Conclusão

Responda explicitamente:

- A feature está alinhada à master em tudo que não é instrumentação?
- A instrumentação foi integralmente preservada e permanece conectada aos fluxos?
- Os testes unitários passaram?
- O build foi validado?
- Há diferenças ou riscos pendentes?
- Há verificações manuais necessárias?

## Critérios finais de aceitação

- [ ] A master permaneceu intacta.
- [ ] Backup local criado para o estado original da feature.
- [ ] Todas as diferenças auditadas e classificadas.
- [ ] Tudo que não é instrumentação foi alinhado à master.
- [ ] Os `pom.xml` preservam apenas diferenças indispensáveis.
- [ ] A instrumentação foi preservada e adaptada ao código atual.
- [ ] Os testes pertinentes foram executados e aprovados.
- [ ] Não houve alteração silenciosa do comportamento funcional.
- [ ] O diff residual está integralmente justificado.
- [ ] Nenhum commit nem push foi executado automaticamente.

**Diretriz principal: a master manda em tudo que não for instrumentação. A feature deve conter somente o delta mínimo necessário para implementar e validar essa instrumentação.**
