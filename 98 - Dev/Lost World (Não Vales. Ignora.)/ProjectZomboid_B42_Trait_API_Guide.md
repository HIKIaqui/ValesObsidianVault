# Mapa prático da API de Project Zomboid B42 para traits

Versão pesquisada: **Build 42.20.2**. A documentação Java consultada foi gerada em 19 de agosto de 2026 e o código Lua vanilla usado para conferência corresponde ao commit `8a90669`, identificado como B42.20.2.

Este não é um despejo das milhares de classes do jogo. É um atlas do que realmente serve para criar traits: o que observar, o que alterar, qual hook costuma capturar o comportamento e onde multiplayer ou a ponte Java–Lua podem criar armadilhas.

## 1. O modelo mental que evita metade dos bugs

A modding API do Zomboid tem três camadas diferentes:

1. **Objetos Java expostos ao Lua** — `IsoPlayer`, `Stats`, `BodyDamage`, `InventoryItem`, `FluidContainer`, `BaseVehicle` etc. A Javadoc informa o que existe no Java, mas nem todo método necessariamente atravessa a ponte Lua em todo contexto.
2. **Eventos Lua** — `Events.EveryOneMinute`, `Events.OnPlayerUpdate`, `Events.OnWeaponSwingHitPoint` etc. São pontos de entrada baratos quando o evento certo existe.
3. **Implementação Lua vanilla** — classes como `ISEatFoodAction`, `ISDrinkFluidAction` e `ISBaseTimedAction`. Quando falta um evento completo, o próprio código vanilla revela onde medir a mudança ou instalar um wrapper.

Para cada mecânica, prefira nesta ordem:

1. evento específico;
2. consulta periódica barata;
3. wrapper de uma Timed Action;
4. snapshot e comparação;
5. patch de método Java/Lua amplo somente se nada melhor existir.

### Graus de confiança usados neste guia

| Grau  | Significado                                                                        |
| ----- | ---------------------------------------------------------------------------------- |
| **A** | Documentado e usado pelo Lua vanilla ou já comprovado no Hiki Traits               |
| **B** | Documentado, mas precisa de teste no contexto exato ou sincronização manual        |
| **C** | Depende de wrapper, snapshot, estado interno ou comportamento sem contrato estável |

O caso `isSitting()` resume a diferença: o método aparece na Javadoc, mas falhou pelo Lua. `isSitOnGround()` e `isSittingOnFurniture()` funcionaram. Documentação confirma existência; teste dentro do jogo confirma usabilidade.

## 2. Onde cada script deve rodar

| Necessidade                                                       | Local recomendado                         | Motivo                                         |
| ----------------------------------------------------------------- | ----------------------------------------- | ---------------------------------------------- |
| Alterar stats, vida, feridas ou estado persistente                | Servidor; localmente no singleplayer      | Mantém autoridade e evita divergência          |
| Ler input, UI, voz ou som que só o jogador afetado ouve           | Cliente local                             | Servidor dedicado não tem UI nem jogador local |
| Detectar uma Timed Action do jogador e alterar stats em MP        | Cliente detecta, servidor valida e aplica | Muitas ações existem primeiro no cliente       |
| Alterar mundo, objetos, inventário compartilhado ou atrair zumbis | Servidor                                  | O mundo precisa de uma única verdade           |
| Efeito puramente cosmético                                        | Cliente                                   | Não precisa trafegar pela rede                 |

Esqueleto seguro para uma verificação periódica:

```lua
local function updateCharacter(character)
    if character == nil or character:isDead() then return end
    -- conferir trait, ler estado e aplicar efeito
end

local function updatePlayers()
    if isClient() then return end

    if isServer() then
        local players = getOnlinePlayers()
        for i = 0, players:size() - 1 do
            updateCharacter(players:get(i))
        end
        return
    end

    updateCharacter(getPlayer())
end

Events.EveryOneMinute.Add(updatePlayers)
```

No singleplayer, normalmente `isClient()` e `isServer()` são ambos falsos. Por isso o ramo local não pode simplesmente ser esquecido.

## 3. Traits, registries e recursos customizados

### Traits

APIs principais:

```lua
CharacterTrait.register("meumod:meu_trait")

local trait = CharacterTrait.get(ResourceLocation.of("meumod:meu_trait"))
local hasIt = trait ~= nil and character:hasTrait(trait)

character:getCharacterTraits():add(trait)
character:getCharacterTraits():remove(trait)
```

Também existem `CharacterTraits:get`, `set`, `getTraits` e `getKnownTraits`. Para um trait escolhido na criação, consultar com `hasTrait` é a parte normal; adicionar e remover durante a campanha exige pensar em persistência, efeitos derivados e sincronização.

### Stats customizados

A B42 expõe oficialmente:

```lua
CharacterStat.register("meumod:valor", minimum, maximum, default)
CharacterStat.getById("meumod:valor")
```

Isso abre espaço para recursos como coragem, foco ou abstinência sem guardar tudo manualmente em `ModData`. Ainda é uma área **B**: um stat customizado não ganha automaticamente moodle, tradução ou comportamento vanilla, e deve ser testado em save/load e MP.

### Moodles customizados

Também existe:

```lua
MoodleType.register("meumod:meu_moodle")
MoodleType.get(ResourceLocation.of("meumod:meu_moodle"))
```

Registrar o tipo não cria sozinho o desenho e a lógica da UI. É a porta de entrada, não o sistema inteiro.

## 4. Stats: a caixa de ferramentas mais valiosa para traits

Objeto:

```lua
local stats = character:getStats()
local value = stats:get(CharacterStat.STRESS)
stats:set(CharacterStat.STRESS, novoValor)
stats:add(CharacterStat.STRESS, quantidade)
stats:remove(CharacterStat.STRESS, quantidade)
stats:reset(CharacterStat.STRESS)
stats:isAtMinimum(CharacterStat.STRESS)
stats:isAtMaximum(CharacterStat.STRESS)
```

Cada `CharacterStat` informa seus próprios limites:

```lua
local min = CharacterStat.STRESS:getMinimumValue()
local max = CharacterStat.STRESS:getMaximumValue()
local safe = CharacterStat.STRESS:clamp(valor)
```

Isso é melhor que espalhar `math.min(1, ...)` por todo o mod. Na prática, Stress e Endurance usam 0–1, enquanto Panic, Pain e Unhappiness usam 0–100, mas consultar os limites torna o código resistente a stats novos e mudanças futuras.

### Todos os stats registrados atualmente

