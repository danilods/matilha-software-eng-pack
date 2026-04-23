---
name: sweng-commits-atomicos-semanticos
description: "Use when about to commit code, reviewing a commit message, or noticing a WIP diff has grown too large. Fires on 'Como commito isso?', 'Essa mensagem de commit tá boa?', 'Meu diff tá muito grande, divido como?', 'Que tipo uso: feat ou refactor?'."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Dispara toda vez que um commit está prestes a ser criado — seja o primeiro commit da sessão, o commit de WIP no fim do dia, ou o commit de merge de uma branch. Também ativa quando o diff atual cresceu demais ("50 arquivos, 3 features misturadas") e a pergunta real é **como dividir**.

Commits servem a três propósitos: (1) documentação do projeto, (2) permitir revisão de código, (3) permitir restaurar estados anteriores com confiança. Um commit que falha em qualquer um dos três é ruído: mistura contextos, esconde o porquê, ou simplesmente não volta atrás sem quebrar tudo.

Atômico = uma mudança lógica por commit. Semântico = tipo explícito (`feat/fix/docs/refactor/test/chore`) + mensagem que explica o **porquê**, não o **o quê** (o diff já mostra o "o quê").

## Preconditions

- O repo tem git inicializado e a branch atual é de trabalho (não direto em `main`/`master` protegido).
- Você identifica a(s) mudança(s) que quer commitar (pelo menos conceitualmente).
- Se já existe diff grande, você aceita dividir antes de commitar (a tentação de "deixa tudo num commit só" é o principal inimigo aqui).
- Pre-commit hooks (se existem) estão configurados e funcionando — investigar falha, nunca passar `--no-verify` sem razão explícita.

## Execution Workflow

1. **Inventarie o diff.** `git status` + `git diff --stat`. Mentalmente, conte quantas mudanças **lógicas** diferentes estão no working tree. Se a resposta é "mais de uma", precisa dividir.
2. **Se mais de uma mudança lógica:** stage seletivo.
   - Por arquivo: `git add path/to/file1 path/to/file2`
   - Por hunk: `git add -p` (interativo, selecione hunks específicos)
   - Esconda o resto: `git stash --keep-index` enquanto commita o stageado, depois `git stash pop`.
3. **Escolha o tipo (do conjunto fixo):**
   - `feat` — funcionalidade nova visível ao usuário
   - `fix` — bug corrigido
   - `docs` — só documentação (README, comentários, ADRs)
   - `style` — formatação (espaço, lint), sem mudança de comportamento
   - `refactor` — reestruturação interna sem mudar comportamento externo
   - `test` — adicionar/modificar testes
   - `chore` — tarefas de manutenção (dependências, configs)
   - `wip` — trabalho em progresso (com descrição do que está em progresso!)
   - `project` — estrutura do próprio projeto (reorganização de pastas)
4. **Escreva a mensagem: `tipo: descrição curta no presente do indicativo`.**
   - `feat: adiciona autenticação por email`
   - `fix: corrige cálculo de juros no parcelamento`
   - `refactor: simplifica lógica de processamento de pedidos`
   - Presente, não passado: `adiciona`, não `adicionou`.
5. **Body quando o "porquê" não é óbvio pelo título.** Linha em branco após o título, depois 1–3 parágrafos explicando contexto e motivação. Referência a issue/task: `Refs #142` ou `Closes #142`.
6. **Rode os pre-commit hooks.** Se quebra, corrige e **re-stagea**, depois commita novamente (não `--amend` — NOVO commit). Hook existe para proteger; bypassar é dívida.
7. **Commita.** Se é WIP mesmo, escreva WIP com substância: `wip: inicia implementação do checkout - validação de CPF ok, falta integração com PSP` (não `wip: progresso`).
8. **Repita** para a próxima mudança lógica até working tree limpo ao final da sessão.

## Rules: Do

