---
name: sweng-kiss-antidote-overengineering
description: "Use when code is starting to look over-engineered, when tempted to add abstractions 'pro futuro', or when asking 'preciso mesmo dessa camada?' — recenters on simplicidade primeiro."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Dispara quando você (ou um agente) está prestes a introduzir complexidade
que o problema atual não pediu. Sinais típicos:

- "Esse código parece over-engineered?"
- "Preciso mesmo dessa abstração?"
- "Tá ficando complexo demais."
- "Vou deixar preparado pro futuro."
- "E se um dia precisarmos de outro backend? melhor já fazer uma interface."
- Estou criando uma classe pra embrulhar duas funções.
- Já tem três níveis de herança e o caso real é um só.

A regra de fundo é simples: **SIMPLICIDADE SEMPRE EM PRIMEIRO LUGAR**.
Se existe uma forma direta e óbvia, usa ela. Complexidade entra depois,
quando o problema real pedir — não por antecipação.

## Preconditions

- Você está prestes a escrever código, revisar um PR, ou aceitar uma
  sugestão do agente que introduz estrutura (classe, padrão de design,
  camada, flag de config, parametrização) que não resolve dor concreta
  agora.
- O caso de uso atual é conhecido (se não for, o problema não é
  over-engineering — é falta de especificação; vai pro scout/plan).
- Existe uma alternativa mais simples na mesa ou imaginável (função pura,
  dict, list, stdlib) — senão não há o que recuar.

## Execution Workflow

1. Nomeie a complexidade que você está prestes a adicionar. "Vou criar
   uma `UserRepository` abstrata com duas implementações." "Vou extrair
   um strategy pattern." "Vou parametrizar isso com três flags de
   config." Escreve em voz alta.
2. Pergunte: **qual dor concreta de hoje essa estrutura resolve?** Se a
   resposta for "prepara pro futuro" ou "se um dia precisarmos", é
   alerta. "Preparação para o futuro excessiva" é um dos sinais
   vermelhos canônicos.
3. Proponha a versão direta: uma função, um dict, uma chamada à stdlib.
   Estima quantas linhas são. Se a versão simples couber em 10-30
   linhas e resolve o caso conhecido, ela vence.
4. Aplica as Regras Fundamentais como checklist:
   - Solução direta e óbvia? (se sim, usa)
   - DRY sem inventar: existe já algo similar no repo? (procura antes
     de criar)
   - Uma responsabilidade por função/classe — se precisa de "e" pra
     explicar, provavelmente está fazendo demais.
   - Nomes claros — `calculate_user_age()` no lugar de `calc()`.
5. Se ainda parece necessário complicar, documente a decisão curta
   ("escolhi X porque Y") e segue. Se não parece, recua para a versão
   simples.
6. Revisita quando o próximo caso de uso chegar. A abstração emerge do
   segundo ou terceiro caso real, não do primeiro imaginado.

## Rules: Do

- Comece simples: implementa o caso básico primeiro. Evolui gradualmente
  quando o problema real chegar.
- Prefira funções puras, estruturas de dados básicas (dict, list, tuple),
  e bibliotecas padrão do Python. Código que um iniciante consiga
  entender envelhece melhor.
- Uma responsabilidade por função/classe. Se o nome precisa de "e" pra
  descrever o que faz, quebra em duas.
- Nomes descritivos: `calculate_user_age` > `calc`; `has_permission`
  > `perm_flag`. Variáveis devem explicar o próprio conteúdo.
- Documenta decisão de recuar da complexidade — por quê a versão simples
  venceu. Isso protege contra o próximo reviewer (humano ou agente)
  tentar "melhorar" de volta.
- DRY com critério: procura se já existe algo similar antes de criar
  função/classe nova.

## Rules: Don't

- Não cria abstrações complexas para casos simples. Pattern + classe +
  interface para duas funções puras é carga sem retorno.
- Não usa padrões de design desnecessários. Factory, strategy, visitor
  aparecem quando o código pede — nunca quando o pattern está bonito
  no livro.
- Não faz "preparação para o futuro" excessiva. O futuro geralmente
  pede algo que você não previu; a abstração antecipada raramente
  encaixa.
- Não cria hierarquias de classes profundas. Dois níveis já é um cheiro;
  três é alerta; quatro quase sempre é indício de que funções puras
  resolviam.
- Não otimiza prematuramente. Legibilidade primeiro; performance quando
  houver métrica real.
- Não aceita sugestão de agente que adiciona camada sem justificar a
  dor concreta. O agente tende a sofisticar porque o treinamento
  premia sofisticação — o harness humano é quem segura a régua.

## Expected Behavior