| `CharacterStat`       | Uso potencial em traits                                                |
| --------------------- | ---------------------------------------------------------------------- |
| `ANGER`               | raiva, agressividade, irritação                                        |
| `BOREDOM`             | isolamento, rotina, leitura, atividades repetidas                      |
| `DISCOMFORT`          | roupa, postura, clima e ferimentos incômodos                           |
| `ENDURANCE`           | fôlego, recuperação e custos físicos                                   |
| `FATIGUE`             | sono e cansaço acumulado                                               |
| `FITNESS`             | estado físico interno; use com cautela, pois não é simplesmente o perk |
| `FOOD_SICKNESS`       | intoxicação alimentar                                                  |
| `HUNGER`              | fome                                                                   |
| `IDLENESS`            | ociosidade interna                                                     |
| `INTOXICATION`        | embriaguez                                                             |
| `MORALE`              | moral; pouco explorado pelo jogo e interessante para mods              |
| `NICOTINE_WITHDRAWAL` | abstinência de nicotina                                                |
| `PAIN`                | dor geral percebida                                                    |
| `PANIC`               | pânico                                                                 |
| `POISON`              | envenenamento                                                          |
| `SANITY`              | sanidade; existe no registry, mas o comportamento vanilla é limitado   |
| `SICKNESS`            | doença geral                                                           |
| `STRESS`              | estresse                                                               |
| `TEMPERATURE`         | temperatura corporal interna                                           |
| `THIRST`              | sede                                                                   |
| `UNHAPPINESS`         | tristeza                                                               |
| `WETNESS`             | umidade do personagem                                                  |
| `ZOMBIE_FEVER`        | febre da infecção zumbi                                                |
| `ZOMBIE_INFECTION`    | progressão estatística da infecção zumbi                               |

O objeto `Stats` também expõe dados sem `CharacterStat`: número de zumbis visíveis, perseguindo ou muito próximos, Endurance anterior, limites de aviso de Endurance, estado de recarga de Endurance e estado de tropeço. Isso permite traits que reagem à pressão real ao redor, não apenas ao Panic.

### Sincronização de stats

Depois de uma alteração autoritativa no servidor:

```lua
sendPlayerStat(character, CharacterStat.STRESS)
```

Também existem `syncPlayerStats(player, syncParams)` e `sendDamage(player)`, mas sincronizar somente o stat modificado é mais explícito e barato quando aplicável.

## 5. Moodles: leia a interpretação do jogo

Stats são valores contínuos; moodles são a classificação que o jogador enxerga.

```lua
local level = character:getMoodles():getMoodleLevel(MoodleType.ENDURANCE)
local maxed = character:getMoodles():isMaxMoodleLevel(MoodleType.PANIC)
```

Moodles expostos:

`ENDURANCE`, `TIRED`, `HUNGRY`, `PANIC`, `SICK`, `BORED`, `UNHAPPY`, `BLEEDING`, `WET`, `HAS_A_COLD`, `ANGRY`, `STRESS`, `THIRST`, `INJURED`, `PAIN`, `HEAVY_LOAD`, `DRUNK`, `DEAD`, `ZOMBIE`, `HYPERTHERMIA`, `HYPOTHERMIA`, `WINDCHILL`, `CANT_SPRINT`, `UNCOMFORTABLE`, `NOXIOUS_SMELL` e `FOOD_EATEN`.

Use o stat quando a mecânica depende de um limite exato. Use o moodle quando ela deve começar exatamente no mesmo estágio que a interface vanilla mostra. `ExhaustionAnxiety`, por exemplo, faz mais sentido com `MoodleType.ENDURANCE` do que com quatro limites recriados manualmente.

## 6. Corpo, vida, ferimentos e medicina

### `BodyDamage`

Entrada principal:

```lua
local bodyDamage = character:getBodyDamage()
local parts = bodyDamage:getBodyParts()
local neck = bodyDamage:getBodyPart(BodyPartType.Neck)
```

Possibilidades relevantes:

- ler, adicionar ou reduzir vida geral;
- recalcular a vida total depois de alterar partes;
- ler todas as partes ou uma região específica;
- consultar se existe ferimento, sangramento, mordida, corte, arranhão ou sutura;
- controlar frio, redução de dor e efeitos de medicamentos;
- consultar e alterar infecção zumbi geral, tempo de infecção e mortalidade;
- consultar infecção falsa e doença de ferida;
- usar `JustAteFood`, `JustDrankBooze`, `JustTookPainMeds` e `JustTookPill` quando a intenção for passar pela lógica vanilla correspondente.

Métodos importantes:

```lua
bodyDamage:getOverallBodyHealth()
bodyDamage:AddGeneralHealth(amount)
bodyDamage:ReduceGeneralHealth(amount)
bodyDamage:calculateOverallHealth()

bodyDamage:isInfected()
bodyDamage:setInfected(boolean)
bodyDamage:getInfectionTime()
bodyDamage:setInfectionTime(hours)
bodyDamage:getInfectionMortalityDuration()
bodyDamage:setInfectionMortalityDuration(hours)
```

### `BodyPart`

Cada parte oferece leitura e mutação granular:

| Família         | Leitura                                           | Alteração                                                             |
| --------------- | ------------------------------------------------- | --------------------------------------------------------------------- |
| Arranhão        | `scratched()`, `getScratchTime()`                 | `setScratched(flag, forceNoInfection)`, `setScratchTime()`            |
| Laceração       | `isCut()`, `getCutTime()`                         | `setCut(flag, forceNoInfection)`, `setCutTime()`                      |
| Mordida         | `bitten()`, `getBiteTime()`                       | `SetBitten(flag, infected)`, `setBiteTime()`                          |
| Ferida profunda | `isDeepWounded()`, `getDeepWoundTime()`           | `setDeepWounded()`, `setDeepWoundTime()`                              |
| Queimadura      | `isBurnt()`, `getBurnTime()`                      | `setBurned()`, `setBurnTime()`                                        |
| Fratura         | `getFractureTime()`                               | `setFractureTime()`, `generateFractureNew()`                          |
| Vidro           | `haveGlass()`                                     | `setHaveGlass()`                                                      |
| Bala            | `haveBullet()`                                    | `setHaveBullet()`                                                     |
| Curativo        | `bandaged()`, vida/tipo/sujeira                   | `setBandaged()` e setters associados                                  |
| Sutura          | `stitched()`, `getStitchTime()`                   | `setStitched()`, `setStitchTime()`                                    |
| Sangramento     | `bleeding()`, `getBleedingTime()`                 | `setBleeding()`, `setBleedingTime()`                                  |
| Infecção comum  | `isInfectedWound()`                               | `setInfectedWound()`, nível da infecção                               |
| Infecção zumbi  | `IsInfected()`, `IsFakeInfected()`                | `SetInfected()`, `SetFakeInfected()`                                  |
| Dano e dor      | `getHealth()`, `getPain()`, `getAdditionalPain()` | `SetHealth()`, `AddHealth()`, `ReduceHealth()`, `setAdditionalPain()` |

Depois de uma transformação de ferimento, normalmente é necessário recalcular a saúde e sincronizar a parte:

```lua
bodyDamage:calculateOverallHealth()
syncBodyPart(bodyPart, syncParams)
sendDamage(character)
```

O valor correto de `syncParams` deve ser copiado da operação vanilla semelhante. Não invente uma máscara de bits por inspiração divina.

### A grande limitação: nascimento de ferimentos

Não existe um evento universal e perfeito de “nova ferida aplicada” que resolva todos os casos e todos os jogadores em MP. `OnPlayerGetDamage` inclui dano contínuo e costuma ser uma observação local. Para traits como `LoudWhenHurt`, `NeckReflex` e `BittenNo`, snapshot por parte do corpo é a técnica mais confiável:

