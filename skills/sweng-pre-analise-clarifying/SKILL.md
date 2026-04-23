---
name: sweng-pre-analise-clarifying
description: "Use when asked to review or analyze existing code/architecture before deep analysis — forces confirming problem, context, and constraints with the user to prevent premature review."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Fires quando o usuário pede "analisa esse código" / "revisa essa arquitetura" / "o que você acha dessa abordagem?" — **antes** de o agente mergulhar em sugestões. Fires também quando a análise em andamento começa a propor mudanças sem ter entendido por que a decisão atual foi tomada.

O problema-alvo é **review prematuro**: o agente lê o código, identifica "melhorias" óbvias (extrair função, trocar padrão, adicionar abstração) — e essas melhorias partem de contexto que o agente não tem. O código atual pode ser do jeito que é **porque** há uma restrição invisível: legado, contrato externo, decisão deliberada de simplicidade. Este skill força a pausa para diagnosticar antes de julgar.

## Preconditions

- Existe código ou decisão técnica real para analisar (não uma ideia hipotética).
- O usuário está disponível para responder perguntas rápidas antes da análise completa — não é um review assíncrono em PR.
- A análise ainda não começou (o skill perde valor se invocado depois de 3 recomendações já terem saído).

## Execution Workflow

1. **Pause a análise** — antes de ler uma segunda vez o código ou propor qualquer coisa, envie a mensagem de confirmação:
   ```
   Antes de analisar em profundidade, preciso confirmar:
   1. Qual o problema real que este código/design resolve?
   2. Qual o contexto de negócio ou histórico (legado, contrato, decisão deliberada)?
   3. Quais restrições conhecidas (performance, compatibilidade, time constraints)?
   4. Qual o critério de sucesso para a análise (está buscando quais tipos de problema)?
   ```
2. **Aguarde respostas** — análise "ok, mas vou começar enquanto isso" não funciona. A resposta do usuário tipicamente muda 30-50% do que seria recomendado.
3. **Registre as respostas** como premissas explícitas — vão entrar na seção Contexto da análise final (ver `sweng-analise-tecnica-estrutura`).
4. **Identifique restrições invisíveis** — decisões que pareciam erradas frequentemente viram "ah, isso é porque X" (integração com sistema legado, constraint regulatório, deadline). Registre.
5. **Declare explicitamente o escopo** — "vou analisar A e B; não vou analisar C porque está fora do contexto que você deu".
6. Só então **proceda para a análise estruturada** (`sweng-analise-tecnica-estrutura`).

## Rules: Do

- Sempre faça as 4 perguntas (problema real, contexto, restrições, critério) — mesmo que pareçam óbvias para você; "óbvio" é exatamente onde você erra.
- Registre premissas explícitas no documento de análise — permite auditoria depois ("a análise assumiu X; se X muda, recomendações mudam").
- Trate resposta "não sei" do usuário como dado importante — se o dono do código não sabe a restrição, a análise precisa ser mais conservadora.
- Quando o código parece obviamente errado, **dobre a suspeita** de que há contexto invisível. Código vive em produção; código obviamente errado que vive em produção normalmente não é obvioiamente errado.

## Rules: Don't

- Não pule esta etapa por pressa — o custo é 2-5 minutos; o benefício é análise que não vira retrabalho por faltar contexto.
- Não proponha soluções durante a fase de clarifying — se começou a listar "sugiro X", você saiu da fase.
- Não assuma que você sabe o problema real a partir só do código — sintomas e causas frequentemente divergem.
- Não trate a confirmação como burocrática ("respondo sim pra tudo") — as respostas do usuário são input real que muda a análise.
- Não reescreva código durante clarifying — nem sketches, nem exemplos. A fase é diagnóstica, não prescritiva.

## Expected Behavior

Depois do skill, a análise subsequente tem uma seção **Contexto** que cita as respostas do usuário como premissas — e quando a recomendação é "não mude", o racional está ancorado no que o usuário mesmo disse sobre restrições.

O usuário raramente sente que o review foi "off-base" — porque o skill captura as restrições no começo ao invés de descobri-las no meio de uma rodada de defesa.

## Quality Gates

- As 4 perguntas (problema real, contexto, restrições, critério) foram feitas **antes** de qualquer proposta de mudança.
- As respostas do usuário estão registradas no documento de análise como premissas explícitas.
- O escopo da análise está declarado ("analisei A, B; não analisei C porque fora do contexto").
- Nenhuma recomendação foi feita antes das respostas chegarem — o histórico do chat mostra perguntas antes de soluções.
- Quando uma restrição invisível foi descoberta (ex.: "ah, esse acoplamento é por causa do legado X"), ela está documentada como premissa.