- **Uma mudança lógica = um commit.** Pergunta de filtro: "Se eu quiser reverter esse commit, o que volta?". Se a resposta for "três coisas diferentes", dividir.
- **Presente do indicativo.** `adiciona`, `corrige`, `remove`, `simplifica`. Mensagens no presente descrevem o que o commit **faz** ao aplicar, não o que o autor fez.
- **Tipo explícito e correto.** Mudar comportamento = `feat` ou `fix`. Não mudar comportamento = `refactor` ou `style`. Testes = `test`. Dependência = `chore`.
- **Referencie issue/task quando relevante.** `Refs #142`, `Closes #143`. Rastreabilidade para o @TODO/backlog.
- **Commit final a cada sessão.** Mesmo que WIP — working tree limpo ao fim do dia preserva contexto para retomada.
- **Código funcional pós-commit.** Cada commit idealmente passa nos testes. Quebrar build em um commit e consertar no próximo corrompe `git bisect`.
- **Investigue pre-commit hook falhando.** Linter quebrou? Testes quebraram? Corrija. Hook existe por um motivo.
- **NOVO commit após hook falhar, não `--amend`.** Se hook falhou, o commit **não aconteceu** — `--amend` mexe no commit anterior e pode destruir trabalho.

## Rules: Don't

- **Não misture features num commit só.** `feat: implementa cadastro, sistema de pedidos e integração com pagamento` é três commits colados, não um.
- **Não escreva mensagens genéricas.** `modificações`, `corrigido`, `WIP` (sem descrição), `várias correções e melhorias` — todas inúteis para revisão e histórico.
- **Não descreva o "como" no título.** `feat: usa visitor pattern para X` é ruim — "visitor pattern" é detalhe de implementação. `feat: adiciona validação composta X` fala do que faz, não como.
- **Não passe `--no-verify` sem razão documentada.** Hook falhou = investiga; não contorna.
- **Não `git push --force` em branch compartilhada** (inclui `main`, `master`, branches abertas em PR). Destrói histórico alheio.
- **Não commite arquivos de secret** (`.env`, `credentials.json`, `*.pem`). Se aconteceu, reescrever história + rotacionar credencial — e aprender.
- **Não acumule 40 arquivos num commit "salvando progresso".** Salvar progresso é `git stash` ou `wip:` com descrição.

## Expected Behavior

Após a skill rodar, o histórico tem commits atômicos e semânticos: cada um lê como uma sentença curta no presente, com tipo explícito, tamanho razoável (dezenas a centenas de linhas, não milhares), e body quando o "porquê" não é óbvio.

Output típico: 1–N novos commits no branch atual, working tree limpo ao final (ou stash dedicado para mudanças que não vão nesse batch), e mensagem final resumindo os commits criados.

A skill **não faz push**. Push é decisão humana — especialmente em branches compartilhadas.

## Quality Gates

- [ ] Cada commit tem tipo explícito (`feat/fix/docs/style/refactor/test/chore/wip/project`)?
- [ ] Título no presente do indicativo, claro, < 72 caracteres?
- [ ] Cada commit representa **uma** mudança lógica (reversível isoladamente)?
- [ ] Mensagem explica o "porquê" (via body ou título suficientemente claro)?
- [ ] Referência a issue/task presente quando aplicável?
- [ ] Pre-commit hooks passaram sem `--no-verify`?
- [ ] Nenhum arquivo de secret foi incluído?
- [ ] Build/testes passam no HEAD (ou WIP explícito com "em progresso" no título)?

## Companion Integration

- **Complementa `sweng-changelog-discipline`:** commits contam o granular; CHANGELOG agrega por release. Um commit `feat:` que afeta usuário vira 1 linha em `### Added` no CHANGELOG.
- **Complementa `sweng-session-checklists`:** checklist de encerramento inclui "commit final do dia" — esta skill define **como** esse commit deve ser.
- **Complementa `sweng-todo-structure`:** commits referenciam tarefa do `@TODO`. `feat: implementa [tarefa-do-todo] - [descrição]`.
- **Integra com `matilha-gather`:** quando matilha-gather consolida uma SP (sub-package), cada commit da SP segue estas regras; o gather final é o commit-merge semântico.
- **Integra com `matilha-hunt`:** worktree de SP termina com commits atômicos; o operador do gather revisa e faz squash/rebase conforme necessidade.

