---
name: sweng-session-checklists
description: "Use when starting or ending a development session, or wondering how to resume tomorrow where you left off today. Fires on 'Começando sessão', 'Terminando sessão', 'Como continuo amanhã?', 'Não sei por onde retomar', 'Fim de expediente, preciso parar limpo'."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Dispara no começo de uma sessão de trabalho (5min de aquecimento) e, com peso maior, no **encerramento** (10min de descompressão). Também ativa quando o sintoma é "abro o projeto e não sei por onde começar" — que é diagnóstico de encerramento mal feito ontem.

Sessão de trabalho tem três fases: (1) início — entrar em contexto, escolher foco; (2) durante — executar com rastro; (3) fim — deixar snapshot coerente para o próximo você. Cada fase tem um ritual curto e cumulativo: pular o fim de uma sessão penaliza o início da próxima em 30–60min de arqueologia.

A disciplina dos checklists é barata (total ~15min/sessão) e o payoff é alto (30–60min recuperados por sessão retomada), principalmente em projeto multi-semana onde cada sessão começa com contexto zero se não houver snapshot persistente.

## Preconditions

- Existe `@documentation/progresso/atual.md` (ou equivalente). Se não existe, execute `sweng-documentation-directory` primeiro.
- Existe `@TODO.md` na raiz (ou estrutura equivalente). Se não, rode `sweng-todo-structure` primeiro.
- Git está inicializado — o checklist de encerramento inclui commit final.
- Você tem pelo menos 5min no início e 10min no fim dedicados (não pule achando "vou só começar rápido" — é falsa economia).

## Execution Workflow

### Checklist de Início (5min)

1. **Lê `@TODO.md`** — identifica a tarefa em `🚧 Em Progresso` (deve haver uma, no máximo duas; se tiver mais, `sweng-todo-structure` foi violado).
2. **Lê `@documentation/progresso/atual.md`** — confirma último foco, bloqueadores conhecidos, próximo passo deixado pela sessão anterior.
3. **`git log -5 --oneline`** — confirma último commit, branch atual, se há algo stashed (`git stash list`).
4. **Define o objetivo específico da sessão em 1 frase.** Exemplo: "Finalizar refresh token e testar integração com frontend". Se não consegue resumir em uma frase, não é sessão, é duas.
5. **Verifica bloqueadores.** Se o próximo passo depende de algo externo ainda não resolvido (chave AWS, decisão pendente, review), troque de tarefa ou desbloqueia antes — senão a sessão trava no meio.

### Durante o Desenvolvimento

6. **Trabalha na tarefa escolhida** (e só nela — desvio não planejado vira item no `@TODO`, não execução na hora).
7. **A cada 1–2h:** atualiza `progresso/atual.md` com uma linha curta ("12:00 — middleware JWT validado com testes; próximo: refresh token").
8. **A cada commit:** mensagem referencia a tarefa (`Refs #142` ou `feat: [tarefa-do-todo] - ...`) — ver `sweng-commits-atomicos-semanticos`.
9. **Ao encontrar bloqueador:** registra imediatamente em `progresso/atual.md` (não espera o fim para lembrar).

### Checklist de Encerramento (10min)

10. **Atualiza status no `@TODO.md`.** Move tarefas entre zonas (`🚧 Em Progresso` → `✅ Concluído` se terminou; mantém em `🚧` se continua).
11. **Atualiza `progresso/atual.md`** com: o que foi feito hoje (2–5 bullets), próximo passo específico (1 linha, acionável), estimativa de tempo, bloqueadores ativos/resolvidos, prioridade da próxima sessão.
12. **Commit final** (mesmo que WIP com substância): `wip: implementa middleware JWT, falta refresh token — próximo: integrar refresh em auth.ts` ou commit semântico se a tarefa concluiu.
13. **`git push`** da branch (backup externo). Não acumula dia de trabalho só local — disco morre, máquina queima, saldo zerado.
14. **Escreve 1 frase para o próximo você:** "Amanhã começa por X, checa Y primeiro, cuidado com Z."

## Rules: Do

