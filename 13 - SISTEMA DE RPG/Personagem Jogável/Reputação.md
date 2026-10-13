# Reputação

Reputação representa relações com grupos específicos e varia de **-3 a +3**. Cada nível fornece um bônus igual ao valor atual, além de benefícios específicos descritos para o grupo.

| Valor | Relação |
| --- | --- |
| -3 | Inimigo Mortal |
| -2 | Hostil |
| -1 | Mal Visto |
| 0 | Neutro |
| +1 | Conhecido |
| +2 | Aliado |
| +3 | Patrono |

## Relações entre Grupos

1. Comece pela reputação direta com o grupo.
2. Se a reputação com um grupo aliado for maior, use-a no lugar da direta, limitada pelo valor da relação entre os grupos.
3. Subtraia a maior reputação com uma facção inimiga, também limitada pelo valor da relação registrado para o grupo-alvo.

**Exemplo de limite:** uma relação de +2 permite considerar a reputação aliada até +2, mesmo que a reputação com aquele aliado seja +3.

A fórmula utiliza a reputação direta ou a maior reputação aliada, menos a maior reputação inimiga.

Consulte [[Relações entre Facções no Sistema]].
