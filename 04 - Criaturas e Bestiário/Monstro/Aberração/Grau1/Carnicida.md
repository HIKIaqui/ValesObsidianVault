---
tags:
- Aberração
- Grau1
statblock: inline
---
**Tipo:** Aberração
**Grau de Perigo:** 1
# Descrição

Criatura média, híbrida de humano e cachorro. Se move em quatro patas/braços, com uma enorme boca cheia de dentes afiados no lugar da cabeça. Seu corpo é formado por carne e pele humana. Devido a diversas mutações, são cegas, se guiando por olfato, sons e vibrações.

Receberam esse nome por normalmente serem encontrados devorando restos mortais, como animais carniceiros.

Normalmente caçam em grupos, com um Carniceiro liderando-os.

>[!tip] Dica do Elgor  
Sem um Carniceiro, o grupo perde maior parte da letalidade, porque Carnicidas não sabem se coordenar sozinhos.


---
# Ficha

```statblock
layout: Vales Bestiário
name: Carnicida
tipo: Aberração
grau: 1
hp: 25
pe: 4
armadura: 0
esquiva: 15
atributos:
- 1
- 2
- 0
- 0
- 0
pericias:
- name: Perícias
  desc: |-
    - Percepção +10
    - Intimidação +5
    - Arrancada +5
    - Porrada +3
    - Reflexos +2
traits:
- name: Cego (Passiva)
  desc: Não recebe penalidade ou bônus por qualquer efeito relacionado a visão.
- name: Ecolocalização (Passiva)
  desc: Em local fechado, tem +1 Dado em testes de [[Mente|Percepção]].
actions:
- name: Mordida
  desc: Porrada | Precisão 0 | Dano 2d6 | Crítico 19+  | +1 Precisão contra alvos sofrendo [[Sangramento]].
- name: Rasgar
  desc: Porrada | Precisão 0 | Dano 2d4 | Crítico 20 | [[Sangramento]] 2 (Res. 12)
fraquezas:
- name: '[^1]**Limitação Natural'
  desc: '** Ao consumir biomassa pela segunda vez e vezes seguintes por cena, faz teste de [[Constituição|Resistência]]
    (Dif. 15) ou explode, espalhando suas entranhas no ambiente. Personagens no mesmo nível de distância recebem
    [[Cheiro Forte]] e testam [[Mente|Vontade]] (Dif. 15) ou recebem +1 Estresse e 1d4 de Dano Perfurante dos fragmentos
    de ossos arremessados.'
- name: Audição Sensível
  desc: Sensíveis a sons muito altos. Ações que emitam sons altos causam [[Atordoado]] 1 (Res. 15) no Carnicida.
- name: Maternidade
  desc: São mais sensíveis a figuras femininas. Desvantagem de -1 Dado em Social contra mulheres.
- name: '[^1]'
  desc: "Dependem de oferta constante de biomassa para que o corpo se mantenha estável. Senão despenca.\n\tCapacidade\
    \ limitada de armazenar biomassa. Acúmulo excessivo leva à instabilidade e possível ruptura do corpo, matando-o\
    \ no processo."
```
