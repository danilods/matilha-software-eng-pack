---
name: sweng-roro-pattern
description: "Use when a function has many positional args, when the return is a tuple of 3+ things, or when asking 'como estruturo o input/output dessa função?' — aplica RORO (Receive an Object, Return an Object)."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Dispara quando a assinatura de uma função está ficando pesada ou a
ergonomia do call-site está frágil. Sinais típicos:

- "Função com muitos parâmetros posicionais."
- "Estou voltando uma tuple de 3 ou 4 coisas."
- "Preciso adicionar mais um parâmetro e vai quebrar todo mundo que
  chama."
- "Como estruturar o return?"
- A chamada virou `process(a, b, c, True, None, 0.5, False)` e ninguém
  lê mais isso.
- Toda vez que adiciono campo no retorno, quebra unpacking em 7 lugares.

A regra: **RORO — Receive an Object, Return an Object**. Em vez de
múltiplos argumentos posicionais e tuplas de retorno, receba e devolva
estruturas nomeadas (dict validado ou Pydantic/dataclass). Facilita
extensibilidade e manutenção.

## Preconditions

- Função tem 3+ parâmetros OU retorna 2+ valores relacionados.
- Linguagem é Python (ou adaptação com tipos nomeados em outra linguagem).
- Há caller real (interno ou externo) — RORO numa função com 1 caller
  trivial pode ser exagero; ver troubleshooting.
- Você controla a assinatura (não é uma função de biblioteca externa
  travada).

## Execution Workflow

1. Olhe a assinatura atual. Conte parâmetros posicionais e valores
   retornados. Se passou de 3 de cada lado, RORO entra em cena.
2. Escolha a estrutura de entrada:
   - **Pydantic BaseModel** quando precisa de validação (entrada de
     API, dados externos, user input).
   - **Dataclass** quando é objeto interno simples sem validação
     externa.
   - **dict tipado (TypedDict)** quando quer manter leveza mas dar
     nome aos campos.
3. Escolha a estrutura de saída com os mesmos critérios. Frequentemente
   dict é suficiente no retorno; Pydantic quando o consumidor vai
   serializar ou validar.
4. Reescreva a função. Antes: `def f(a, b, c, d) -> tuple[x, y, z]`.
   Depois: `def f(params: Params) -> Result`. Os campos passam a ter
   nome; extensão não quebra call-sites nem unpacking.
5. Atualize os call-sites. Se forem muitos, considera fan-out agêntico
   (ver companion integration). Se forem poucos, troca direto.
6. Documente o contrato (docstring): campos obrigatórios, opcionais,
   exceções. O objeto nomeado já faz metade da documentação sozinho.
7. Testa o caminho feliz e um caminho de campo faltando — se for
   Pydantic, a validação captura; se for dict, adicionar guard.

## Rules: Do

- Receba dict validado / Pydantic / dataclass quando a função tem 3+
  parâmetros. Nomear os campos vale mais do que a economia de
  tipagem.
- Retorne dict / Pydantic / dataclass quando devolver 2+ valores
  relacionados. Tuple posicional é fonte clássica de bug na hora de
  adicionar o 4º campo.
- Prefira Pydantic quando entra dado externo (API, config, user input)
  — validação no boundary pega erro cedo.
- Valide entrada no início da função. Fail fast — melhor errar na
  porta de entrada do que 20 linhas depois com `KeyError` obscuro.
- Adicione type hints em todas as assinaturas. RORO sem type hints
  perde metade do valor.
- Mantenha o objeto de entrada **plano e específico da função**. Não
  mistura "objeto monolítico que passa por todo lado" com RORO — isso
  vira God Object.

## Rules: Don't

- Não aplica RORO em função trivial de 1-2 argumentos e 1 retorno.
  `def square(x: int) -> int` não precisa de `SquareInput`/`SquareResult`.
  Isso vira ritual sem ganho.
- Não use tupla posicional para retorno multivalor que o caller vai
  desestruturar em vários lugares. A primeira extensão do retorno
  quebra tudo.
- Não reutiliza o mesmo objeto de entrada entre funções não
  relacionadas. O objeto é da função, não do módulo.
- Não esconde complexidade — se o objeto de entrada tem 15 campos
  opcionais, a função está fazendo demais (ver sweng-kiss).
- Não omite a docstring achando que o Pydantic "se explica". O objeto
  nomeia; a docstring explica por que existe e o que a função faz
  com ele.

## Expected Behavior

Após aplicar RORO, adicionar campo de entrada/saída deixa de ser
breaking change. Chamadas ficam auto-documentadas
(`calculate_metrics(data=payload)` em vez de `calculate_metrics(x, y, z,
True)`). IDEs (e agentes) dão completion correto porque os campos têm
nome. Code review acelera — o reviewer não precisa caçar a ordem dos
argumentos.

