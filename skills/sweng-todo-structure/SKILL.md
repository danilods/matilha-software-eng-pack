---
name: sweng-todo-structure
description: "Use when creating or reorganizing a project @TODO.md, prioritizing backlog, or deciding where a new task belongs. Fires on '@TODO do projeto', 'Onde coloco essa tarefa?', 'Como priorizo?', 'Backlog tá bagunçado', 'Preciso de um planejamento central'."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Dispara quando o projeto precisa de um arquivo central de planejamento — seja porque ainda não existe (`@TODO.md` ausente), porque o atual virou uma lista caótica sem priorização, ou porque uma tarefa nova chegou e o lugar dela não é óbvio.

O `@TODO.md` na raiz do projeto é o **contrato da equipe com o projeto** (mesmo que a equipe seja só você). Ele responde três perguntas em qualquer momento: (a) o que está em progresso agora?, (b) o que vem depois?, (c) o que já foi feito (e quando)?

A estrutura canônica tem **5 zonas** com emojis fixos (não cosmética — emojis funcionam como labels instantâneos para scan visual): `🚧 Em Progresso`, `⚡ Próximas (Alta)`, `📋 Backlog (Normal)`, `🔮 Futuras (Baixa)`, `✅ Concluído`. Sem zona, sem prioridade; sem prioridade, caos.

## Preconditions

- Projeto tem raiz identificada (onde vive `README.md`) — `@TODO.md` mora lá.
- Se já existe lista de tarefas espalhada (issues do GitHub, post-its, Notion), aceite migrar para `@TODO.md` como single source of truth — duas fontes de verdade = zero fonte de verdade.
- Existe noção de prioridade (mesmo informal) entre as tarefas atuais.
- Para projetos longos: `sweng-documentation-directory` já rodou (ou vai rodar junto), porque `@TODO.md` e `@documentation/progresso/atual.md` se complementam.

## Execution Workflow

1. **Crie (ou reorganize) `@TODO.md` na raiz** com o esqueleto:
   ```markdown
   # @TODO: [Nome do Projeto]

   ## 🚧 Em Progresso
   - [ ] [CRÍTICO] Tarefa atual [Estimativa]
     - **Descrição:** ...
     - **Critério de Aceitação:** ...
     - **Bloqueadores:** ...
     - **Próximo passo:** ...

   ## ⚡ Próximas Tarefas (Alta Prioridade)
   - [ ] Tarefa prioritária 1 [2h]
   - [ ] Tarefa prioritária 2 [4h]

   ## 📋 Backlog (Prioridade Normal)
   - [ ] Funcionalidade 1 [1d]
   - [ ] Funcionalidade 2 [3h]

   ## 🔮 Futuras Melhorias (Baixa Prioridade)
   - [ ] Melhoria 1
   - [ ] Melhoria 2

   ## ✅ Concluído
   - [x] Tarefa concluída 1 (15/04/2026)
   - [x] Tarefa concluída 2 (14/04/2026)
   ```
2. **Mova no máximo 1–2 tarefas para `🚧 Em Progresso`.** WIP disciplinado: mais de duas simultâneas = nenhuma progredindo bem. Cada tarefa em Progresso carrega **template expandido** (descrição, critério, bloqueadores, próximo passo).
3. **Priorize o resto em zonas.** Regra de filtro:
   - `⚡ Alta` — bloqueia ou acelera o objetivo principal atual da sprint/semana.
   - `📋 Normal` — precisa ser feito, mas não bloqueia; "logo depois que Alta esvaziar".
   - `🔮 Baixa` — ideias que vale registrar mas não vai olhar essa semana.
4. **Defina cada tarefa nova com o template completo** (descrição + critério + estimativa + dependências + riscos), ver `sweng-task-definition-estimativas` para detalhe.
5. **Estimativas em buckets canônicos:** `[15min]`, `[1h]`, `[4h]`, `[1d]`. Se tarefa parece > 1d, **quebra em subtarefas**.
6. **Prioridades com labels:** `[CRÍTICO]` (bloqueador), `[URGENTE]` (prazo apertado), `[ALTA]`, `[NORMAL]`, `[BAIXA]`. Use `[BLOQUEADOR]` inline quando uma tarefa bloqueia outra.
7. **Concluído acumula com data.** Nunca apague tarefas concluídas — histórico é valor. Data no formato `DD/MM/YYYY` (ou ISO, escolha e mantenha consistente).
8. **Mova em tempo real durante a sessão.** Iniciou tarefa? Move para `🚧`. Terminou? `✅` com data. Nova ideia? `📋` ou `🔮`. Não deixa pra "arrumar depois".
9. **Commit dedicado para alterações estruturais grandes:** `docs: reorganiza @TODO (nova sprint, prioridades atualizadas)`. Para movimentações rotineiras (checkbox, mover item), o commit da feature referencia o @TODO.