1. inicializar sem reagir aos ferimentos antigos;
2. comparar booleans e timers em intervalos curtos;
3. reagir apenas a uma transição nova;
4. marcar o ferimento como visto mesmo quando o trait não pôde tratá-lo;
5. sincronizar qualquer transformação autoritativa.

Para transformar mordida em laceração com garantia de cura da infecção zumbi, limpar apenas `SetBitten(false)` não basta. É preciso considerar a flag na parte, a flag geral, infecção falsa, tempos de infecção e o estado de ferida comum. Os scripts `NeckReflex` e `BittenNo` são bons modelos dessa operação composta.

## 7. Nutrição, fitness, sono e necessidades

### Nutrição

```lua
local nutrition = character:getNutrition()
nutrition:getWeight()
nutrition:setWeight(weight)
nutrition:characterHaveWeightTrouble()

nutrition:getCalories()
nutrition:getCarbohydrates()
nutrition:getLipids()
nutrition:getProteins()
```

Todos os macronutrientes possuem setters. Também há flags de ganho e perda de peso. Para saber se o peso está problemático, `characterHaveWeightTrouble()` é melhor que procurar traits estáticos de peso.

### Fitness e exercício

`character:getFitness()` dá acesso a:

- exercício atual;
- repetições;
- regularidade por exercício;
- rigidez futura e atual;
- redução de Endurance;
- incremento de stats do exercício.

A classe `ISFitnessAction` expõe `start`, `animEvent`, `stop` e `complete`, sendo melhor para detectar uma sessão concreta. `ExerciseRoutine` usa essa camada em vez de adivinhar pelo gasto de Endurance.

### Sono, repouso e postura

APIs úteis:

```lua
character:isAsleep()
character:isSitOnGround()
character:isSittingOnFurniture()
character:isReading()
character:getHoursSurvived()
```

Timed Actions úteis: `ISRestAction`, `ISSitOnGround`, `ISGetOnBedAction`, `ISWakeOtherPlayer` e as ações de sono do cliente.

`isSitting()` está documentado, mas já falhou pelo Lua nesta versão. Use os dois estados concretos, idealmente protegidos por `pcall` quando a compatibilidade for importante.

## 8. Inventário, itens, equipamento e roupas

### `InventoryItem`

Quase todo item permite consultar:

```lua
item:getFullType()       -- "Base.AlgumaCoisa"
item:getType()           -- tipo sem módulo
item:getDisplayName()
item:getCategory()
item:getTags()
item:hasTag(ItemTag.FIREARM)
item:getCondition()
item:setCondition(value)
item:getWeight()
item:getActualWeight()
item:isBroken()
item:isEquipped()
item:isFavorite()
item:getContainer()
item:getModData()
item:getScriptItem()
item:getFluidContainer()
```

`Use()` consome um uso do item, mas chamá-lo diretamente ignora parte da semântica de uma ação. Para traits, normalmente é melhor observar a ação vanilla e aplicar um efeito depois que ela confirma o consumo.

### Tags versus listas de IDs

Tags tornam compatibilidade com mods muito melhor, mas só quando a tag representa exatamente o conceito desejado. A B42 possui centenas. Algumas particularmente úteis:

`ALCOHOLIC_BEVERAGE`, `CAN_EAT`, `CHEESE`, `DRIED_FOOD`, `EGG`, `FIREARM`, `FISH_MEAT`, `MILK`, `PRESERVED_FOOD`, `TOBACCO`, `CHEWING_TOBACCO`, `PETROL`, `IS_FIRE_FUEL`, `GAS_MASK`, `RESPIRATOR`, `HAZMAT_SUIT`, `HEAVY_ITEM`, `WEARABLE`, `AMMO` e `ANIMAL_CORPSE`.

Não existe uma tag genérica perfeita para todo conceito. `MeatFueled`, por exemplo, ainda se beneficia de uma lista explícita de carnes puras porque `FISH_MEAT` não cobre toda carne e receitas transformam o item final.

Boa estratégia:

1. tag oficial precisa;
2. propriedades da classe;
3. ingredientes ou fluidos preservados;
4. lista vanilla explícita;
5. função pública para outros mods registrarem IDs.

### `ItemContainer`

Permite:

- listar com `getItems()`;
- contar por tipo ou tag;
- buscar primeiro tipo/tag/categoria;
- procurar dentro de bolsas quando a sobrecarga escolhida permite recursão;
- adicionar e remover itens;
- localizar o container mais externo;
- filtrar comida, itens quebrados e itens equipados.

Métodos frequentes: `contains`, `containsType`, `containsTag`, `getCountType`, `getCountTag`, `FindAndReturn`, `FindAndReturnCategory`, `getItemFromType`, `AddItem`, `Remove` e `RemoveOneOf`.

Em MP, adicionar ou remover inventário exige usar o caminho de rede equivalente do jogo. A mudança existir na tabela do servidor não garante que todos os clientes a tenham visto.

### Itens vestidos e anexados

```lua
local worn = character:getWornItems()
for i = 0, worn:size() - 1 do
    local item = worn:getItemByIndex(i)
end

local attached = character:getAttachedItems()
```

`WornItems` e `AttachedItems` permitem consultar item por índice/local, descobrir a localização, adicionar, remover e limpar. Isso serve para proteção auditiva, máscaras, calçados, mochilas, coldres e amuletos. Para um efeito protetor, estar no inventário não deveria contar se o design exige estar vestido.

## 9. Comida e consumo

### Propriedades de `Food`

Além dos dados gerais de item:

```lua
food:isFresh()
food:isRotten()
food:isFrozen()
food:getHungerChange()
food:getThirstChange()
food:getCalories()
food:getCarbohydrates()
food:getLipids()
food:getProteins()
food:getStressChange()
food:getUnhappyChange()
food:getBoredomChange()
food:getEndChange()
food:getPainReduction()
food:getFluReduction()
food:getPoisonPower()
food:getSpices()
food:getEvolvedRecipeName()
```

Há também temperatura, tempo de congelamento, estado queimado/cozido, idade, limites de deterioração e propriedades de ervas. Algumas dessas funções aparecem na superclasse ou em interfaces e podem não estar agrupadas de forma intuitiva na página `Food`.

### Hooks de comida

A B42.20.2 **não possui um `Events.OnEat` global**. Para consumo real, `ISEatFoodAction:eat()` e `:complete()` são os pontos comprovados: `complete()` cobre a conclusão normal e `eat()` registra a parcela consumida quando a ação é interrompida.

Problemas comuns:

- comer parcialmente;
- ação interrompida depois de consumir parte;
- receita preparada trocar o `fullType` do ingrediente;
- comida dividida em tigelas;
- consumo disparar no cliente antes de a mudança autoritativa.

`SelectiveEater`, `ComfortEater` e `MeatFueled` representam três níveis de detecção: identidade/receita, qualquer alimento e lista estrita de alimentos.

## 10. Fluidos: um sistema próprio da B42

Líquidos não são simplesmente `Food`. Um item ou objeto pode conter um `FluidContainer` com mistura, quantidade e propriedades combinadas.

### `FluidContainer`

