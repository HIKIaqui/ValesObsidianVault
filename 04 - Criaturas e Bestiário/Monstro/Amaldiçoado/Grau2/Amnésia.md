---
tags:
- Amaldiçoado
- Grau2
statblock: inline
---
**Tipo:** Amaldiçoado
**Nível de Perigo:** 2

À primeira vista, uma Amnésia parece apenas um Esquecido magro, sem as garras normalmente encontradas nessas criaturas. Em seu lugar, possui dedos finos e anormalmente longos, alguns alcançando abaixo dos joelhos quando os braços estão abaixados.

Seu rosto nunca pode ser observado com clareza. Não parece coberto, deformado ou realmente desfocado; os olhos conseguem enxergá-lo, mas o cérebro não preserva aquilo que viu. Testemunhas são incapazes de concordar sobre suas feições mesmo enquanto continuam encarando a criatura.

O mesmo ocorre com seus movimentos. Amnésias não são particularmente ágeis, porém partes de suas ações parecem desaparecer da percepção. Observadores percebem que sua postura mudou sem conseguirem identificar quando ou como o movimento aconteceu.

Para atacar, a criatura apenas ergue um de seus dedos e aponta para o alvo. Energia de Maldição é concentrada na extremidade e lançada em forma de um feixe, ferindo a alma e interrompendo temporariamente a capacidade da vítima de compreender ou controlar as próprias ações.

Tentar acompanhar uma Amnésia provoca confusão crescente. Quanto mais alguém força a mente a manter aquela criatura como alvo, mais informações parecem desaparecer.

---
# Ficha

```statblock
layout: Vales Bestiário
name: Amnésia
tipo: Amaldiçoado
hp: 25
pe: 3
armadura: 0
esquiva: 10
atributos:
- 1
- 2
- 1
- 1
- 0
pericias:
- name: Perícias
  desc: |-
    - Resistência +10
    - Mira +5
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
- name: Feixe de Descontinuidade
  desc: Mira | Alcance 3 | 2d4 Maldição | Ao acertar, o alvo testa Vontade (Dif. 15). Se falhar, recebe [[Ruptura
    Cognitiva]].
fraquezas:
- name: Queima com Fogo
  desc: Sofre +1 Dado de Dano de Calor. Amnésias reduzidas a 0 PV por Dano de Calor não ativam **[[#^ExistenciaOdiosa|Existência
    Odiosa]]**.
```
