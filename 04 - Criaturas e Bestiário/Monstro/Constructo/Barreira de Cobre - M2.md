---
tags:
- Constructo
- Grau3
statblock: inline
---
**Tipo:** Constructo
**Grau de Perigo:** 3

> [!note] Repetições a confirmar
> Metralhadora de Mana diz "Repete esse ataque 3 vezes". Confirmar se são três disparos totais ou três repetições além do primeiro antes de calcular o dano médio total da ação.

# Descrição

O Barreira de Cobre – M2 é a evolução direta do modelo básico M1. Criado pelo Reino de Malaquita para suprir a escassez de soldados humanos durante a Guerra, seu design prioriza:

- fogo supressivo constante;
- defesa de corredores estreitos;
- eliminação rápida de múltiplos alvos;
- manutenção mínima.

O corpo mantém a silhueta esquelética metálica dos modelos anteriores, composto de placas de cobre e madeira reforçada, com tendões orgânicos ancorados em articulações biomecânicas. A principal alteração é no braço direito, substituído por um Canhão Cinético de Mana, uma arma viva com tambor rotatório interno capaz de disparar centenas de projéteis por minuto. O núcleo de mana de madeira, preso ao centro do peito, pulsa em alta frequência durante o disparo, emitindo o som característico: tic-tac-tac-tac-tac-TAC-TAC-TAC-TAC No braço esquerdo, o escudo também foi aumentado, agora sendo grande o suficiente para fornecer cobertura quase completa.

Nunca desistem ou recuam em combate. Tentar dialogar com um deles é completa loucura.

---
# Ficha

```statblock
layout: Vales Bestiário
name: Barreira de Cobre - M2
tipo: Constructo
grau: 3
hp: 45
pe: Não informado no livro.
armadura: 5
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
    - Percepção +5
    - Reflexos +5
    - Mira +5
traits:
- name: Feito de Metal (Passiva)
  desc: +3 de Armadura contra golpes cortantes.
- name: Sem Vontade (Passiva)
  desc: Não é afetado por emoções ou qualquer tipo de manipulação. Não recua.
- name: Escudo Pesado (Passiva)
  desc: +2 de Armadura contra projéteis.
actions:
- name: Disparo de Mana Preciso
  desc: Mira | Precisão +1 | Dano 2d10 | Crítico 18+ | Alcance 4
- name: Metralhadora de Mana
  desc: Mira | Precisão 0 | Dano 2d6 | Crítico 20 | Alcance 2 | Repete esse ataque 3 vezes. | Seu corpo superaquece.
    Ele mesmo sofre Atordoado 1.
- name: Pancada com Escudo
  desc: Porrada | Precisão 0 | Dano 2d6 | Crítico 20 | Inimigo testa Resistência (Dif. 20) ou é empurrado 1 nível
    de distância e sofre Atordoado 1.
fraquezas:
- name: Curto e Congelamento
  desc: Eletricidade pode dar curto nele, e Gelo pode congelar seus mecanismos. Causando Atordoado 1 se ele falhar
    em teste de Resistência (Dif. 20)
- name: Autoridade de Malaquita
  desc: Insígnias Reais de Malaquita podem ser usadas para comandar Soldados de Cobre, desde que a ordem não contradiga
    uma ordem superior ainda ativa.
- name: Fonte
  desc: Livro de Regras dos Vales, pp. 172–173.
```