```lua
local fc = item:getFluidContainer()
fc:getAmount()
fc:getCapacity()
fc:getFreeCapacity()
fc:getFilledRatio()
fc:getPrimaryFluid()
fc:getPrimaryFluidAmount()
fc:getRatioForFluid(fluid)
fc:contains(fluid)
fc:isPureFluid(fluid)
fc:isCategory(FluidCategory.Alcoholic)
fc:getProperties()
```

Também permite adicionar, remover, ajustar e transferir fluidos. As operações `transferTo`, `transferFrom` e `Transfer` devem ser tratadas como alterações de inventário/mundo e sincronizadas pelo caminho correto.

### Tipos vanilla registrados

`Water`, `TaintedWater`, `CarbonatedWater`, `SodaPop`, `Coffee`, `Tea`, `Beer`, `Wine`, `Whiskey`, `Mead`, `CowMilk`, `SheepMilk`, `AnimalMilk`, `Petrol`, `RubbingAlcohol`, `Bleach`, `Blood`, `AnimalBlood`, `Honey`, `Acid`, `CleaningLiquid`, `AnimalGrease`, tintas, corantes, venenos e `Modded`.

Categorias: `Beverage`, `Alcoholic`, `Hazardous`, `Medical`, `Industrial`, `Colors`, `Dyes`, `HairDyes`, `Paint`, `Fuel`, `Poisons` e `Water`.

### Propriedades combinadas da mistura

`fluidContainer:getProperties()` fornece:

- mudança de fadiga, fome, estresse, sede e tristeza;
- calorias, carboidratos, lipídios e proteínas;
- álcool e veneno;
- redução de gripe e dor;
- mudança de Endurance e doença alimentar.

### Hooks de bebida

Não existe um único hook que represente todos os caminhos. A B42.20.2 usa pelo menos:

- `ISDrinkFluidAction` para recipientes/fluidos;
- `ISDrinkFromBottle` para beber diretamente;
- `ISTakeWaterAction` para fontes do mundo;
- `ISAddFluidFromItemAction`, `ISTransferWaterAction`, `ISDumpWaterAction` e `ISDumpContentsAction` para movimentação de fluidos.

Para medir quanto foi realmente bebido, grave `getAmount()` antes e subtraia o valor depois. `OnlyDrankSoda` é o modelo de cobertura dos três caminhos de consumo.

## 11. Movimento, estado do personagem e ações

Consultas diretas úteis:

```lua
character:isMoving()
character:isRunning()
character:isSprinting()
character:isSneaking()
character:isAiming()
character:isClimbing()
character:isOutside()
character:isDriving()
character:isOnFire()
character:isReading()
character:getCurrentState()
character:getActionContext()
character:getTimedActionTimeModifier()
```

Também existem inventário nas mãos, veículo, quadrado atual, coordenadas, animação e número de zumbis atacando ao redor.

Eventos adequados:

- `OnPlayerMove` quando a mudança depende de movimento;
- `OnPlayerUpdate` para observação por jogador;
- `OnTick` somente quando a resposta precisa ser imediata;
- `EveryOneMinute` ou `EveryTenMinutes` para acúmulo de necessidades;
- Timed Actions quando a atividade tem início/fim/cancelamento claros.

Evite fazer 17 varreduras corporais, uma busca de inventário recursiva e uma contagem de zumbis a cada `OnTick` para todos os personagens. Frequência deve acompanhar a velocidade perceptível da mecânica.

## 12. Timed Actions: onde está a maior parte das atividades

`ISBaseTimedAction` possui os ciclos `start`, `update`, `animEvent`, `stop`, `perform` e, nas ações novas, frequentemente `complete`. O progresso pode ser consultado com `getJobDelta()`.

Famílias especialmente úteis para traits:

| Família                  | Ações relevantes                                                                                                       |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------- |
| Comer/beber/remédios     | `ISEatFoodAction`, `ISDrinkFluidAction`, `ISDrinkFromBottle`, `ISTakeWaterAction`, `ISTakePillAction`                  |
| Medicina                 | `ISApplyBandage`, `ISDisinfect`, `ISStitch`, `ISRemoveBullet`, `ISRemoveGlass`, `ISCleanBurn`, `ISSplint`, cataplasmas |
| Exercício/repouso        | `ISFitnessAction`, `ISRestAction`, `ISSitOnGround`, cama/sono                                                          |
| Leitura/pesquisa/escrita | `ISReadABook`, `ISResearchRecipe`, `ISWriteSomething`                                                                  |
| Inventário/equipamento   | `ISTransferAction`, `ISEquipWeaponAction`, `ISUnequipAction`, `ISWearClothing`, hotbar                                 |
| Armas                    | recarga, inserir/ejetar carregador, rack, carregar/descarregar munição, upgrade                                        |
| Construção/destruição    | barricada, desbarricada, craft, desmontar, reparar, cortar árvore, destruir objetos                                    |
| Fogo/energia             | gerador, churrasqueira, fogão, combustível, bateria, apagar fogo                                                       |
| Limpeza e higiene        | lavar personagem/roupa, secar, torcer, limpar sangue, curativo e grafite                                               |
| Veículos                 | entrar/sair, mecânica, combustível e peças                                                                             |
| Animais                  | alimentar, dar água, acariciar, ordenhar, tosquiar, abater, carnear, coletar ovos                                      |

### Wrapper compatível

Nunca substitua um método vanilla sem chamar a implementação original. Guarde a referência, impeça instalação duplicada e capture valores antes de o original apagá-los:

```lua
local HOOK_VERSION = 1

if MeuTrait._hookVersion ~= HOOK_VERSION then
    local vanillaComplete = MeuTrait._vanillaComplete
        or ISEatFoodAction.complete

    MeuTrait._hookVersion = HOOK_VERSION
    MeuTrait._vanillaComplete = vanillaComplete

    function ISEatFoodAction:complete()
        local character = self.character
        local item = self.item
        local result = vanillaComplete(self)
        -- efeito complementar, sem impedir o vanilla
        return result
    end
end
```

Ainda existe conflito se dois mods substituírem o mesmo método e um deles guardar uma referência antiga de modo incorreto. Quanto mais específico o hook, menor a superfície de conflito.

## 13. Combate, armas e dano

### `HandWeapon`

Permite consultar:

- `isRanged()`;
- dano mínimo/máximo;
- alcance;
- raio e volume de som;
- tipo de munição/carregador;
- tempo de mira, recarga e recuo;
- condição, upgrades e propriedades de ataque.

### Eventos de combate mais úteis

| Evento | Melhor uso | Observação |
|---|---|---|
| `OnWeaponSwingHitPoint` | detectar disparo ou ataque executado | Com `weapon:isRanged()`, sustentou `EarRinging` |
| `OnPlayerAttackFinished` | depois do ataque | Vanilla usa para estado de armas/recarga |
| `OnWeaponHitXp` | acerto e XP de arma | Bom para efeitos por acerto; assinatura deve ser conferida na versão |
| `OnWeaponHitTree` | atacar árvore | Específico e barato |
| `OnHitZombie` | impacto em zumbi | Usado pelo vanilla, mas valide lado cliente/servidor |
| `OnPlayerGetDamage` | dano recebido pelo jogador local | Não equivale a ferimento novo; inclui dano periódico |
| `OnZombieDead` | morte de zumbi | Bom para recompensas, culpa, buffs e contadores |

