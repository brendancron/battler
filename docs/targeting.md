# Targeting

When a character uses a move that needs a target, it picks one after picking
the move. Which foes it may pick is limited by **targeting tiers**.

## Targeting tiers

- Every character has a targeting tier, a number. Normally it is 0.
- When picking a target, only the foes in the **highest** targeting tier among
  the foes still standing can be picked.
- If several foes share that tier, the player chooses among them.
- Targeting tiers only apply to moves where the player picks a single target.
  Area moves ignore them and hit every foe still standing, and so do moves that
  choose their own targets, like the Assassin's Execution.
- Targeting tiers only limit picking a foe. Any ally can always be picked.

## Picking in order

A move that hits several foes in an order the player picks (the
Stormbringer's Chain Lightning, [characters.md](characters.md#stormbringer))
applies targeting tiers one pick at a time: each pick must be a foe not yet
picked, from the highest tier among the foes standing that haven't been
picked. Taunting foes come first and shrouded ones last.

## Taunt

A taunt raises a character's targeting tier, usually to 1. While it lasts,
foes have to target the taunting character. If several foes are taunting,
the player picks one of them.

## Shroud

A reverse taunt: it lowers a character's targeting tier, for example to -1, so
foes can only pick it when no one in a higher tier is left. The Ninja is
always shrouded, by its passive ([characters.md](characters.md#ninja)).

## Ignoring restrictions

A buff can let a character ignore targeting tiers. While it has the buff, it
may pick any foe still standing.

A move can too: the Swashbuckler's Pistol Shot may pick any foe standing,
whatever their tiers ([characters.md](characters.md#swashbuckler)). Area
moves and the Assassin's Execution ignore tiers as well, but don't pick.

## Examples

Foes F1, F2, F3.

| Targeting tiers     | Can pick   |
|---------------------|------------|
| F1 0, F2 0, F3 0    | F1, F2, F3 |
| F1 0, F2 1, F3 0    | F2         |
| F1 1, F2 1, F3 0    | F1, F2     |
| F1 -1, F2 0, F3 0   | F2, F3     |
| F1 -1, F2 -1, F3 -1 | F1, F2, F3 |