- **Faça os dois checklists mesmo em sessão curta.** 5+10min em sessão de 2h parece caro; sessão de 4h sem retomada custa 40min de warmup — contas fecham.
- **`progresso/atual.md` é o principal artefato do encerramento.** É lá que o próximo você lê primeiro.
- **Commit final sempre.** Mesmo que WIP. Working tree sujo ao fim do dia é perda de contexto empacotada para amanhã.
- **Push após commit final.** Backup externo não é opcional.
- **Próximo passo é 1 linha acionável.** Não "continuar feature X"; sim "implementar refresh token em `src/auth/jwt.ts`, função `refresh(token)`, teste em `tests/auth.test.ts`".
- **Bloqueadores documentados imediatamente.** Memória falha entre ondas de foco.
- **Objetivo da sessão em 1 frase.** Filtro: se não cabe numa frase, a sessão tem mais de um objetivo — escolha um.
- **Honre o `@TODO`.** Se durante a sessão surge nova ideia, vai para `@TODO` (backlog), não para execução imediata.

## Rules: Don't

- **Não pule o encerramento "porque tá atrasado".** É exatamente quando mais importa — cansado + sem snapshot = amnésia amanhã.
- **Não escreva `atual.md` genérico.** "Continuei implementando auth" é inútil. Detalhes específicos (nomes de função, linhas aproximadas, próximo sub-passo) são o que salva contexto.
- **Não abra sessão sem ler `atual.md`.** Mergulhar direto no código sem snapshot gera retrabalho.
- **Não acumule `@TODO` desatualizado.** Durante a sessão, move itens em tempo real (Em Progresso → Concluído, novas ideias → Backlog).
- **Não deixe mais de uma tarefa em `🚧 Em Progresso`.** Dois WIPs simultâneos = dois contextos parcialmente carregados = zero terminados bem.
- **Não confie "vou lembrar amanhã".** Não vai. Escreva.

## Expected Behavior

Após encerrar a sessão com esta skill, o próximo você abre o projeto e encontra:

1. `progresso/atual.md` com **data de hoje**, 2–5 bullets do que foi feito, próximo passo específico, bloqueadores explícitos.
2. `@TODO.md` coerente: uma tarefa em `🚧 Em Progresso` (ou zero se terminou limpo), zonas de prioridade arrumadas.
3. `git log -1` mostrando commit final com mensagem de substância.
4. `git status` limpo (ou `git stash list` explicado em `atual.md`).
5. `git push` feito — branch no remoto atualizada.

Tempo total gasto nos dois checklists: ~15min. Tempo economizado na retomada da próxima sessão: 30–60min. ROI positivo a partir da segunda sessão.

## Quality Gates

### Início
- [ ] Li `@TODO.md` e identifiquei tarefa em `🚧 Em Progresso`?
- [ ] Li `progresso/atual.md` e sei qual era o próximo passo?
- [ ] `git log -5 --oneline` confirma estado esperado?
- [ ] Objetivo da sessão resumido em 1 frase?
- [ ] Bloqueadores checados (nada travando o próximo passo)?

### Encerramento
- [ ] `@TODO.md` atualizado (tarefas movidas entre zonas)?
- [ ] `progresso/atual.md` tem data de hoje, feito-hoje, próximo-passo (1 linha acionável), bloqueadores?
- [ ] Commit final feito (semântico ou WIP com substância)?
- [ ] `git push` executado?
- [ ] `git status` limpo (ou stash documentado em `atual.md`)?

## Companion Integration

- **Depende de `sweng-documentation-directory`:** precisa de `@documentation/progresso/atual.md` existindo.
- **Depende de `sweng-todo-structure`:** precisa de `@TODO.md` com zonas canônicas.
- **Complementa `sweng-commits-atomicos-semanticos`:** o commit final do checklist segue as regras de commit atômico + semântico.
- **Integra com `matilha-howl`:** `matilha-howl` lê `progresso/atual.md` para dizer "próxima ação é X" — esta skill mantém `atual.md` vivo para `matilha-howl` funcionar.
- **Integra com `matilha-hunt`/`matilha-gather`:** em waves matilha, cada SP pode seguir este ritual em versão reduzida (snapshot por SP em vez de por dia).

