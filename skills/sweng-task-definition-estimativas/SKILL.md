---
name: sweng-task-definition-estimativas
description: "Use when defining a new task, estimating effort, or when a task in the backlog feels vague ('refactor X', 'improve Y', 'one week') — enforces task template + hour-session estimates."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Fires quando o usuário está criando uma tarefa nova no `@TODO`, estimando quanto tempo algo vai levar, ou reclamando que "essa tarefa tá muito vaga". Fires também em review de backlog, quando se descobre uma tarefa antiga sem critério de aceitação claro.

O problema-alvo são tarefas que **parecem definidas mas não são**: "refatorar módulo de pagamentos", "melhorar performance", "estudar opções". Essas tarefas vivem no backlog, ninguém pega, porque ninguém sabe quando elas terminam. O skill transforma-as em unidades executáveis com critério de conclusão e estimativa em hora-sessão.

## Preconditions

- Existe um `@TODO` ou backlog onde tarefas vivem (ver `sweng-todo-structure`).
- O usuário aceita que tarefa sem critério de aceitação é tarefa impossível de concluir (só de abandonar).
- Estimativas vão usar unidade hora-sessão (`15min`, `1h`, `4h`, `1d`), não "sprints" nem "semanas abstratas".
- Há disposição para quebrar tarefas >1d em subtarefas — monólitos de "uma semana" vão ser rejeitados.

## Execution Workflow

1. Receba a tarefa bruta do usuário (ex.: "refatorar autenticação").
2. Aplique o **template obrigatório** de 5 campos:
   - **Título**: claro, específico, verbo de ação ("Implementar X", "Substituir Y por Z"), não "refatorar" sozinho.
   - **Descrição**: 1-3 frases do que deve ser feito.
   - **Critérios de Aceitação**: lista de condições verificáveis — cada uma ou é verdade ou é falsa, não subjetiva.
   - **Dependências**: o que precisa estar pronto antes.
   - **Recursos**: links, docs, APIs relevantes (dispensa ctrl-F no momento de executar).
   - **Riscos**: possíveis complicações conhecidas (1-2 linhas).
3. Estime usando a **tabela fixa**:
   - `[15min]` — ajuste simples, correção menor, rename local.
   - `[1h]` — implementação pequena, fix com teste, doc curta.
   - `[4h]` — funcionalidade média, uma feature de escopo limitado.
   - `[1d]` — funcionalidade complexa, atravessa múltiplos módulos.
   - `[Xd]` — **quebre em tarefas menores**. `>1d` não é estimativa, é sinal de que a tarefa não foi pensada.
4. Classifique prioridade: `[CRÍTICO]` (bloqueia outras), `[URGENTE]` (prazo), `[ALTA]` (objetivo principal), `[NORMAL]` (backlog), `[BAIXA]` (futuro).
5. Valide: se algum critério de aceitação é subjetivo ("deve ficar bom", "performance melhor"), reescreva até ser verificável ("p95 < 200ms", "zero erros 500 em 1h de stress test").
6. Grave no `@TODO` com o template completo. Se a tarefa não passar no passo 5, ela não entra no backlog — vai para "a refinar" ou volta para o usuário.

## Rules: Do

- Sempre use verbo de ação no título: "Implementar refresh token", "Substituir lib X por Y", "Documentar endpoints de auth".
- Critérios de aceitação em forma de lista verificável — idealmente cada um testável por comando ou teste automatizado.
- Estimativa em hora-sessão; use a tabela fixa `[15min/1h/4h/1d]`. Quebrar qualquer coisa acima de 1d.
- Registre dependências explícitas — a tarefa "depende de X concluído" explicita ordem de execução e evita deadlocks silenciosos.
- Inclua riscos conhecidos curtos — ajuda o executor a preparar fallbacks.

## Rules: Don't

- Não use verbos vagos: "refatorar", "melhorar", "otimizar", "estudar", "revisar". Eles não têm ponto de chegada. Substitua por verbo concreto ("reduzir acoplamento entre X e Y", "substituir loop N² por index").
- Não estime em "dias abstratos" ou "sprints" — `[uma semana]` é sinal de tarefa mal pensada, não de estimativa alta.
- Não aceite critério de aceitação subjetivo — "deve ficar bom" não é verificável; reescreva.
- Não misture múltiplas tarefas sob um título ("refatorar auth + adicionar OAuth + documentar" = 3 tarefas).
- Não pule a seção de dependências — dependência implícita é bloqueador futuro garantido.

## Expected Behavior

Depois do skill, o backlog do projeto contém tarefas onde cada uma pode ser pega por qualquer executor (humano ou agente) e concluída sem perguntar nada — porque título, critério de aceitação e dependências estão no próprio cartão.

Estimativas ganham feedback loop: no fim de cada tarefa, comparar estimado vs real é trivial (ambos usam a mesma unidade). Desvios >50% viram sinal de que o refinamento precisa melhorar.

## Quality Gates

