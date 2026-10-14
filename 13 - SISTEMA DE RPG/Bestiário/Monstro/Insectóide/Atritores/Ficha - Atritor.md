---
aliases:
- Ficha de Atritor
- Atritor no RPG
tags:
- Grau1
- Insectóide
- Atritor
statblock: inline
---

```statblock
layout: Vales Bestiário
name: Atritor
tipo: '[[Insectóide]]'
grau: 1
habitat: Cavernas e Ecossistemas Subterrâneos
nome_popular: Estalante (Chamado assim em lugares remotos)
hp: 25
pe: 2
armadura: 3
esquiva: 9
atributos:
- 1
- 1
- 1
- 1
- 0
pericias:
- name: Perícias
  desc: |-
    - Percepção +5
    - Reflexos +2
traits:
- name: Rede de Cliques (Passiva)
  desc: Para cada outro Atritor no campo de batalha, recebe +1 Porrada.
- name: (Nota 1) Formação Defensiva (Passiva)
  desc: Quando em combate e na mesma Casa que um ou mais aliados também com esta habilidade, estes se protegem.
    O dano recebido por um é dividido para todos com a habilidade, incluindo o que sofreu o golpe. A redução de
    armadura é aplicada para cada um dos atritores.
actions:
- name: Rasgar
  desc: Porrada | Precisão 0 | Dano 2d4 Cortante | Crítico 19+ | Recebe +1 Precisão para cada Atritor que atacou
    o alvo nesse turno.
- name: Mordida de Pressão
  desc: 2 PE | Porrada | Precisão -1 | Dano 2d4 Perfurante | Crítico 20 | Recebe +1 Precisão e +1d2 Dano para cada
    Atritor que atacou o alvo nesse turno.
fraquezas:
- name: Sensibilidade a Luz (Passiva)
  desc: É altamente sensível a luzes fortes (Recebe -5 para todos os testes quando sob efeito.).
- name: Nota 1
  desc: Do dano total, caso não possa ser dividido perfeitamente, o valor inteiro que sobrar após divisão é aplicado
    somente ao alvo original. Valores decimais são descartados
- name: Observações
  desc: Usar fogo, eletricidade, ou apenas atrapalhar sua comunicação são as melhores forma de lidar com Atritores.
    Em locais com muito barulho, eles acabam por ter dificuldade para se comunicar.
observacoes:
- name: Criatura no mundo
  desc: '[[04 - Criaturas e Bestiário/Monstro/Insectóide/Atritores/Atritor|Atritor]]'
```
