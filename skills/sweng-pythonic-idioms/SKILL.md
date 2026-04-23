---
name: sweng-pythonic-idioms
description: "Use when reviewing Python code, asking 'comprehension ou loop?', 'higher-order ou for explícito?', ou quando um for com append está ficando longo — ensina idiomas canônicos de controle e iteração."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Dispara em code review Python ou durante implementação quando a escolha
de idioma fica ambígua. Sinais típicos:

- "Comprehension ou loop explícito?"
- "Uso `map`/`filter` ou for?"
- "Preciso do índice no loop — `range(len(...))`?"
- "Esse if-else de 1 linha cabe em ternário?"
- "For com append virou 10 linhas, tem algo melhor?"
- "Code review Python — o que está fora do idiomatic?"

A regra: **prefira a forma Python-nativa quando ela é mais legível**.
Comprehensions > for-append manual; `enumerate()` > `range(len(x))`;
ternário > if-else longo para atribuição; funções de ordem superior
(`map`, `filter`, `reduce`) quando adequado. Legibilidade vence
esperteza.

## Preconditions

- Código é Python (3.9+ recomendado).
- A transformação que você está fazendo é **simples** — comprehension
  com 3+ filtros aninhados não é mais legível que for, ver troubleshooting.
- Há alternativa imperativa na mesa para comparar.

## Execution Workflow

1. Identifique o padrão na mão:
   - `for x in ys: result.append(f(x))` → list comprehension.
   - `for i in range(len(items)): ... items[i]` → `enumerate()`.
   - `if cond: x = a else: x = b` → ternário.
   - Acumulador com `for` e op associativa → `reduce()` ou `sum()`.
2. Tente a versão idiomática. Lê em voz alta. Se um iniciante em Python
   consegue ler em 5 segundos, venceu. Se não, recua.
3. Aplique o teste da legibilidade: comprehension com 2 filtros + 1
   map ainda está ok; comprehension aninhada + condicional complexo
   provavelmente vale mais como for explícito.
4. Prefira `sum()`, `max()`, `min()`, `any()`, `all()` antes de
   `reduce()`. A stdlib já tem o reduce mais comum pronto e nomeado.
5. Mantenha type hints nos callables de higher-order quando a
   legibilidade pede: `list(map(str.upper, names))` é claro;
   `list(map(lambda x: x.value * 2 if x.active else 0, items))` não —
   recua pra comprehension.
6. Em loops com lógica condicional real (múltiplos `if`, side effects,
   `break`/`continue`), mantém o `for` explícito. Ninguém ganha prêmio
   por espremer tudo em comprehension.

## Rules: Do

- Prefira list/dict/set comprehensions para transformações simples
  (1 loop, até 1 filtro, expressão curta).
- Use `enumerate(items)` quando precisar do índice. `range(len(items))`
  só quando o índice importa e o elemento não.
- Use ternário (`x if cond else y`) para atribuição condicional
  curta. Para blocos com múltiplas linhas, `if/else` normal.
- Aproveite `sum`, `max`, `min`, `any`, `all` antes de escrever
  redução manual.
- Use `map`/`filter` quando o callable é nomeado (`map(str.upper,
  names)`, `filter(is_active, users)`). Se o callable é lambda com
  lógica real, comprehension é mais legível.
- Mantenha condicionais simples sem chaves desnecessárias — Python
  já é enxuto, não adiciona ritual.

## Rules: Don't

- Não aninha comprehension de 3 níveis "porque couberam em uma
  linha". Uma linha ilegível é pior que 5 linhas claras.
- Não escreve `for i in range(len(items)): x = items[i]` — é
  anti-pattern clássico, usa `enumerate` ou itera direto.
- Não usa `reduce(lambda a, b: a + b, xs)` quando `sum(xs)` existe.
- Não transforma loop com side-effect em comprehension. Comprehension
  é pra valor; for é pra ação.
- Não mistura ternário dentro de comprehension dentro de expressão
  booleana — vira hieróglifo. Quebra em duas linhas.
- Não reescreve for explícito por vaidade de "ser mais pythônico". A
  métrica é legibilidade, não contagem de tokens.

## Expected Behavior

Após aplicar a skill, loops triviais ficam compactos e expressivos;
loops com lógica real ficam explícitos e auditáveis. Code review não
gasta tempo em "dá pra escrever com comprehension" porque a escolha
virou reflexo. Leitores novos entendem o fluxo sem precisar
decodificar indexação manual.

