---
aliases:
- Ficha de Carniceiro
- Carniceiro no RPG
tags:
- Aberração
- Grau2
statblock: inline
---

```statblock
layout: Vales Bestiário
name: Carniceiro
tipo: Aberração
grau: 2
hp: 35
pe: 4
armadura: 0
esquiva: 15
atributos:
- 2
- 2
- 1
- 1
- 0
pericias:
- name: Perícias
  desc: |-
    - Percepção +10
    - Porrada +7
    - Intimidação +5
    - Reflexos +5
    - Arrancada +2
traits:
- name: Cego (Passiva)
  desc: Não recebe penalidade ou bônus por qualquer efeito relacionado a visão.
- name: Ecolocalização (Passiva)
  desc: Em local fechado, tem +1 Dado em testes de [[Mente|Percepção]].
- name: Coordenar a Carne (Passiva)
  desc: |-
    Todos os [[Carnicida|Carnicidas]] em combate recebem +1 Dado de Dano e, podem agir no mesmo turno que o Carniceiro ao invés de nos seus próprios.
    Se houver outro Carniceiro em cena, ambos se tornam agressivos um ao outro e são incapazes de coordenar os [[Carnicida|Carnicidas]], anulando este efeito enquanto ambos estiverem vivos.
actions:
- name: Rasgar
  desc: Porrada | Precisão 0 | Dano 2d6 | Crítico 20 | [[Sangramento]] 2 (Res. 15) | +1 Precisão contra alvos sofrendo
    [[Sangramento]].
- name: Absorção
  desc: 2 PE | Absorve carne de cadáveres e criaturas vivas. Se absorver um [[Carnicida|Carnicidas]] vivo, esse
    morre em seguida. Ao absorver, recebe +10 de PV máximos e recupera 10 PV. Seus ataques também recebem permanentemente
    +1d4 de dano. ^absorcao
fraquezas:
- name: (Nota 1) **Limitação Natural
  desc: '** Ao consumir biomassa pela segunda vez e vezes seguintes por cena, faz teste de [[Constituição|Resistência]]
    (Dif. 15) ou explode, espalhando suas entranhas no ambiente. Personagens no mesmo nível de distância recebem
    [[Cheiro Forte]] e testam [[Mente|Vontade]] (Dif. 15) ou recebem +1 Estresse e 1d4 de Dano Perfurante para cada
    vez que este usou Absorção nesta cena.'
- name: Audição Sensível
  desc: Sensíveis a sons muito altos. Ações que emitam sons muito altos causam [[Atordoado]] 1 (Res. 15) no Carniceiro.
- name: Maternidade
  desc: São mais sensíveis a figuras femininas. Desvantagem de -1 Dado em [[Social]] contra mulheres.
- name: Nota 1
  desc: "Dependem de oferta constante de biomassa para que o corpo se mantenha estável. Senão despenca.\n\tCapacidade\
    \ limitada de armazenar biomassa. Acúmulo excessivo leva à instabilidade e possível ruptura do corpo, matando-o\
    \ no processo."
observacoes:
- name: Criatura no mundo
  desc: '[[04 - Criaturas e Bestiário/Monstro/Aberração/Carnicidas/Carniceiro|Carniceiro]]'
```