O som que o jogador ouve e o som que atrai zumbis são mecanismos separados:

```lua
character:playerVoiceSound("ShoutHey")       -- voz do personagem
getSoundManager():playUISound("MeuSom")      -- som local/UI
character:addWorldSoundUnlessInvisible(20, 50, false) -- IA do mundo
```

Isso permite tinnitus somente local, mas grito audível para a IA; ou um som cosmético que não altera a simulação.

## 14. XP, perks e profissões

Perks atuais incluem:

`Fitness`, `Strength`, `Sprinting`, `Lightfoot`, `Nimble`, `Sneak`, `Cooking`, `Woodwork`, `Aiming`, `Reloading`, `Farming`, `Fishing`, `Trapping`, `PlantScavenging`, `Doctor`, `Electricity`, `Blacksmith`, `MetalWelding`, `Mechanics`, `Spear`, `Maintenance`, `SmallBlade`, `LongBlade`, `SmallBlunt`, `Blunt`, `Axe`, `Tailoring`, `Tracking`, `Husbandry`, `FlintKnapping`, `Masonry`, `Pottery`, `Carving`, `Butchering` e `Glassmaking`, além de categorias agregadoras.

```lua
local level = character:getPerkLevel(Perks.Cooking)
character:getXp():AddXP(Perks.Cooking, amount)
```

Eventos `AddXP` e `LevelPerk` permitem reagir ao ganho e à subida de nível. Modificar XP em MP deve seguir a autoridade do servidor e ser testado contra as opções de multiplicador.

Possibilidades: aprendizagens condicionais, especialização por ferramenta, bônus depois de descanso, medo de inexperiência, satisfação ao subir skill e penalidades por falha.

## 15. Tempo, calendário e persistência

### Tempo do mundo

```lua
local gameTime = getGameTime()
local hours = gameTime:getWorldAgeHours()
local hour = gameTime:getHour()
local minute = gameTime:getMinutes()
local days = gameTime:getDaysSurvived()
local dayLength = getSandboxOptions():getDayLengthMinutes()
```

`getWorldAgeHours()` é a melhor base para cooldowns e prazos que devem avançar com o mundo. `getTimestampMs()` serve para cooldown real de rede/duplicidade, não para “seis horas dentro do jogo”.

Eventos temporais:

- `EveryOneMinute`;
- `EveryTenMinutes`;
- `EveryHours`;
- `EveryDays`;
- `OnGameTimeLoaded`;
- `OnNewGame`;
- `OnGameStart`.

### `ModData`

```lua
local data = character:getModData()
data.HikiTraits = data.HikiTraits or {}
data.HikiTraits.lastUseHour = getGameTime():getWorldAgeHours()
```

Use para cooldowns, uso único, contadores, ferimentos já avaliados e última satisfação de uma necessidade. Prefira uma tabela com namespace do mod e versão de schema.

```lua
data.HikiTraits = data.HikiTraits or { schema = 1 }
```

O fato de `ModData` ser salvo não significa que toda alteração local foi transmitida. Em MP, o servidor deve possuir o estado decisivo ou receber um comando validado.

## 16. Multiplayer e comandos

### Fluxo cliente → servidor

```lua
-- cliente
sendClientCommand(character, "HikiTraits", "MinhaAcao", {
    amount = amount,
})

-- servidor
local function onClientCommand(module, command, character, args)
    if module ~= "HikiTraits" or command ~= "MinhaAcao" then return end
    if character == nil or character:isDead() then return end

    -- validar trait, limites, cooldown e se a ação é plausível
end

Events.OnClientCommand.Add(onClientCommand)
```

### Fluxo servidor → cliente

`sendServerCommand` e `Events.OnServerCommand` servem para efeitos/UI que precisam ser solicitados pelo servidor.

### Nunca confie nos argumentos do cliente

O servidor deve validar:

- se o personagem possui o trait;
- se está vivo;
- limites numéricos;
- cooldown;
- item, posição ou estado quando verificáveis;
- uso único persistente.

`TaskFixation` usa exatamente esse padrão: o cliente detecta o cancelamento local, envia progresso e nome; o servidor limita progresso, aplica cooldown, confere o trait e só então altera Stress.

### Funções de sincronização relevantes

| Função | Uso |
|---|---|
| `sendPlayerStat(player, stat)` | um `CharacterStat` alterado |
| `syncPlayerStats(player, params)` | conjunto de stats por máscara |
| `sendDamage(player)` | estado de dano corporal |
| `syncBodyPart(bodyPart, params)` | campos de uma parte corporal |
| `sendItemStats(item)` | propriedades mutáveis de item |
| `sendEquip(player)` | equipamentos |
| `sendClothing(...)`, `syncVisuals(...)` | roupa/aparência |
| `sendItemsInContainer(...)` | conteúdo de container mundial |

Copie a mesma sequência usada pela operação vanilla mais parecida. Os nomes são documentados; a combinação correta ainda depende do protocolo daquele objeto.

## 17. Veículos

```lua
local vehicle = character:getVehicle()
if vehicle and vehicle:getDriver() == character then
    local speed = vehicle:getCurrentAbsoluteSpeedKmHour()
end
```

`BaseVehicle` permite observar:

- motorista, ocupantes e assentos;
- velocidade com ou sem sinal;
- motor iniciado, rodando ou funcional;
- condição, qualidade, potência e barulho do motor;
- combustível restante;
- teto do assento;
- peças, portas, janelas, luzes e containers;
- dano, colisão, reboque e regulador de velocidade.

Eventos: `OnEnterVehicle`, `OnExitVehicle`, `OnSwitchVehicleSeat`, `OnVehicleHorn`, `OnVehicleDamageTexture`, `OnMechanicActionDone` e `OnUseVehicle`.

Ideias viáveis: enjoo por velocidade, motorista calmo, claustrofobia no carro, felicidade em viagem, medo de motor danificado, mecânico obsessivo, estresse sem cinto ou em assento sem teto.

`RoadTripper` comprova o padrão mais simples: `getVehicle()`, motorista e velocidade em `EveryOneMinute`.

## 18. Mundo, quadrados, salas e construções

### `IsoCell`

`getCell()` oferece:

- `getGridSquare(x, y, z)`;
- lista de zumbis;
- lista de objetos/moving objects;
- veículos;
- construções e salas carregadas;
- zumbi visível mais próximo para um jogador.

### `IsoGridSquare`

Um quadrado informa:

- coordenadas e vizinhos;
- sala, construção e zona;
- interior/exterior;
- água e poças;
- portas, janelas, paredes, cercas e bloqueios;
- piso sólido, telhado e vegetação;
- luz, visibilidade e propriedades dos objetos;
- distância e acessibilidade para outro quadrado;
- objetos, corpos, armadilhas, fogo e containers presentes.

### Salas e construções

`IsoRoom` fornece nome, quadrados, definição, construção e presença de água. `IsoBuilding` fornece definição e salas, inclusive sala aleatória por nome.