O codebase fica com uma textura consistente: map/filter/comprehension
aparecem onde fazem sentido, for aparece onde a lógica pede —
ninguém está espremendo nem evitando.

## Quality Gates

- Nenhum `for i in range(len(...))` em código novo (exceto casos onde
  só o índice importa).
- Transformações 1-para-1 (map puro) escritas como comprehension ou
  `map()` — não como for+append.
- Ternário usado apenas em atribuição simples; if/else para blocos.
- Comprehensions aninhadas limitadas a 2 níveis; acima disso,
  decompor em for explícito ou helper.
- Lambdas em `map`/`filter` só quando simples; lógica real volta pra
  comprehension ou função nomeada.

## Companion Integration

Pareia com `sweng-kiss-antidote-overengineering` — o idioma pythônico
é o caminho simples, não o caminho esperto. Comprehension espremida
viola KISS tanto quanto `for i in range(len())` viola idiomatic.

Complementa `sweng-roro-pattern` (funções bem-estruturadas ficam
mais fáceis de compor em `map`/`filter`) e `sweng-nomenclatura-clareza`
(callables nomeados em higher-order ficam auto-explicativos).

Em revisão de PR (phase 50), roda junto com `matilha:matilha-review`
e com review agents do `matilha-harness-pack:harness-review-agents-by-persona`
— especialmente o persona "reliability" que checa side-effects em
comprehensions.

## Output Artifacts

- Patch/PR com loops e condicionais reescritos no idioma apropriado.
- Comentário em revisão sinalizando o anti-pattern e propondo
  alternativa com uma linha de exemplo.
- Opcional: lint rule (ruff/pylint) ativando checks como `C0200`
  (use-of-range-len), `R1714` (consider-using-in), `C0209` (f-string
  over percent).

## Example Constraint Language

- Use "must" para: não usar `for i in range(len(x))` em código novo;
  não aninhar comprehension de 3+ níveis.
- Use "should" para: preferir comprehension em map puro; preferir
  `sum`/`any`/`all` sobre `reduce` quando aplicável.
- Use "may" para: uso de `map`/`filter` com callable nomeado (gosto
  pessoal vs comprehension); ternário em parâmetro de função vs
  variável intermediária.

## Troubleshooting

- **"Minha comprehension tá ilegível"**: isso é sinal pra recuar. Se
  precisou de comentário pra explicar, quebra em for explícito ou em
  funções auxiliares nomeadas.
- **"Preciso de índice E elemento"**: `for i, item in enumerate(items)`.
  Nunca `range(len())`.
- **"reduce com lambda tá parecendo feio"**: checa se não é `sum`,
  `max`, `min`, `any`, `all`, `math.prod`, `functools.reduce` com
  operator nomeado (`reduce(operator.add, xs)`). Quase sempre há
  versão mais limpa.
- **"Ternário ficou ruim no return"**: ternário em `return` curto é
  ok; se tem múltiplas condições, volta pra `if/return`.
- **"O agente reescreveu tudo em comprehension"**: checa side-effects.
  Agentes tendem a "pythonificar" agressivamente. Loops com log,
  escrita em disco, ou efeito externo devem continuar for explícito.

## Concrete Example

Antes:

```python
# Anti-pattern: range(len) + for-append manual + if-else verboso
squares = []
for i in range(len(numbers)):
    if numbers[i] > 0:
        squares.append(numbers[i] ** 2)

total = 0
for x in squares:
    total = total + x

label = ""
if total > 100:
    label = "high"
else:
    label = "low"
```

Depois (idiomático):

```python
squares = [x ** 2 for x in numbers if x > 0]
total = sum(squares)
label = "high" if total > 100 else "low"
```

Passou de 11 linhas ruidosas para 3 linhas que se leem em voz alta.
Nenhum índice manual, nenhum acumulador explícito, nenhum if-else
verboso — e nada foi "espremido" — a lógica está toda visível.

Caso oposto (mantém for explícito):

```python
# Aqui tem múltiplas ações por iteração + side-effect.
# Comprehension esconderia a intenção. For explícito vence.
for user in users:
    if user.is_expired:
        send_notification(user)
        log.info("notified %s", user.id)
        mark_processed(user)
```

## Sources

- [[docs/rules/Diretrizes de Qualidade de Código Python]]
- Destilado das seções "Estruturas de Controle" e "Padrões de
  Codificação" do rule file. Preserva os exemplos canônicos do
  documento original (list comprehension, `enumerate`, `reduce`,
  ternário) e a ordenação implícita: legibilidade > contagem de
  tokens > vaidade pythônica.
