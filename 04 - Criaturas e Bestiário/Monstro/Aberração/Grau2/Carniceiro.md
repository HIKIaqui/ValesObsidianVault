---
tags:
- Aberração
- Grau2
statblock: inline
---
**Tipo:** Aberração
**Grau de Perigo:** 2
# Descrição

Maior e mais inteligente que [[Carnicida|Carnicidas]] comuns, o Carniceiro é um [[Carnicida]] Alfa, capaz de emitir sons para liderar grupos desses monstros, coordenando ataques, o que aumenta muito sua letalidade.

Tendem a permanecer na retaguarda ao lutar, enquanto os [[Carnicida|Carnicidas]] fazem todo o trabalho sujo.

Diferente de [[Carnicida|Carnicidas]], Carniceiros não tem nenhum resquício de pele, e muitos normalmente andam sob duas pernas.

Podem absorver matéria orgânica, tornando-os maiores, mais perigosos, e curando-os de feridas.

Normalmente não se dão bem com outros Carniceiros.

>[!tip] Dica do Elgor  
Ao ser atacado por um grupo de Carnicidas, sempre foque em eliminar o Carniceiro antes dos outros, ou a situação pode escalar absurdamente.

---
# Ficha

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
- name: '[^1]**Limitação Natural'
  desc: '** Ao consumir biomassa pela segunda vez e vezes seguintes por cena, faz teste de [[Constituição|Resistência]]
    (Dif. 15) ou explode, espalhando suas entranhas no ambiente. Personagens no mesmo nível de distância recebem
    [[Cheiro Forte]] e testam [[Mente|Vontade]] (Dif. 15) ou recebem +1 Estresse e 1d4 de Dano Perfurante para cada
    vez que este usou [[#^absorcao|Absorção]] nesta cena.'
- name: Audição Sensível
  desc: Sensíveis a sons muito altos. Ações que emitam sons muito altos causam [[Atordoado]] 1 (Res. 15) no Carniceiro.
- name: Maternidade
  desc: São mais sensíveis a figuras femininas. Desvantagem de -1 Dado em [[Social]] contra mulheres.
- name: '[^1]'
  desc: "Dependem de oferta constante de biomassa para que o corpo se mantenha estável. Senão despenca.\n\tCapacidade\
    \ limitada de armazenar biomassa. Acúmulo excessivo leva à instabilidade e possível ruptura do corpo, matando-o\
    \ no processo."
```