Isso permite traits ligados a:

- interior/exterior;
- tipo de sala;
- dormir em local conhecido;
- ficar em porão ou andar alto;
- água, chuva e abrigo;
- proximidade de corpos, fogo, janelas quebradas ou vegetação;
- base limpa, iluminada, barricada ou lotada.

Eventos úteis: `LoadGridsquare`, `OnObjectAdded`, `OnObjectAboutToBeRemoved`, `OnDestroyIsoThumpable`, `OnContainerUpdate`, `OnWaterAmountChange` e os context menus do inventário/mundo.

Varreduras globais de `getCell():getZombieList()` ou `getObjectList()` são potencialmente caras. Prefira o quadrado do personagem e um raio limitado.

## 19. Zumbis, percepção e ruído mundial

`IsoZombie` expõe, entre muitas outras coisas:

- alvo atual e último alvo visto;
- tempo desde que viu carne;
- estado de alerta e atração por som;
- velocidade, audição, visão, memória, cognição e força;
- estado de crawler, corrida, lunge e ataque;
- dono de rede do zumbi;
- outfit, grupo, caminho e localização;
- parte atingida por último e personagem que deu o último golpe.

As APIs de pathing permitem mandar um zumbi para um personagem ou localização, mas mexer diretamente na IA é **B/C** e deve ser autoritativo.

Para atrair zumbis, prefira som mundial:

```lua
character:addWorldSoundUnlessInvisible(radius, volume, stressHumans)
```

Ou `getWorldSoundManager():addSound(...)` quando precisar de fonte, flags, repetição ou controle mais avançado. O manager calcula qual som é mais relevante para cada zumbi.

Possibilidades: cheiro/ruído involuntário, gritos, passos especiais, calma quando nenhum zumbi está visível, adrenalina conforme perseguidores, recompensas por matar e pânico baseado em proximidade real.

## 20. Clima, temperatura e ambiente

`ClimateManager.getInstance()` fornece:

```lua
climate:getTemperature()
climate:getHumidity()
climate:getWindIntensity()
climate:getWindAngleIntensity()
climate:getFogIntensity()
climate:isRaining()
climate:getRainIntensity()
climate:isSnowing()
climate:getSnowIntensity()
climate:getDayLightStrength()
climate:getNightStrength()
climate:getSeason()
climate:getWeatherPeriod()
```

Eventos: `OnClimateManagerInit`, `OnThunderEvent`, `OnWeatherPeriodStart`, `OnWeatherPeriodStage` e `OnWeatherPeriodComplete`.

Combine clima com `character:isOutside()`, Wetness, Temperature, roupa e abrigo. Isso permite amante de chuva, medo de tempestade, tolerância ao frio, fotossensibilidade, humor sazonal e penalidades por roupa molhada.

## 21. Animais e pecuária

A B42 expõe `IsoAnimal` e uma família enorme de Timed Actions. Dados diretamente úteis incluem:

```lua
animal:getAnimalType()
animal:getBreed()
animal:getAge()
animal:getHunger()
animal:getThirst()
animal:getStress()
animal:getAcceptanceLevel(player)
animal:isWild()
```

O objeto possui muito mais estado relacionado a saúde, reprodução, leite, lã, genética, grupo, abrigo e produtos, mas essa área ainda merece teste individual porque diversas funções trabalham com componentes internos.

Ações observáveis:

- acariciar e atrair;
- alimentar e dar água;
- pegar, prender, transportar e colocar no curral;
- coletar ovos;
- ordenhar e tosquiar;
- abater, sangrar, retirar pele, ossos, cabeça e carne;
- limpar curral e ninho.

`AnimalLover`, baseado em `ISPetAnimal:animEvent/complete`, é um bom modelo para efeito associado a uma interação específica sem varrer todos os animais do mapa.

## 22. Áudio, voz e feedback local

Três camadas:

| API | Quem percebe | Uso |
|---|---|---|
| `getSoundManager():playUISound(name)` | somente cliente local | tinnitus, notificadores, efeitos mentais |
| `character:playSound(name)` ou emitter | jogadores que recebem o som do objeto | efeitos posicionais |
| `addWorldSound...` | IA de zumbis/animais e possível estresse | consequência mecânica do ruído |

O sound manager local permite guardar o handle, consultar se está tocando e parar antes de reiniciar:

```lua
if handle and getSoundManager():isPlayingUISound(handle) then
    getSoundManager():stopUISound(handle)
end
handle = getSoundManager():playUISound("EarRinging")
```

Emitters permitem volume, pitch, parâmetros FMOD, posição e interrupção por handle. Voz usa `playerVoiceSound(suffix)` e pode ser transmitida separadamente quando necessário.

## 23. Sandbox e compatibilidade de configuração

`getSandboxOptions()` permite consultar opções pelo nome/índice e também alguns multiplicadores diretos:

```lua
getSandboxOptions():getDayLengthMinutes()
getSandboxOptions():getEnduranceRegenMultiplier()
getSandboxOptions():getStatsDecreaseMultiplier()
getSandboxOptions():getOptionByName("...")
```

Traits com taxa por tempo devem decidir se representam minutos do mundo ou efeito por dia real configurado. Usar eventos de minuto do jogo acompanha automaticamente a velocidade do relógio; multiplicadores de regen e stats podem exigir escala adicional para combinar com a dificuldade escolhida.

Opções customizadas do próprio mod podem controlar intensidade, cooldowns, chance, compatibilidade e logs sem editar o Lua.

## 24. Outras superfícies especializadas que também podem render traits

### Crafting e receitas

`getScriptManager()`, `getAllRecipes()`, `OnMakeItem`, `OnDynamicMovableRecipe`, `ISCraftAction`, `ISAddItemInRecipe`, `ISFixAction`, `ISDismantleAction` e os novos objetos de crafting da B42 permitem observar receita, ingredientes, resultado, dificuldade e conclusão. É uma boa base para perfeccionismo, especialização artesanal, prazer em fabricar ou medo de desperdiçar recursos.

### Agricultura, coleta, pesca e armadilhas

Existem objetos específicos de plantas, zonas de forage, armadilhas e pesca, além de eventos como `OnForageSpot`, `OnForageRequestZone`, `OnForagePool` e `OnItemFound`. Muitas dessas mecânicas são melhor interceptadas na ação que concede o item ou XP, porque o objeto mundial sozinho não diz quem realizou o trabalho.

### Energia, fogo e dispositivos

Geradores, baterias, fogões, churrasqueiras, luzes, alarmes, rádios e mídia possuem objetos e Timed Actions dedicados. Isso permite traits ligados a escuridão, eletricidade, fogo, ouvir rádio, colecionar mídia, manter geradores e dormir perto de alarmes. Classes de UI podem ajudar a detectar interação, mas o efeito mecânico deve continuar fora da UI sempre que possível.

### Fogo, corpos e higiene

`IsoObject`, quadrados e ações vanilla permitem observar fogo, cinzas, sangue, sujeira, roupas molhadas, corpos e sepultamento. O `BodyDamage` calcula inclusive doença por corpos. Traits de limpeza, tanatofobia, fascínio por fogo ou resistência a ambientes imundos são viáveis, mas contagens espaciais devem usar raio pequeno e baixa frequência.

