---
aliases:
- Esquecidos
tags:
- Amaldiçoado
- Grau1
statblock: inline
---
**Tipo:** Amaldiçoado  
**Grau de Perigo:** 1

# Descrição

Quando alguém morre deixando assuntos inacabados, sua alma pode permanecer no mundo como um fantasma. Porém, quando uma pessoa é morta por um monstro, a energia de [[Maldição]] pode aprisionar sua alma no próprio cadáver e transformá-la em um Esquecido. Cadáveres abandonados em regiões com grandes concentrações dessa energia também podem sofrer o mesmo processo.

Esquecidos vagam sem propósito, movidos por um ódio que já não conseguem compreender. Incapazes de raciocinar sobre aquilo que sentem, atacam, matam e devoram tudo que encontram pelo caminho.

Aqueles que são mortos por Esquecidos também se tornam criaturas da mesma espécie, independentemente de possuírem assuntos inacabados. Ao se levantarem, compartilham o mesmo ódio que animava seu agressor, conectando-se aos demais Amaldiçoados através de uma espécie de mente colmeia.

---
# Ficha

```statblock
layout: Vales Bestiário
name: Esquecido
tipo: Amaldiçoado
grau: 1
hp: 25
armadura: 0
esquiva: 7
atributos:
- 2
- 0
- 0
- 0
- 0
pericias:
- name: Perícias
  desc: |-
    - Resistência +10
    - Intimidação +5
    - Porrada +5
traits:
- name: Cérebro Morto (Passiva)
  desc: Não é afetado por testes sociais ou de Intimidação.
- name: Mente Colmeia (Passiva)
  desc: Amaldiçoados próximos, presentes na mesma cena ou área, compartilham seus sentidos. Se um deles enxergar
    um inimigo, todos sabem sua localização.
- name: Existência Odiosa (Passiva)
  desc: Ao ser reduzido a 0 PV, role 1d10. Se o resultado for maior que o dano que o reduziu a 0 PV, o Esquecido
    se levanta no próximo turno com 10 PV. Enquanto estiver caído, pode ser finalizado com uma ação, impedindo que
    se levante. ^ExistenciaOdiosa
- name: Ódio Contagioso (Passiva)
  desc: Uma criatura morta por um Amaldiçoado se levanta no fim da cena como um Amaldiçoado. Humanos se transformam
    em Esquecidos.
actions:
- name: Garras Podres
  desc: Porrada | Precisão 0 | Dano 2d6 Cortante ou Perfurante | Crítico 19+
fraquezas:
- name: Queima com Fogo
  desc: Sofre +1 Dado de Dano de Calor. Esquecidos reduzidos a 0 PV por Dano de Calor não ativam **[[#^ExistenciaOdiosa|Existência
    Odiosa]]**.
```
