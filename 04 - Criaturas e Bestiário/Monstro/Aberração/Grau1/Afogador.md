---
tags:
- Aberração
- Grau1
statblock: inline
---
**Tipo:** Aberração
**Grau de Perigo:** 1
**Habitat:** Rios, lagos, canais, pântanos e regiões alagadas

# Descrição

O Afogador é uma criatura aquática de carapaça cinzenta semelhante a um caranguejo, com aproximadamente *50cm* de altura. Possui seis patas curtas e resistentes, além de vários tentáculos capazes de alcançar alguns poucos metros de distância.

Sua boca fica escondida na parte inferior do corpo e é formada por placas serrilhadas que se movem umas contra as outras, cortando e triturando matéria orgânica.

Afogadores passam grande parte do tempo imóveis no fundo da água, parcialmente enterrados ou cobertos por lodo. A coloração e o formato irregular da carapaça fazem com que sejam facilmente confundidos com pedras.

Quando uma criatura se aproxima da margem ou entra na água, o Afogador prende suas patas ao fundo e a alcança com os tentáculos. Sua estratégia não consiste em matar imediatamente, mas em arrastar a presa para uma região profunda e mantê-la submersa até que pare de resistir.

Depois disso, leva o cadáver até a boca sob seu corpo e começa a consumi-lo.

Apesar de perigosos dentro da água, Afogadores são consideravelmente menos eficientes em terra firme. Suas patas sustentam o corpo, mas seus movimentos são lentos e desajeitados fora de ambientes alagados.

São criaturas naturais relativamente comuns nos Vales. Muitos de seus ataques acabam atribuídos a afogamentos, especialmente quando o corpo da vítima nunca é recuperado.

---
# Ficha

```statblock
layout: Vales Bestiário
name: Afogador
tipo: Aberração
grau: 1
habitat: Rios, lagos, canais, pântanos e regiões alagadas
hp: 15
pe: 3
armadura: 3
esquiva: 10
atributos:
- 1
- 1
- 1
- 0
- 0
pericias:
- name: Perícias
  desc: |-
    - Técnica +3
    - Porrada +2
    - Furtividade +2
    - Resistência +2
traits:
- name: Garras Fixadoras (Passiva)
  desc: Recebe +5 Resistência para resistir a [[Movimento Forçado]].
- name: Camuflagem Natural (Passiva)
  desc: Recebe +5 Furtividade enquanto não notado pelo alvo e fora de combate.
actions:
- name: Arranhar
  desc: Porrada | Precisão 0 | Dano 2d4 | Crítico 20
- name: Envolver com Tentáculos
  desc: Técnica | Alcance 1 | Precisão 0 | Dano 1d6 | Crítico 20 |
- name: Afogar
  desc: Técnica | Alcance 1 | Precisão 0 | Dano 1d6 | Crítico 20 |
fraquezas:
- name: Desnorteado (Passiva)
  desc: Ao ser afetado por [[Movimento Forçado]], sofre [[Atordoado]].
- name: Ventre Protegido (Passiva)
  desc: Ataques Baixos contra o Afogador **Ignoram Armadura**, porém sofrem **-1 Precisão**. Caso o Afogador esteja
    [[Atordoado]], a penalidade de Precisão não se aplica.
```