### UI, input, chat e relações multiplayer

Eventos de teclado, mouse, context menu, chat, trade, facção e safehouse existem e podem sustentar feedback ou ações sociais. São adequados para traits que oferecem comandos ou opções extras. Não use o texto da UI como fonte autoritativa de uma alteração no mundo; envie uma intenção ao servidor e valide-a.

### Objetos de script, IDs e registries

A B42 usa registries e `ResourceLocation` com mais intensidade. Traits, moodles, perks, fluidos, tags e tipos de item devem ser resolvidos por identificadores estáveis em vez de nomes traduzidos. `getDisplayName()` serve para log/UI; `getFullType()`, tags e IDs de registry servem para lógica.

## 25. Eventos mais valiosos para traits

Esta tabela é um mapa de entrada, não uma promessa de assinatura universal. Eventos do motor nem sempre documentam parâmetros na Javadoc; confira uma chamada vanilla da mesma build ou registre argumentos em debug.

| Grupo         | Eventos                                                                                                                      |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| Ciclo de vida | `OnGameBoot`, `OnGameStart`, `OnNewGame`, `OnCreatePlayer`, `OnPlayerDeath`, `OnResetLua`                                    |
| Tempo         | `OnTick`, `EveryOneMinute`, `EveryTenMinutes`, `EveryHours`, `EveryDays`, `OnGameTimeLoaded`                                 |
| Jogador       | `OnPlayerUpdate`, `OnPlayerMove`, `OnPlayerGetDamage`, `OnPlayerAttackFinished`                                              |
| Combate       | `OnWeaponSwingHitPoint`, `OnWeaponHitXp`, `OnWeaponHitTree`, `OnHitZombie`, `OnZombieDead`                                   |
| Itens         | `OnEquipPrimary`, `OnEquipSecondary`, `OnClothingUpdated`, `OnContainerUpdate`, `OnMakeItem`; comida exige `ISEatFoodAction` |
| Mundo         | `LoadGridsquare`, `OnObjectAdded`, `OnObjectAboutToBeRemoved`, `OnDestroyIsoThumpable`, `OnWaterAmountChange`                |
| Veículos      | `OnEnterVehicle`, `OnExitVehicle`, `OnSwitchVehicleSeat`, `OnVehicleHorn`, `OnMechanicActionDone`                            |
| Clima         | `OnClimateManagerInit`, `OnThunderEvent`, `OnWeatherPeriodStart/Stage/Complete`                                              |
| XP            | `AddXP`, `LevelPerk`                                                                                                         |
| UI/input      | `OnKeyPressed`, `OnKeyStartPressed`, `OnKeyKeepPressed`, context menus e mouse                                               |
| Rede          | `OnClientCommand`, `OnServerCommand`, `OnConnected`, `OnDisconnect`                                                          |
| Persistência  | `OnInitGlobalModData`, `OnLoad`, `OnPostSave`, `OnServerStartSaving`, `OnServerFinishSaving`                                 |

### Escolha de frequência

| Necessidade | Hook sugerido |
|---|---|
| Reação visual/ferida quase imediata | `OnPlayerUpdate` ou `OnTick`, com snapshot pequeno |
| Mudança lenta de stat | `EveryOneMinute` |
| Necessidade de horas/dias | `EveryHours` ou timestamp em `ModData` |
| Ação concreta | evento específico ou Timed Action |
| Efeito depois de input local | evento de input no cliente |

## 26. Sistemas que ainda podem render traits novos

### Muito diretos

- estar molhado, com frio, calor, fome, sede, sono ou carga pesada;
- número de zumbis visíveis/próximos;
- correr, esgueirar, mirar, dirigir ou permanecer parado;
- hora do dia, noite, estação, chuva, neve, vento e neblina;
- peso e macronutrientes;
- item equipado, arma em mãos, roupa ou proteção;
- nível de perk;
- estar dentro/fora, em uma sala específica ou no veículo.

### Moderados

- resultado de leitura, crafting, reparo ou tratamento;
- consumo parcial de comida ou líquido;
- interação com animal;
- matar/acertar zumbis com arma específica;
- dirigir acima de uma velocidade ou com veículo danificado;
- ferida nova numa parte específica;
- roupa suja, molhada, ensanguentada ou com proteção específica.

### Delicados

- modificar diretamente IA/pathfinding de zumbis;
- transformar feridas e limpar infecção zumbi;
- alterar inventário ou objetos mundiais em MP;
- detectar ingrediente original depois que uma receita virou outro item;
- substituir método base usado por muitos mods;
- criar stat/moodle customizado com UI e sincronização completas;
- alterar valores que o vanilla recalcula todo tick, pois a mudança pode ser imediatamente sobrescrita.

## 27. Ideias de traits sugeridas diretamente pela API

| Sistema | Ideia | Implementação provável |
|---|---|---|
| Zumbis visíveis | **Crowd Control**: recupera pânico quando nenhum zumbi o persegue | `Stats:getNumChasingZombies()` por minuto |
| Temperatura | **Cold Focus**: frio moderado reduz tédio, frio severo aumenta estresse | Moodle + `CharacterStat.TEMPERATURE` |
| Chuva | **Pluviophile**: chuva externa reduz tristeza | ClimateManager + `isOutside()` |
| Noite | **Night Person**: bônus de Endurance à noite, penalidade de manhã | GameTime + luz noturna |
| Equipamento | **Barefoot Habit**: calma descalço, desconforto com sapatos | WornItems/BodyLocation |
| Inventário | **Prepared**: estresse cai se carregar água, comida e arma | container + tags/fluidos |
| Armas | **Recoil Junkie**: tiro reduz tristeza, mas aumenta pânico | `OnWeaponSwingHitPoint` |
| Combate | **Clean Kill**: recompensa por matar com uma categoria de arma | `OnZombieDead` + last hit |
| Medicina | **Self-Sufficient**: tratar a própria ferida reduz estresse | medical Timed Actions |
| Leitura | **Bookworm**: concluir livro reduz tristeza; interromper aumenta tédio | `ISReadABook` |
| Craft | **Maker's High**: completar receita difícil reduz estresse | craft action / `OnMakeItem` |
| Veículo | **Engine Whisperer**: calma ao dirigir motor em boa condição | vehicle condition + speed |
| Lugar | **Homebody**: recupera stats dentro da construção marcada | square/building + ModData |
| Corpos | **Mortician**: menos doença/estresse perto de corpos | square/corpse scan limitado |
| Animais | **Shepherd**: humor melhora ao cuidar de animais estressados | animal Timed Actions |
| Nutrição | **Protein Driven**: benefício com proteína alta | Nutrition + world hours |
| Fluidos | **Tea Ritual**: bebida pura específica alivia pânico | FluidType/ratio + drink hooks |
| Sono | **Power Napper**: primeira soneca curta do dia recupera Endurance | sono + world age + ModData |
| Ferimentos | **Battle Scarred**: cada parte já curada concede tolerância temporária | snapshot de timers + ModData |
| Som | **Startle Response**: buzina/trovão causa pânico local | vehicle/weather events |

