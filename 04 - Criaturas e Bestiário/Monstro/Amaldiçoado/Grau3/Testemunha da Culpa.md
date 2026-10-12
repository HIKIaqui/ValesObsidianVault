---
aliases:
- Testemunhas da Culpa
tags:
- Amaldiçoado
- Grau3
statblock: inline
---
**Tipo:** Amaldiçoado  
**Grau de Perigo:** 3

# Descrição

Uma Testemunha da Culpa é um cadáver desnutrido coberto por diversas mãos. Cada uma possui proporções e características diferentes, como se não pertencessem à mesma pessoa.

Sua cabeça não possui cabelos e é cercada por olhos que nunca piscam, constantemente vermelhos de agonia. Quando alguém encara a criatura diretamente, tanto seus olhos quanto os da vítima começam a sangrar.

A boca da Testemunha permanece sempre aberta e imóvel, mesmo quando a criatura fala, produz sons ou imita a voz de alguém importante para sua vítima. Sua presença força aqueles que a observam a confrontar os próprios [[Trauma|Traumas]], transformando culpa em uma pressão capaz de ferir, paralisar ou literalmente esmagar uma pessoa.

---
# Ficha

```statblock
layout: Vales Bestiário
name: Testemunha da Culpa
tipo: Amaldiçoado
grau: 3
hp: 65
armadura: 0
esquiva: 10
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
    - Percepção +7
    - Ocultismo +7
traits:
- name: Cérebro Morto (Passiva)
  desc: Não é afetada por testes sociais ou de Intimidação.
- name: Mente Colmeia (Passiva)
  desc: Amaldiçoados próximos compartilham seus sentidos. Se um deles enxergar um inimigo, todos sabem sua localização.
- name: Peso da Culpa (Passiva)
  desc: |-
    No início de cada turno, todos que estiverem encarando diretamente a Testemunha testam Vontade (Dif. 20). Ao falhar, o personagem recebe +1 Estresse para cada Trauma que possui.

    Se o Estresse do personagem atingir ou já estiver no nível máximo durante esse efeito, ele realiza imediatamente outro teste de Vontade:

    - **1 natural:** O personagem é literalmente esmagado pela própria culpa e morre de forma grotesca.
    - **2-7:** O personagem cai inconsciente com uma hemorragia interna causada por compressão psíquica. Sofre 3d6 de Dano que ignora Armadura, mas esse dano não pode reduzir seus PV abaixo de 1.
    - **8-15:** O personagem fica paralisado por `4 - Mente` turnos. Enquanto a Testemunha estiver viva, realiza um teste de Resistência com dificuldade igual a `5 × quantidade de Traumas` a cada turno. Se falhar, sofre 2d4 de Dano que ignora Armadura.
    - **16+:** Nada acontece. O personagem realiza seu teste de colapso normalmente.
actions:
- name: Imitar Voz
  desc: Alcance 2 | Imita a voz de alguém importante para o alvo, que testa Vontade (Dif. 20). Se falhar, recebe
    +1 Estresse e sofre 2d4 de Dano Vital.
fraquezas:
- name: Olhos Sensíveis
  desc: Uma fonte de luz forte ou mágica aproximada da Testemunha faz com que ela feche os olhos, desabilitando
    **Peso da Culpa**. A cada turno, porém, a criatura realiza um teste de Ocultismo (Dif. 15). Se passar, a fonte
    de luz se apaga subitamente.
- name: Vulnerabilidade Elemental
  desc: Sofre +1 Dado de Dano de Calor e Eletricidade.
```