Testes também ficam mais claros: `Params(name="x", age=30)` conta a
história sozinho; `process("x", 30)` não.

## Quality Gates

- Nenhuma função pública nova com 4+ argumentos posicionais sem
  justificativa.
- Nenhum retorno novo como `tuple[X, Y, Z]` quando os 3 têm nomes
  naturais (`average`, `total`, `count`).
- Entrada de API / CLI / boundary externo: Pydantic ou equivalente
  com validação, não dict cru.
- Call-sites atualizados passam type check (mypy/pyright).
- Docstring explica o que a função faz e referencia o modelo de
  entrada.

## Companion Integration

Complementa `sweng-kiss-antidote-overengineering` — RORO é o inverso
de ritual desnecessário: aplica quando a dor (args posicionais, tuple
quebrável) é concreta. Não aplica em função trivial.

Pareia com `sweng-pythonic-idioms` (tipo de retorno afeta como você
compõe o próximo map/filter/comprehension) e com
`sweng-nomenclatura-clareza` (os campos do objeto RORO precisam de
nomes que revelem intenção — `is_active`, `has_permission`,
`user_age`).

Quando precisar converter muitas funções legadas de estilo posicional
para RORO, pareia com `matilha-harness-pack:harness-code-is-free`
(fan-out agêntico) e com `matilha:matilha-gather` para mergear os
patches.

## Output Artifacts

- Função com assinatura `def f(params: Params) -> Result`.
- Modelo de entrada (Pydantic/dataclass/TypedDict) declarado no módulo
  ou em `models.py` se reutilizado.
- Modelo de saída quando 2+ valores nomeados.
- Docstring Google-style com `Args`, `Returns`, `Raises`.
- Testes atualizados usando o objeto nomeado.

## Example Constraint Language

- Use "must" para: aplicar RORO quando função pública tem 4+ args OU
  retorna 2+ valores relacionados; validar entrada externa com
  Pydantic no boundary.
- Use "should" para: preferir Pydantic sobre dict em entrada; preferir
  dataclass em objetos internos sem validação externa.
- Use "may" para: TypedDict como alternativa leve quando não precisa
  de runtime validation; retorno como Pydantic quando o consumidor
  imediato vai serializar.

## Troubleshooting

- **"Minha função só tem 2 args, preciso aplicar RORO?"**: não. RORO
  serve dor concreta (args demais, tupla quebrável). 2 args nomeados
  resolvem.
- **"Ficou verboso, agora tenho modelo só pra chamar uma função"**:
  verifica se o modelo não está sendo reutilizado em outros lugares —
  se não está, RORO provavelmente é exagero para esse caso. Recua.
- **"Pydantic é pesado pra um caso interno"**: use dataclass ou
  TypedDict. Pydantic quando o dado cruza boundary; dataclass quando
  é só interno.
- **"Tenho 20 call-sites legados em tuple posicional"**: fan-out
  agêntico — um agente faz a refatoração por arquivo, você revisa os
  patches em lote. Veja `matilha-harness-pack:harness-code-is-free`.
- **"O objeto de entrada virou um God Object"**: a função está
  fazendo demais. Quebra em duas, cada uma com seu objeto menor.

## Concrete Example

Antes:

```python
def calculate_average_and_total(values):
    average = sum(values) / len(values)
    total = sum(values)
    return average, total

avg, tot = calculate_average_and_total([1, 2, 3])
```

Agora quero adicionar `median`. Tenho que mudar todo call-site que
desempacota a tupla.

Depois (RORO):

```python
def calculate_metrics(data: dict) -> dict:
    values = data["values"]
    return {
        "average": sum(values) / len(values),
        "total": sum(values),
    }

metrics = calculate_metrics({"values": [1, 2, 3]})
print(metrics["average"], metrics["total"])
```

Adicionar `median`: uma linha no retorno, zero call-site quebra.

Versão com Pydantic (entrada externa validada):

```python
class MetricsInput(BaseModel):
    values: list[float]

class MetricsResult(BaseModel):
    average: float
    total: float

def calculate_metrics(payload: MetricsInput) -> MetricsResult:
    return MetricsResult(
        average=sum(payload.values) / len(payload.values),
        total=sum(payload.values),
    )
```

## Sources

- [[docs/rules/Diretrizes de Qualidade de Código Python]]
- Destilado da seção "Padrão RORO (Receive an Object, Return an
  Object)" do rule file de Danilo. Preserva o exemplo canônico
  (`calculate_metrics` vs `calculate_average_and_total`) e a
  justificativa original — facilita extensibilidade e manutenção.
