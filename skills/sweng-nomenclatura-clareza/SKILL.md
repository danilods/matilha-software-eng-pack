---
name: sweng-nomenclatura-clareza
description: "Use when naming variables, functions, classes, or files feels off — 'esse nome tá ruim', 'como chamo essa função?', 'data/info/temp são nomes?' — aplica convenções Python e faz o código auto-documentar."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Dispara em code review, durante implementação ou em refatoração quando
aparece um nome que não conta a história. Sinais típicos:

- "O nome dessa variável não tá bom."
- "Como eu chamo essa função?"
- "Tenho `data`, `info`, `temp`, `result` no mesmo arquivo."
- "Abreviação: `usr_cnt` ou `user_count`?"
- "Essa classe deveria ser verbo ou substantivo?"
- "Constante — onde declaro? Que caixa?"
- Variáveis como `x`, `d`, `obj` fora de contexto matemático.
- Funções como `process`, `handle`, `do_stuff` — que processam o quê?

A regra: **nomes revelam intenção**. Um bom nome remove a necessidade
de comentário. Convenções do documento de Danilo: `snake_case` para
funções/variáveis, `PascalCase` para classes, `UPPER_SNAKE` para
constantes, verbos auxiliares para booleanos (`is_active`,
`has_permission`). Sem abreviações desnecessárias.

## Preconditions

- Você está prestes a nomear (ou renomear) variável, função, classe,
  arquivo, constante, ou módulo.
- Linguagem é Python (princípio geral aplica em outras; convenções
  específicas variam — PascalCase em Go é diferente, por exemplo).
- Tem contexto do que a entidade faz — sem saber o que a função faz,
  nomenclatura não é o problema (é sweng-kiss ou scout).

## Execution Workflow

1. Classifica a entidade:
   - **Arquivo/diretório** → `snake_case.py`, `data_processing/`.
   - **Função/variável** → `snake_case`. Descritivo, indica propósito.
   - **Booleano** → verbo auxiliar: `is_active`, `has_permission`,
     `can_delete`, `should_retry`.
   - **Constante** → `UPPER_SNAKE`, declarada no topo do módulo:
     `MAX_RETRY_COUNT`, `DEFAULT_TIMEOUT`.
   - **Classe** → `PascalCase`. Substantivo, nunca verbo.
     `UserService`, `DataProcessor` — não `ProcessUser`.
2. Aplica o teste do iniciante: alguém novo no projeto consegue
   inferir o que a entidade faz só pelo nome? Se não, reescreve.
3. Remove abreviações desnecessárias. `calc` → `calculate_user_age`.
   `usr` → `user`. `cfg` só passa se for convenção do projeto
   (e mesmo assim, `config` é melhor).
4. Para função, o nome deve ser **verbo** — `calculate_user_age`,
   `fetch_metrics`, `send_email`. Se o nome é substantivo, ou é
   property/getter ou é nome errado.
5. Para variável, o nome deve ser **substantivo que descreva o
   conteúdo**. Não `data` — `user_records`. Não `result` —
   `total_metrics`. Não `temp` — nomeie o que é temporário.
6. Para booleano, o nome deve ler como pergunta ou afirmação:
   `if user.is_active:` lê melhor do que `if user.active:`
   (ambíguo: propriedade? método?).
7. Teste anti-"process": se está prestes a chamar algo
   `process_something`, pergunta *como* processa.
   `validate_user`, `normalize_email`, `persist_order` — cada um diz
   o que de fato faz.

## Rules: Do

- Use `snake_case` para módulos, funções, variáveis.
- Use `PascalCase` para classes; nomes são substantivos (`UserService`,
  não `ManageUser`).
- Use `UPPER_SNAKE` para constantes, declaradas no topo do módulo.
- Prefixe booleanos com verbo auxiliar: `is_`, `has_`, `can_`,
  `should_`, `will_`.
- Use nomes descritivos que indiquem propósito. `calculate_user_age`
  > `calc`. Variáveis devem explicar seu conteúdo.
- Pra função, nome com verbo que descreva a ação exata
  (`fetch`, `validate`, `normalize`, `persist`) — nunca genérico
  como `process`, `handle`, `do`.
- Consistência no projeto: se já existe `fetch_user`, o próximo é
  `fetch_order`, não `get_order`. Grepability vale dinheiro.

## Rules: Don't

- Não use variáveis como `x`, `d`, `obj`, `data`, `info`, `temp`,
  `result`, `item` fora de escopo minúsculo (comprehension, lambda,
  loop de 3 linhas). Em função pública, sempre nomeia.
- Não nomeia função com substantivo (`user_validation()` no lugar de
  `validate_user()`). Substantivo é classe ou variável.
- Não abrevie sem necessidade: `usr`, `cnt`, `desc`, `pwd`, `msg`.
  Editor tem autocomplete, ninguém economiza nada real em
  caracteres.
- Não use números no fim pra diferenciar (`user1`, `user2`,
  `process_data2`). Se precisa de 2, é sinal de que o design pede
  nomes reais (`user_source`, `user_target`).
- Não misture convenções no mesmo repo. Decidiu `snake_case` pra
  arquivos, não aparece `UserService.py` no meio.
- Não nomeia por implementação ("sql_user_list") — nomeia por
  propósito ("active_users"). Implementação muda; propósito dura
  mais.
- Não aceita nome do agente sem lê-lo em voz alta. Agentes tendem a
  usar `data`, `result`, `process`. Você renomeia antes de commitar.