## Companion Integration

- **Complementa** `matilha:matilha-plan` — `matilha-plan` é para features **novas** (spec authoring do zero); `sweng-pre-analise-clarifying` é para revisar **código existente** (diagnose before judge). Os dois resolvem problemas diferentes na mesma fase metodológica.
- **Pré-requisito de** `sweng-analise-tecnica-estrutura` — o skill de análise estruturada assume que o contexto foi confirmado; este skill é o passo prévio.
- **Aplica** `sweng-responsabilidade-tecnica` — princípio "Questione premissas" materializado em workflow concreto.
- Fases metodológicas: 10 (discovery/research, onde contexto é levantado), 50 (review, onde clarifying previne recomendações off-base).

## Output Artifacts

- Lista das 4 perguntas + respostas do usuário, salva na seção Contexto do documento de análise.
- Declaração explícita de escopo (o que entra e o que fica de fora).
- Opcional: nota sobre restrições invisíveis descobertas — útil em ADR ou onboarding futuro.

## Example Constraint Language

- **Must**: fazer as 4 perguntas antes de qualquer recomendação; registrar respostas como premissas; declarar escopo.
- **Should**: tratar "não sei" como dado; dobrar a suspeita quando o código "parece óbvio"; linkar premissas a recomendações futuras.
- **May**: formato exato das perguntas (adaptar linguagem ao domínio), ordem, uso de follow-ups rápidos.

## Troubleshooting

- **"O usuário responde sempre 'melhor você que ver'"**: insista em 1-2 linhas específicas. Se genuinamente não há contexto, declare isso na análise: "sem contexto de negócio fornecido; análise assume critério genérico de manutenibilidade".
- **"Minhas perguntas atrasam a review"**: 2-5 minutos de clarifying previne 1-2 rodadas de defesa depois. O cálculo favorece clarifying em qualquer análise não trivial.
- **"Já comecei a análise antes de invocar o skill"**: pare, volte para a fase de perguntas. Melhor recolocar trilhos agora do que entregar recomendações que o dono vai rejeitar.
- **"Parece óbvio o que tá errado, pra que perguntar?"**: exatamente o caso onde perguntar mais paga. Código em produção raramente tem erro óbvio não intencional.
- **"Isso vira interrogatório com o autor do código"**: tom importa. Perguntas começam com "para eu não propor algo fora do contexto, pode me confirmar..." — é cooperativo, não acusatório.

## Concrete Example

Usuário: "analisa esse método `calculate_price` que tá 200 linhas, com certeza precisa refatorar."

Agente **sem** o skill: "Sim, vou extrair as branches em métodos separados, aplicar strategy pattern..." → usuário: "ah, mas não podemos tocar aí, cada branch é uma lei fiscal estadual, foi auditado..."

Agente **com** o skill:
```
Antes de analisar, preciso confirmar:
1. Qual o problema real? (performance do método, bugs, dificuldade de manutenção, outro?)
2. Contexto: esse método toca regras de negócio externas (leis, contratos, auditoria)?
3. Restrições: tem ciclo de auditoria ou release que limite refatoração?
4. Critério: está buscando reduzir linhas, facilitar testes, algo específico?
```

Usuário responde: "(1) dificulta testar novos cenários; (2) sim, cada branch é uma lei estadual auditada; (3) sim, toda mudança passa por auditor fiscal, leva 3 semanas; (4) quero poder adicionar novas leis sem tocar nas antigas."

Análise que sai dali: **não** propõe strategy pattern (audit blocker). Propõe: extrair cada branch em função separada com mesmo nome e assinatura (auditor só revisa a função nova), adicionar tabela de despacho por estado, e sistema de testes parametrizado por lei. Zero mudança no comportamento das branches auditadas. Recomendação acionável porque respeita as restrições reais.

## Sources

- `[[docs/rules/Análise Crítica e Técnica Responsável]]` — seção "### 1. COMPREENSÃO PROFUNDA — **Antes de qualquer análise, confirme com o usuário**".
- Distillation: as 4 perguntas são ampliação prática do bloco de confirmação do rule original ("posso prosseguir com a análise completa?"), estendido para capturar especificamente problema, contexto, restrições e critério — os 4 eixos onde review prematuro falha.