## Rules: Do

- **5 zonas, nem mais nem menos.** Expansão vira ruído; redução perde granularidade.
- **Max 1–2 em `🚧 Em Progresso`.** WIP limit disciplinado. Terceira tarefa ativa é falso paralelismo.
- **Toda tarefa tem estimativa em bucket canônico.** Sem estimativa = tarefa não pensada.
- **Toda tarefa em `🚧` tem template expandido** (descrição + critério + próximo passo). Tarefa sem critério de aceitação vira "pronto quando sentir que tá pronto" — subjetivo, sem fim claro.
- **`✅ Concluído` é imutável.** Só acrescenta, não edita, não remove. Data sempre.
- **Move tarefas em tempo real**, não em batch no fim da semana.
- **Priorização é decisão ativa.** Se tudo é `⚡ Alta`, nada é alta — reescreva.
- **Linke issue/PR.** `- [ ] Feat X [2h] Refs #142` quando existe.
- **Sincronize com `progresso/atual.md`.** Tarefa em `🚧` no @TODO é o foco do `atual.md`.

## Rules: Don't

- **Não confunda `@TODO.md` com `progresso/atual.md`.** @TODO é planejamento (o que fazer); atual.md é snapshot de execução (estado de agora). Complementam, não substituem.
- **Não dupe zonas ("Alta 1", "Alta 2").** Uma zona, ordem dentro dela.
- **Não escreva tarefas vagas.** `- [ ] Fazer cadastro` é ruim. `- [ ] [ALTA] Implementar formulário de cadastro com validação de CPF [4h]` é mínimo aceitável.
- **Não deixe `🚧` acumular.** Tarefa há mais de 2 dias sem update em `🚧` = sinal de alerta (ver seção Troubleshooting).
- **Não apague `✅`.** Histórico é auditoria. Apagado = perdido.
- **Não use `@TODO.md` como diário.** Observações curtas vão no template da tarefa; observação longa vai em `progresso/atual.md` ou em ADR de `@documentation/arquitetura/`.
- **Não crie `@TODO-backend.md` e `@TODO-frontend.md` separados.** Fragmenta o contrato. Mantém um só, com seções se necessário.

## Expected Behavior

Após a skill rodar, `@TODO.md` tem:

1. As 5 zonas presentes, cada uma com cabeçalho consistente.
2. 1–2 tarefas em `🚧 Em Progresso` com template expandido.
3. Próximas tarefas priorizadas em `⚡`, `📋`, `🔮` com estimativas.
4. `✅ Concluído` preservando histórico com datas.
5. Link implícito ou explícito para `@documentation/progresso/atual.md` e para issues/PRs quando relevantes.

Output típico: um `@TODO.md` reorganizado (diff grande se houve limpeza inicial, pequeno em manutenção) + commit dedicado `docs: reorganiza @TODO ...`.

## Quality Gates

- [ ] As 5 zonas canônicas estão presentes com emojis corretos?
- [ ] No máximo 2 tarefas em `🚧 Em Progresso`?
- [ ] Toda tarefa tem estimativa em bucket canônico (`15min/1h/4h/1d`)?
- [ ] Toda tarefa em `🚧` tem template expandido (descrição, critério, próximo passo)?
- [ ] Zonas de prioridade estão com conteúdo coerente (não tudo em `⚡`)?
- [ ] `✅ Concluído` tem datas em formato consistente?
- [ ] `@TODO.md` está linkado no README (seção "Roadmap" ou "Status")?
- [ ] Não há fragmentação em múltiplos `@TODO-*.md`?

## Companion Integration

- **Depende de `sweng-documentation-directory`:** `@TODO.md` vive na raiz, mas sincroniza com `@documentation/progresso/atual.md` — um é planejamento, outro é snapshot.
- **Feeds `sweng-session-checklists`:** o checklist de início lê `@TODO.md` primeiro para identificar tarefa em `🚧`.
- **Feeds `sweng-task-definition-estimativas`:** esta skill cria a estrutura; definição detalhada de cada tarefa mora na skill companheira.
- **Feeds `sweng-commits-atomicos-semanticos`:** commits referenciam tarefas do @TODO (`Refs [tarefa]` ou `feat: [tarefa] - descrição`).
- **Integra com `matilha-plan`:** quando matilha-plan produz spec de feature nova, a feature entra como tarefa(s) em `@TODO.md` zona `⚡` ou `📋`.
- **Integra com `matilha-hunt`:** SPs de uma wave matilha podem mapear 1:1 para tarefas no @TODO, cada uma com seu sub-commit atômico.