## Expected Behavior

Após aplicar a skill, o código lê-se como prosa técnica. Uma pessoa
nova consegue navegar por nomes sem precisar abrir cada função pra
entender o que faz. Code review deixa de gastar ciclos em "esse nome
tá ruim" — os nomes já vêm bons.

Grep fica mais útil: `grep "fetch_"` lista todos os pontos de
entrada de dados; `grep "is_"` lista predicados; `grep "MAX_"` lista
limites. O codebase fica navegável por convenção.

## Quality Gates

- Nenhum arquivo novo em `CamelCase` ou `kebab-case` (Python: sempre
  `snake_case`).
- Nenhuma classe nomeada com verbo (`ProcessOrder` → `OrderProcessor`
  ou quebra em funções puras).
- Nenhum booleano sem prefixo `is_`/`has_`/`can_`/`should_`.
- Constantes no topo do módulo, em `UPPER_SNAKE`.
- Variáveis `data`/`result`/`temp`/`info` em escopo maior que 3
  linhas são renomeadas antes do merge.
- Funções novas passam no teste "o nome conta o que a função faz?"
  sem precisar abrir o corpo.

## Companion Integration

Pareia diretamente com `sweng-kiss-antidote-overengineering` — um
dos pilares de KISS no rule de Danilo é exatamente "nomes claros e
descritivos". Juntas: KISS diz para escolher a forma simples;
nomenclatura-clareza diz para batizar a forma escolhida de modo que
ela se explique.

Complementa `sweng-roro-pattern` (os campos do objeto de entrada/saída
precisam de nomes que revelem intenção) e `sweng-pythonic-idioms`
(higher-order com callable nomeado — `map(normalize_email, ...)` —
só funciona se `normalize_email` tiver nome bom).

Pareia com `sweng-readme-non-rotting`: README que não envelhece
assume nomes auto-documentados no código — senão precisa explicar
cada função, o que vira documentação que apodrece. Em phase 50,
roda junto com `matilha:matilha-review`.

## Output Artifacts

- Patch/PR com nomes renomeados OU nomes novos aprovados no review.
- Lista curta de anti-patterns encontrados (ex: "3 variáveis `data`
  no módulo `orders.py`") quando aplicada em auditoria.
- Opcional: regra de linter habilitada (ruff `N`-codes, pylint `C0103`).

## Example Constraint Language

- Use "must" para: `snake_case` para funções/variáveis;
  `PascalCase` para classes; `UPPER_SNAKE` para constantes; prefixo
  verbo auxiliar para booleanos.
- Use "should" para: evitar abreviações; preferir verbos específicos
  (validate/normalize/fetch) sobre genéricos (process/handle).
- Use "may" para: nomes curtos (`i`, `x`) em escopo minúsculo
  (comprehension, lambda, loop de 3 linhas); uso de abreviação
  convencional do domínio quando time inteiro reconhece.

## Troubleshooting

- **"Meu nome ficou longo demais"**: longo-mas-claro > curto-mas-
  ambíguo. `calculate_monthly_invoice_total` lê bem. Se passou de
  40 caracteres, talvez a função esteja fazendo demais (ver
  sweng-kiss).
- **"Não consigo pensar num nome bom"**: sinal de que a função não
  tem responsabilidade clara. Se você não consegue nomear, você
  provavelmente não entende o que ela faz — volta pra KISS.
- **"Agente nomeou tudo como `data`, `result`, `process`"**: esperado.
  Agentes usam nomes genéricos. Varre o patch e renomeia antes de
  commitar.
- **"No legado tem `usr_cnt`, mantenho a convenção?"**: renomeia à
  medida que toca. Não faz renomeação big-bang, mas não propaga
  nome ruim em código novo.
- **"Boolean `active` funciona, preciso mesmo do `is_`?"**: sim. `if
  user.active` pode ser propriedade, método, ou atributo ambíguo.
  `if user.is_active` é inequívoco.

## Concrete Example

Antes (tudo ambíguo):

```python
MAX = 5

class ProcessUser:
    def do(self, d):
        r = []
        for i in range(len(d)):
            if d[i]["a"]:
                r.append(d[i])
        return r
```

Problemas: constante sem nome, classe com verbo, método `do`,
parâmetro `d`, variável `r`, campo `"a"` opaco, `range(len)` genérico.

Depois (nomes revelam intenção):

```python
MAX_ACTIVE_USERS = 5

class ActiveUserFilter:
    def filter_active(self, users: list[dict]) -> list[dict]:
        return [user for user in users if user["is_active"]]
```

- `MAX_ACTIVE_USERS`: constante em UPPER_SNAKE, nomeia o que limita.
- `ActiveUserFilter`: PascalCase, substantivo — descreve o que É.
- `filter_active`: verbo — descreve o que FAZ.
- `users: list[dict]`: parâmetro diz o que recebe.
- `is_active`: booleano com prefixo — lê como afirmação.
- Comprehension limpa (pareia com sweng-pythonic-idioms).

## Sources

- [[docs/rules/Diretrizes de Qualidade de Código Python]]
- Destilado da seção "Convenções de Nomenclatura" do rule file de
  Danilo. Preserva as quatro regras canônicas (arquivos, funções,
  constantes, classes) e o princípio raiz: nomes descritivos que
  indicam propósito, sem abreviações desnecessárias. Referência
  cruzada ao princípio KISS original: "Prefira `calculate_user_age()`
  ao invés de `calc()`".