- Todas as tarefas no `@TODO` têm os 5 campos preenchidos (Descrição, Critérios, Dependências, Recursos, Riscos) — não um "TODO: definir depois".
- Cada tarefa tem estimativa em `[15min / 1h / 4h / 1d]` — não há `[2 dias]`, `[1 semana]`, ou estimativa ausente.
- Critérios de aceitação são verificáveis por teste, comando ou checagem binária — não por "feeling".
- Nenhuma tarefa tem título começando com "refatorar", "melhorar", "otimizar" ou "estudar" sem qualificador específico.
- Dependências listadas apontam para tarefas reais do `@TODO` (ou `N/A` explícito), não ficam em branco.

## Companion Integration

- **Complementa** `sweng-todo-structure` — este skill define a **forma** de cada tarefa; o `@TODO` define a **organização** delas em seções (Em Progresso / Próximas / Backlog / Futuras / Concluídas).
- **Complementa** `sweng-progresso-atual` — `atual.md` rastreia execução de tarefas; só funciona bem se as tarefas estão bem definidas por este skill.
- **Pares com** `matilha:matilha-plan` — para features novas grandes, `/plan` gera SPs (tasks de alta granularidade com spec); para tarefas de manutenção/evolução do backlog diário, use este skill.
- Fases metodológicas: 20-30 (plan authoring, onde tarefas nascem), 40 (dispatch, onde estimativas imprecisas viram custo real).

## Output Artifacts

- Entradas no `@TODO` com template completo (5 campos + estimativa + prioridade).
- Opcional: planilha ou nota comparando estimado vs real para calibrar estimativas ao longo do tempo.
- Opcional: lista "a refinar" para tarefas que chegaram cruas mas ainda não foram convertidas.

## Example Constraint Language

- **Must**: 5 campos preenchidos; estimativa em unidade hora-sessão da tabela; critérios de aceitação verificáveis.
- **Should**: incluir riscos conhecidos; referenciar recursos com links diretos; priorizar com a classificação fixa.
- **May**: formato exato dos bullets (indentação, emoji opcional no título, ordem interna) — conteúdo é mandatório, apresentação é flexível.

## Troubleshooting

- **"Não consigo estimar, a tarefa é muito desconhecida"**: sinal claro de que a tarefa precisa de subtarefa `[1h]` de **spike / investigação** antes. Crie a spike com critério de aceitação "documento de 1 página descrevendo abordagem + nova estimativa da tarefa principal".
- **"Minhas estimativas erram sempre por muito"**: é problema de refinamento, não de chute. Tarefa com critério de aceitação claro estima melhor. Se desvio >50% é constante, o critério não está bom — refaça.
- **"Critério de aceitação fica sempre parecendo um teste"**: é isso mesmo. Idealmente cada critério é um teste automatizado. Se não dá, é um comando manual verificável ("executar `curl /auth` e retornar 200").
- **"Tenho tarefas antigas sem template"**: não tente migrar todas de uma vez. Aplique o skill apenas quando a tarefa for ser executada — tarefas no backlog profundo podem viver cruas até chegar a vez delas.
- **"O gerente insiste em estimar em semanas"**: traduza na entrega: `[1 semana] = 5 × [1d]`, então explicite quais 5 tarefas `[1d]`. Se não conseguir listar as 5, a semana não existe como estimativa, é wishful thinking.

## Concrete Example

**Bruta (ruim)**: "Refatorar autenticação — 1 semana."

**Refinada**:

```markdown
- [ ] [ALTA] Implementar autenticação JWT na API [4h]
  - **Descrição**: Criar middleware de autenticação usando JWT para proteger rotas /api/v1/*.
  - **Critérios de Aceitação**:
    * POST /auth/login com credenciais válidas retorna token JWT válido (teste integração passa).
    * GET /api/v1/users sem header Authorization retorna 401 (teste passa).
    * GET /api/v1/users com token expirado retorna 401 (teste passa).
  - **Dependências**: Modelo User concluído (TASK-042).
  - **Recursos**: [RFC 7519](https://tools.ietf.org/html/rfc7519), [jsonwebtoken docs](link).
  - **Riscos**: Configuração de chave RSA depende de acesso ao vault (bloqueador potencial).
```

A tarefa de "1 semana" virou uma tarefa de `[4h]` (a primeira). As outras 4 `[4h]` (refresh token, logout, docs OpenAPI, testes de integração) são novas entradas com o mesmo template. O total bate com a "semana" mas agora é executável em incrementos verificáveis.

## Sources

- `[[docs/rules/Gestão de Tarefas e Acompanhamento de Execução]]` — seções "## Padrões de Qualidade" (1. Definição de Tarefas + 2. Estimativas + 3. Prioridades) e "## Exemplos Práticos — ✅ Exemplo de Tarefa Bem Definida"
- Distillation: template de 5 campos + tabela fixa de estimativas são reproduzidas do rule; as anti-patterns e verbos vagos vêm da seção "Sinais de Alerta" ampliada para o contexto de AI-assisted dev.