## Output Artifacts

- 1–N commits novos no histórico da branch.
- Working tree limpo ao final (ou `git stash` dedicado se houve mudanças retidas).
- Mensagem de resumo listando os commits criados e tipos.

## Example Constraint Language

- "Esse diff tem 3 mudanças lógicas: renomear `User.authenticate`, adicionar retry em `Payment.charge`, atualizar README. Faça 3 commits: `refactor: renomeia User.authenticate para ...`, `feat: adiciona retry em Payment.charge`, `docs: atualiza seção auth do README`."
- "O hook de lint quebrou. Corrige a formatação, re-stage, NOVO commit (não `--amend`). Mensagem: `style: ajusta formatação apontada pelo lint`."
- "Não commita esse arquivo `.env` junto. Adiciona a `.gitignore` primeiro: `chore: adiciona .env ao gitignore`. Depois commita o resto."

## Troubleshooting

- **"Já commitei misturado."** Se ainda não fez push: `git reset --soft HEAD~1`, depois stage seletivo e commits divididos. Se já fez push em branch pessoal: mesma coisa + `push --force-with-lease` (nunca `--force`). Se em branch compartilhada: deixa como está, aprende.
- **"Hook demora demais, perco fluxo."** Hook pesado é problema de configuração — mova testes pesados para CI e mantenha só lint/type-check rápido no pre-commit. Não bypass.
- **"Commit ficou gigante por 1 semana sem commitar."** Divida retroativamente via `git add -p` no working tree, ou crie branch "divida", cherry-pick hunks em commits atômicos separados. Próxima vez, commit final por sessão.
- **"Não sei se é `refactor` ou `feat`."** Pergunta de filtro: usuário externo vê diferença? Sim → `feat`/`fix`. Não → `refactor`/`style`.
- **"Só mudei um espaço, vale commit?"** `style:` vale. Tamanho não qualifica — atomicidade qualifica.
- **"Commit bloqueou por gpg sign."** Configure a chave; não desabilite signing silenciosamente.

## CLI shortcut (optional)

Se `matilha` CLI disponível:
```bash
matilha commit              # interactive: detecta tipo, sugere mensagem, roda hooks
matilha commit split        # ajuda a dividir working tree em N commits atômicos
```

Equivalente manual (sempre funciona):
```bash
git status
git diff --stat
git add -p                  # selecione hunks
git commit -m "feat: adiciona validação de CPF no cadastro"
# repete até working tree limpo
```

## Concrete Example

Fim de sessão, working tree com: novo módulo de auth JWT (src/auth/*), fix de um NPE em `orders.py`, ajuste de indentação em `cart.py`, atualização do README. Commit ingênuo: 1 commit "progresso auth + fixes". Resultado: impossível reverter só o fix sem perder auth; revisor precisa decifrar 4 coisas ao mesmo tempo; CHANGELOG vira bagunça.

Commit disciplinado: `feat: adiciona autenticação JWT (middleware + rotas protegidas) Refs #142`, `fix: corrige NPE quando order.customer é nulo Closes #156`, `style: ajusta indentação em cart.py`, `docs: documenta fluxo de auth no README`. Quatro commits, reversíveis isolados, revisão trivial, entries de CHANGELOG quase prontas (3 das 4 relevantes).

## Sources

- [[rules/Gerenciamento de Commits e Rastreamento de Código]] — documento completo (princípios, tipos, exemplos bons e ruins)
- Convenção externa alinhada: [Conventional Commits](https://www.conventionalcommits.org/), GitFlow — adaptado à voz do projeto (incluindo `wip` e `project`, que não são convencionais mas são úteis na prática de Danilo).
