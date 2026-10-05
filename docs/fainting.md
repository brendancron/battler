# Fainting, corpses and resurrection

## Corpses

When a character faints, it leaves its **corpse** on the field, in its slot.
A fainted character is out of the fight: it doesn't act, can't be targeted
like a standing character, and doesn't count toward its team still standing.
Its corpse stays until something removes it. Every fall leaves a corpse,
except for a character with no max HP left (below), or a foe knocked out by
the Grim Reaper ([characters.md](characters.md#grim-reaper)).

## Resurrection

A character that has fainted can be **resurrected** if its corpse is still
there. It comes back standing at a percentage of its max HP that depends on
what resurrected it, and its corpse is gone, since the character is using it
again.

Resurrection comes from a move (the Cleric's Resurrection) or from the
Paladin's Last Rites when it falls ([characters.md](characters.md#paladin)).

What it comes back with:

- **No effects but its passive.** Every buff and debuff it had is gone; its
  passive, which can't be removed, stays.
- **The same max HP.** Max HP lost before it fainted (to Reap, say) stays
  lost, and the resurrection percentage is of that reduced max.
- **The same cooldowns.** A move that was cooling down when it fainted is
  still cooling down, with the same number of turns left.

If it's resurrected partway through a round and hadn't acted yet that round,
it can still act, following the usual phase rules ([speed.md](speed.md)): a
character can act in its own tier's phase or any later one, so one revived
during the Normal phase acts then if it's Fast or Normal, or in the Slow phase
if it's Slow. If it had already acted that round before fainting, it waits for
the next round.

A character whose max HP has dropped to 0 can never come back: its corpse is
removed the moment it faints.

## Removing corpses

Some abilities target corpses and remove them, and the Grim Reaper's Soul
Harvest removes the corpse of every foe that falls while it stands. The Necromancer's
Wither removes the corpse of every foe it knocks out, and its Raise Dead
turns an ally's corpse into a skeleton ([summons.md](summons.md)). The
Jester's Puppeteer takes a foe's corpse and raises it as a puppet on the
Jester's team. A character whose
corpse has been removed can no longer be resurrected. Corpse removal is niche: it's a
counter to teams built around resurrection, not something most teams need.

## Losing

A team loses as soon as all its characters have fainted, even if their corpses
are still there and could be resurrected. Minions ([summons.md](summons.md)) don't count:
a team with only minions left has lost. If both teams lose at the same moment, the game is a draw
([fatigue.md](fatigue.md#how-a-game-ends)).

## Targeting corpses

Corpses can be targeted by abilities that work on them: resurrecting one, or
removing one. Which corpses an ability can reach (allies', foes', or both)
depends on the ability.