## Output Artifacts

### Início
- Objetivo da sessão declarado em 1 frase (mental ou no topo de `atual.md`).
- Lista curta de próximos passos identificados.

### Encerramento
- `@documentation/progresso/atual.md` atualizado com data de hoje.
- `@TODO.md` refletindo estado real.
- Commit final (`git log -1`).
- Push para remoto.

## Example Constraint Language

- "Antes de mergulhar no código, lê `progresso/atual.md` e resume em 1 frase o objetivo desta sessão. Se não conseguir resumir, me mostra seus candidatos — vou te ajudar a escolher um."
- "Está 17:45. Antes de fechar: atualiza `atual.md` com o que foi feito hoje + próximo passo em 1 linha acionável, faz commit final (mesmo WIP), push. Dez minutos, não mais."
- "Nova ideia? Registra em `@TODO.md` na zona `📋 Backlog` e volta pra tarefa em andamento. Não troca contexto no meio da sessão."

## Troubleshooting

- **"Abri e não sei por onde começar."** Sinal de encerramento mal feito ontem. Lê últimos 3 commits, reconstrói mentalmente, **e escreve um `atual.md` retroativo antes de codar**. Próxima vez, não pula o fim.
- **"Checklist parece burocracia."** Teste por 5 sessões. Meça tempo de warmup antes/depois. A maioria dos devs fica convencida pela diferença empírica.
- **"Sessão foi curta (1h), vale a pena?"** Encerramento reduzido (3min) basta: 1 bullet no `atual.md`, commit WIP, push.
- **"Não terminei o 'próximo passo' previsto."** Ok, comum. Atualiza `atual.md` com onde parou e o que faltou. Não é falha — é execução real.
- **"`atual.md` tá virando dump longo."** Corta. `atual.md` é snapshot **presente**; histórico vai para `progresso/concluido.md` ou vira commit que já carrega contexto.
- **"Esqueci de fazer push ontem."** Primeira coisa hoje: push. Depois continua como normal.

## CLI shortcut (optional)

Se `matilha` CLI disponível:
```bash
matilha session start    # imprime @TODO + atual.md, pede objetivo da sessão
matilha session end      # abre template de encerramento em atual.md
```

Equivalente manual:
```bash
# INÍCIO
cat @TODO.md
cat @documentation/progresso/atual.md
git log -5 --oneline

# FIM
$EDITOR @documentation/progresso/atual.md
$EDITOR @TODO.md
git add -A && git commit -m "wip: ..."
git push
```

## Concrete Example

**Cenário com ritual:** 18:00 sexta. Dev atualiza `atual.md`:
> **Estado atual (21/04/2026 18:00).** Feito hoje: middleware JWT criado, 3 testes passando. **Próximo passo:** implementar `refresh(token)` em `src/auth/jwt.ts`, teste em `tests/auth.test.ts` (est. 1h). **Bloqueador:** nenhum. **Amanhã começa por:** rodar `pytest tests/auth/ -v`, confirmar baseline, implementar refresh.

Commit: `wip: adiciona middleware JWT (3 testes ok), falta refresh token`. Push.

**Segunda 09:00.** Abre `atual.md`, lê 30s, sabe exatamente o próximo movimento. Em 3min está rodando o teste. 55min depois, feature concluída.

**Cenário sem ritual:** 18:00 sexta, fecha laptop. Segunda 09:00 abre, olha `git log`, lê 4 commits, não lembra se testou o middleware ou não. Abre testes, roda, quebra um (mudou outra coisa), debug 40min descobrindo que foi config de ambiente. 10:20 ainda não escreveu linha produtiva. Perdeu 1h20.

## Sources

- [[rules/Gestão de Tarefas e Acompanhamento de Execução]] — seções "### 1. Início de Sessão", "### 2. Durante o Desenvolvimento", "### 3. Final de Sessão"
- [[rules/Documentação Completa do Projeto]] — seção "## Prevenção de Perda de Contexto" (complementa o racional dos checklists)
