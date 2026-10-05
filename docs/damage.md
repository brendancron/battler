# Damage

```
damage = power × attack ÷ defense
```

A move's **power** is the percentage of a character's HP it takes off when the
attacker's attack equals the defender's defense. Every character has 100 HP, so
power 30 against an equal match deals 30 damage.

`attack` and `defense` are the current values, after ups, downs and passives
([effects.md](effects.md)).

| Power | Attack | Defense | Damage |
|-------|--------|---------|--------|
| 30    | 100    | 100     | 30     |
| 30    | 150    | 100     | 45     |
| 30    | 100    | 150     | 20     |