Após aplicar a skill, a decisão de complexidade fica explícita. Se a
versão simples vencer, o PR (ou patch do agente) fica menor, mais
legível, e auditável por quem não viveu a conversa. Se a versão
complexa vencer, ela vem com uma frase curta justificando a dor real
que resolve.

O resultado de longo prazo: o codebase acumula menos "código morto
estrutural" (classes/interfaces que nunca foram usadas pro que
imaginaram). Cada abstração viva tem pelo menos dois ou três casos
reais que a justificaram.

## Quality Gates

- Para cada estrutura nova (classe, abstração, camada, flag de config)
  existe uma dor concreta de hoje documentada — não só "pro futuro".
- A versão simples foi pelo menos esboçada e comparada antes da versão
  complexa ser aceita.
- Função/classe passa no teste do "e" — se o nome precisa de "e",
  refatora antes de mergear.
- Nomes passam no teste do iniciante — alguém novo no projeto consegue
  ler sem decodificar abreviações.
- Nenhum novo padrão de design entra sem uma frase escrita explicando
  qual problema concreto ele resolve neste repo.

## Companion Integration

**Complementa `matilha-harness-pack:harness-code-is-free`** por outro
ângulo. `harness-code-is-free` afirma que produção de código ficou
barata (AI escreve rápido, então planeje P3s, migrações travadas,
padronização em massa). `sweng-kiss-antidote-overengineering` mantém
a disciplina arquitetural humana: só porque é barato escrever não
significa que vale escrever. Simplicidade é valor humano — o harness
fornece capacidade de implementar, a disciplina KISS fornece o filtro
do que vale ser implementado.

Fluxo típico: você decide *o quê* fazer com KISS (filtro humano), e
*quanto fan-out* com code-is-free (capacidade agêntica). Pareia também
com `sweng-nomenclatura-clareza` (nomes claros são parte do contrato
de simplicidade) e com `matilha:matilha-review` em phase 50.

## Output Artifacts

- Versão simplificada do patch/PR (menos linhas, menos camadas).
- Commentário curto justificando recuo da complexidade OU justificativa
  da complexidade mantida — qualquer um dos dois, nunca silêncio.
- Opcional: entrada em `docs/decisions/` quando a decisão for grande o
  bastante pra virar ADR.

## Example Constraint Language

- Use "must" para: documentar a dor concreta ANTES de aceitar qualquer
  camada/abstração nova; aplicar o teste do "e" em nomes de função.
- Use "should" para: preferir funções puras e stdlib a classes
  customizadas; esboçar a versão simples antes da complexa.
- Use "may" para: qual notação de docstring usar, onde exatamente
  registrar a decisão (commit message, ADR, comentário inline).

## Troubleshooting

- **"Mas um dia vamos precisar"**: não é justificativa. Ou a dor existe
  agora, ou a abstração emerge quando existir. Abstração antecipada
  quase nunca encaixa no formato que o futuro pede.
- **"O agente sugeriu, parece idiomático"**: agentes tendem a gerar
  padrões porque o treinamento recompensa padrões. Pergunta qual dor
  resolve no SEU caso. Se não tem resposta concreta, recua.
- **"Já tem 3 classes, refatorar dá trabalho"**: se são 3 classes para
  um problema de 1 função pura, o trabalho de refatorar agora é menor
  do que o trabalho de carregar o peso depois.
- **"Mas eu gosto do padrão, fica elegante"**: elegância é métrica
  pessoal; legibilidade pra equipe é métrica compartilhada. Simples
  ganha no longo prazo.

## Concrete Example

Agente propõe criar `class NotificationSender` com métodos
`send_email`, `send_sms`, `send_push`, cada um chamando um provider
interno via strategy pattern. O caso de uso atual: mandar UM email de
confirmação de signup.

Aplicando KISS:

- Dor concreta de hoje: mandar email de confirmação. Uma função.
- Versão simples: `def send_signup_email(user: User) -> None` chamando
  a lib de email diretamente. 8 linhas.
- Versão complexa proposta: 4 classes, 1 interface, strategy pattern.
  60+ linhas, 2 arquivos.

Decisão: recua pra versão simples. Quando o SMS chegar (se chegar), aí
avalia se vale extrair um protocolo comum — provavelmente vai. Ou
provavelmente o SMS vai pedir um formato totalmente diferente e a
"preparação" teria atrapalhado.

Commit message: `feat(auth): send_signup_email como função pura; extrair
protocolo quando o 2º canal real chegar`.

## Sources

- [[docs/rules/Princípios KISS - Keep It Simple, Stupid]]
- Síntese direta da regra do Danilo em `/Memory/docs/rules/`. Preserva
  o princípio raiz ("SIMPLICIDADE SEMPRE EM PRIMEIRO LUGAR"), as quatro
  Regras Fundamentais (solução direta, DRY, uma responsabilidade,
  nomes claros) e os Sinais de Alerta do documento original.