## Output Artifacts

- `@TODO.md` na raiz do projeto, reorganizado ou criado.
- Link no README (em "Roadmap" ou "Status do Projeto") para `@TODO.md`.
- Commit dedicado: `docs: reorganiza @TODO.md com zonas de prioridade e estimativas` (ou `docs: cria @TODO.md inicial`).

## Example Constraint Language

- "Essa nova funcionalidade vai pra `⚡ Próximas` ou `📋 Backlog`? Se ela bloqueia a entrega desta sprint, `⚡`. Se não bloqueia, `📋`."
- "Tarefa em `🚧` há 3 dias sem update. Dois caminhos: desbloqueia e finaliza hoje, ou move para `📋` com nota `pausada por X` e pega outra."
- "Mais de duas tarefas em `🚧`? Escolhe uma, move as outras de volta para `⚡`. Dois WIPs = zero terminados bem."

## Troubleshooting

- **"Tenho 200 tarefas herdadas de issue tracker."** Primeira passagem: move tudo que não foi olhado em 30 dias para `🔮 Futuras Melhorias` (ou arquiva). `@TODO.md` é foco ativo, não arquivo morto. Segunda passagem: o que sobrou, priorize em `⚡`/`📋`.
- **"Ninguém do time atualiza."** Sintoma de: (a) @TODO desconectado do fluxo de commit, (b) sem ritual de sessão (ver `sweng-session-checklists`), (c) formato inconsistente (ninguém sabe onde colocar). Corrija na ordem.
- **"Tarefa travada em `🚧` faz uma semana."** Sinal de alerta. Duas causas possíveis: bloqueador externo (documenta em `atual.md` + move para `📋` pausado) ou tarefa mal definida (quebra em subtarefas ou reescreve critério de aceitação).
- **"Priorização muda toda semana, sensação de caos."** Normal no começo. Fixe a revisão na sexta/segunda; mudança fora dessa cadência só para `[CRÍTICO]` genuíno.
- **"Projeto solo — precisa de template completo?"** Em `🚧`: sim (descrição + critério + próximo passo). Em outras zonas: título + estimativa basta.
- **"Estou misturando bugs com features."** Rotule inline: `- [ ] [BUG][ALTA] Corrige NPE em orders.py [1h]` vs `- [ ] [FEAT][NORMAL] Adiciona relatório X [1d]`. Zona continua por prioridade; rótulo diferencia por tipo.

## CLI shortcut (optional)

Se `matilha` CLI disponível:
```bash
matilha todo init                    # cria @TODO.md com esqueleto canônico
matilha todo add "Tarefa X" --zone alta --est 2h
matilha todo move "Tarefa X" --to progresso
matilha todo done "Tarefa X"         # move para Concluído com data de hoje
```

Equivalente manual:
```bash
$EDITOR @TODO.md
# edita, salva, commita quando mudança é estrutural
git add @TODO.md && git commit -m "docs: move feature X para Em Progresso"
```

## Concrete Example

Semana 2 de projeto. `@TODO.md` acumulou 40 itens sem priorização, três em "em progresso" ao mesmo tempo, zero estimativas. Dev abre segunda e não sabe por onde começar.

Aplicando a skill: (1) Escolhe 1 tarefa para `🚧` (refresh token JWT, o próximo passo mais claro) com template completo. (2) Move as outras duas de "progresso" para `⚡ Próximas`. (3) Varre 40 itens: 5 viram `⚡` (bloqueiam sprint atual), 12 viram `📋` (backlog ativo), 18 viram `🔮` (ideia registrada, sem prazo), 5 vão para `✅` (já foram feitos mas não marcados, com datas recuperadas do git log). Commit: `docs: reorganiza @TODO.md com 5 zonas, estimativas e prioridades`.

Resultado: dev sabe em 30s qual é a tarefa atual, qual é a próxima, e o que esperar da semana. Manutenção semanal: 10min.

## Sources

- [[rules/Gestão de Tarefas e Acompanhamento de Execução]] — seção "### 1. Arquivo Principal: [@TODO]" + "## Padrões de Qualidade"
- [[rules/# Planejamento Completo do Projeto]] — seções "## Estrutura do Arquivo @TODO" + "## Processo de Atualização" (a estrutura de 5 zonas e as práticas de priorização vêm desta rule)