## 28. Checklist para implementar cada trait

1. **Defina a fonte da verdade.** Stat, moodle, item, fluido, parte corporal, ação ou estado do mundo?
2. **Escolha o hook mais estreito.** Evento específico antes de polling; polling antes de patch amplo.
3. **Defina autoridade.** Quem detecta? Quem valida? Quem altera?
4. **Defina unidade.** Segundo real, minuto do jogo, hora do mundo, litros, fração consumida ou progresso da ação?
5. **Inicialize sem falsos positivos.** Save carregado já pode ter ferida, item, stat ou cooldown.
6. **Evite dupla aplicação.** Flag por ação, hook versionado e cooldown de servidor.
7. **Use limites do registry.** `stat:clamp`, `getMinimumValue`, `getMaximumValue`.
8. **Persista uso único/cooldown.** Namespace no `ModData`.
9. **Sincronize explicitamente.** Stat, dano, parte, item ou objeto correto.
10. **Teste SP, host e servidor dedicado.** São três contextos, não um.
11. **Teste cancelamento.** Antes de começar, durante, no final e após consumo parcial.
12. **Teste save/load e reconexão.** Especialmente usos únicos e snapshots.
13. **Teste sem trait e ao morrer.** O hook existe para todos; o efeito não.
14. **Deixe constantes calibráveis e logs removíveis.** Balanceamento raramente sobrevive ao primeiro playtest.

## 29. Armadilhas confirmadas ou prováveis

- Javadoc não garante chamada Lua em todo contexto (`isSitting()`).
- A B42.20.2 não oferece `Events.OnEat`; consumo de comida exige hook na Timed Action.
- `OnPlayerGetDamage` não significa nova ferida e pode ser local.
- Queimadura, sangramento, fome e doença causam dano repetido.
- Líquidos usam containers e múltiplas Timed Actions; não trate tudo como `Food`.
- IDs finais de receitas não revelam necessariamente todos os ingredientes.
- `OnTick` pode ocorrer dezenas de vezes por segundo.
- listas Java usam índices `0 .. size()-1`, não `1 .. #table`.
- `getOnlinePlayers()` é o caminho do servidor; `getPlayer()` é local/singleplayer.
- som audível ao usuário não atrai automaticamente zumbis.
- mudar ferida sem recalcular/sincronizar pode deixar UI, vida e rede divergentes.
- limpar mordida sem limpar o estado geral de infecção pode salvar a pele e manter a sentença de morte.
- wrapper duplicado depois de `OnResetLua` pode aplicar efeito duas vezes.
- nomes de métodos não são consistentes: `SetBitten`, `setCut`, `scratched`, `isCut`.
- valores que o vanilla deriva podem ser sobrescritos na atualização seguinte.
- `ModData` persiste, mas não concede autoridade nem transmissão automática.

## 30. Fontes e índice oficial

Pontos de partida oficiais:

- [Índice completo de classes](https://projectzomboid.com/modding/allclasses-index.html)
- [LuaManager.GlobalObject](https://projectzomboid.com/modding/zombie/Lua/LuaManager.GlobalObject.html)
- [CharacterTrait](https://projectzomboid.com/modding/zombie/scripting/objects/CharacterTrait.html)
- [IsoGameCharacter](https://projectzomboid.com/modding/zombie/characters/IsoGameCharacter.html)
- [IsoPlayer](https://projectzomboid.com/modding/zombie/characters/IsoPlayer.html)
- [Stats](https://projectzomboid.com/modding/zombie/characters/Stats.html)
- [CharacterStat](https://projectzomboid.com/modding/zombie/characters/CharacterStat.html)
- [Moodles](https://projectzomboid.com/modding/zombie/characters/Moodles/Moodles.html)
- [MoodleType](https://projectzomboid.com/modding/zombie/scripting/objects/MoodleType.html)
- [BodyDamage](https://projectzomboid.com/modding/zombie/characters/BodyDamage/BodyDamage.html)
- [BodyPart](https://projectzomboid.com/modding/zombie/characters/BodyDamage/BodyPart.html)
- [Nutrition](https://projectzomboid.com/modding/zombie/characters/BodyDamage/Nutrition.html)
- [InventoryItem](https://projectzomboid.com/modding/zombie/inventory/InventoryItem.html)
- [ItemContainer](https://projectzomboid.com/modding/zombie/inventory/ItemContainer.html)
- [ItemTag](https://projectzomboid.com/modding/zombie/scripting/objects/ItemTag.html)
- [WornItems](https://projectzomboid.com/modding/zombie/characters/WornItems/WornItems.html)
- [Food](https://projectzomboid.com/modding/zombie/inventory/types/Food.html)
- [HandWeapon](https://projectzomboid.com/modding/zombie/inventory/types/HandWeapon.html)
- [FluidContainer](https://projectzomboid.com/modding/zombie/entity/components/fluids/FluidContainer.html)
- [FluidType](https://projectzomboid.com/modding/zombie/entity/components/fluids/FluidType.html)
- [FluidCategory](https://projectzomboid.com/modding/zombie/entity/components/fluids/FluidCategory.html)
- [SealedFluidProperties](https://projectzomboid.com/modding/zombie/entity/components/fluids/SealedFluidProperties.html)
- [BaseVehicle](https://projectzomboid.com/modding/zombie/vehicles/BaseVehicle.html)
- [PerkFactory.Perks](https://projectzomboid.com/modding/zombie/characters/skills/PerkFactory.Perks.html)
- [SandboxOptions](https://projectzomboid.com/modding/zombie/SandboxOptions.html)
- [IsoGridSquare](https://projectzomboid.com/modding/zombie/iso/IsoGridSquare.html)
- [IsoCell](https://projectzomboid.com/modding/zombie/iso/IsoCell.html)
- [IsoRoom](https://projectzomboid.com/modding/zombie/iso/areas/IsoRoom.html)
- [IsoBuilding](https://projectzomboid.com/modding/zombie/iso/areas/IsoBuilding.html)
- [IsoZombie](https://projectzomboid.com/modding/zombie/characters/IsoZombie.html)
- [IsoAnimal](https://projectzomboid.com/modding/zombie/characters/animals/IsoAnimal.html)
- [ClimateManager](https://projectzomboid.com/modding/zombie/iso/weather/ClimateManager.html)
- [GameTime](https://projectzomboid.com/modding/zombie/GameTime.html)
- [WorldSoundManager](https://projectzomboid.com/modding/zombie/WorldSoundManager.html)

Para eventos e Timed Actions, a melhor referência prática continua sendo o Lua vanilla da mesma build: procure primeiro por `Events.NomeDoEvento.Add` e pela classe `IS...Action` correspondente. A Javadoc mostra a superfície Java; o Lua vanilla mostra a receita que o jogo realmente executa.

---

**Resumo operacional:** quase qualquer trait pode ser montado como `condição observável + hook de frequência adequada + estado persistente opcional + efeito autoritativo + sincronização`. A parte difícil raramente é alterar o valor. É provar que o evento aconteceu exatamente uma vez, no lado certo da rede, sem confundir consequência periódica com causa nova.
