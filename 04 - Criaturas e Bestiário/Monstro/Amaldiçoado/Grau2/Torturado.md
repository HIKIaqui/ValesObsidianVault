---
aliases:
- Torturados
tags:
- Amaldiçoado
- Grau2
statblock: inline
---
**Tipo:** Amaldiçoado  
**Grau de Perigo:** 2

# Descrição

Um Torturado é um [[Esquecido]] profundamente transformado pela energia de [[Maldição]]. Seu corpo continua sofrendo mutações depois da primeira reanimação, tornando-se ainda mais monstruoso e especializado em causar sofrimento.

As enormes garras presentes em uma de suas mãos se fundem e crescem até formar uma única lâmina de osso. Espinhos também surgem por todo o corpo, rompendo a carne e tornando perigoso qualquer contato direto com a criatura.

Tudo que um Torturado devora é convertido em ácido dentro de seu organismo. A substância pode ser expelida contra suas vítimas, permitindo que a criatura ataque mesmo quando não consegue alcançá-las com a lâmina ou as garras.

---
# Ficha

```statblock
layout: Vales Bestiário
name: Torturado
tipo: Amaldiçoado
grau: 2
hp: 35
pe: 4
armadura: 2
esquiva: 12
atributos:
- 3
- 1
- 0
- 0
- 0
pericias:
- name: Perícias
  desc: |-
    - Resistência +10
    - Intimidação +7
    - Porrada +7
traits:
- name: Cérebro Morto (Passiva)
  desc: Não é afetado por testes sociais ou de Intimidação.
- name: Mente Colmeia (Passiva)
  desc: Amaldiçoados próximos, presentes na mesma cena ou área, compartilham seus sentidos. Se um deles enxergar
    um inimigo, todos sabem sua localização.
- name: Existência Odiosa (Passiva)
  desc: Ao ser reduzido a 0 PV, role 1d10. Se o resultado for maior que o dano total que o reduziu a 0 PV, o Torturado
    se levanta no próximo turno com 10 PV. Enquanto estiver caído, pode ser finalizado com uma ação, impedindo que
    se levante. ^ExistenciaOdiosa
- name: Espinhos Corporais (Passiva)
  desc: Ataques desarmados, contra-ataques ou agarrões contra o Torturado causam 1d6 de Dano em quem desferiu o
    golpe.
- name: Ódio Contagioso (Passiva)
  desc: Uma criatura morta por um Amaldiçoado se levanta no fim da cena como um Amaldiçoado. Humanos se transformam
    em [[Esquecido|Esquecidos]].
actions:
- name: Lâmina de Osso
  desc: Porrada | Precisão 0 | Dano 2d6 Cortante | Crítico 18+
- name: Garras Podres
  desc: Porrada | Precisão 0 | Dano 2d6 Cortante ou Perfurante | Crítico 19+
- name: Vômito Ácido
  desc: 2 PE | Mira | Alcance 1 | Precisão 0 | Dano 2d6 Químico | Crítico 20 | Queimadura (Resistência Dif. 10)
fraquezas:
- name: Queima com Fogo
  desc: Sofre +1 Dado de Dano de Calor. Torturados reduzidos a 0 PV por Dano de Calor não ativam **[[#^ExistenciaOdiosa|Existência
    Odiosa]]**.
```
