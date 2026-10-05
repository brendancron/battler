# Guardian (generation 3)

The third AI generation ([balance.md](balance.md#generations)),
`src/agents/guardian.cx`. It replaced the Strategist, which counted what a
cooldown costs and didn't beat the Tactician by enough to keep.

**Its one new idea: it forecasts what the foes will do, and values
protection by the danger it takes away.** Otherwise it plays like the
Tactician (an effect that changes attack or defense is worth what it does to
the damage each side can deal), and it keeps the Strategist's view of
knockouts: knocking a foe out is worth that foe's best hit × 3, not a flat
50.

## Why

The older AIs value protection blind. In `scoring.cx`:

| Effect | Worth to Greedy and the Tactician | So |
|---|---|---|
| Invincible (Fairy Ring) | a flat 15 | the same on every ally: it lands on a random one |
| Shield (Pixie Dust, Bulwark) | half its size | the same on an ally nobody is hitting |
| Barrier | 15, then 6 | no idea which hit it blocks |
| Taunt (Lockdown, Guard, Shield Bash) | 10 | no idea whose hits it pulls away |
| Speed up and down (Allegro, Largo) | a flat 10 or 12 | no idea who acts before whom |
| Greater Health Potion | by how close the ally is to the line | guesses "one hit is about 25" |

Staged games (Stormbringer, Assassin, Monk, Grim Reaper against Warlock,
Fairy, Knight, Cleric; Tactician on both sides) showed the cost. Fairy Ring
went to the Witch, the Knight, the Fairy itself or the Cleric, and to the
Warlock once in 14 games. In round 1 the Fairy always chose Pixie Dust (worth
about 30) over Ring (15) with Backstab about to land on the Warlock. With
the Guardian on the Fairy's side, Ring went on the Warlock in round 1 in all
8 games, and the Warlock fired.

## The forecast

1. **Each foe still to act** this round (not fainted, takes turns, not
   skipping) is assumed to make its **Greedy pick**: the move and target
   that score best for it right now, with no noise. Targeting tiers apply,
   so a taunt or Shadowed changes who it can pick.
2. Each picked move's hits on the Guardian's side are collected: a
   single-target hit on its target, an area hit on everyone.
3. A hit is **before** the ally if the foe's tier is faster than the ally's,
   or the same (taken as first, to be safe), and the ally hasn't acted yet.
   The Guardian itself is acting now, so nothing is before it.

**Next round** is forecast the same way with every foe, against the side as
it will be after the round ends: Invincible is over and shields have lost
their decay.

## Danger

Each ally's hits are run against it: the ones before it first, then the
rest. Invincible stops them, a barrier blocks the next direct hit, a cap
(Ironclad) trims each, a shield soaks, and a Greater Health Potion fires
when the ally drops below its line. Then the ally counts:

- the **HP it would lose** (not capped at its HP, so raising its HP changes
  only whether it falls);
- if it would fall, its **save value**: its best hit × 3, + 25 for being in
  the fight at all;
- if it would fall **before it acts**, its best hit again: the action it
  loses.

The **danger** is this round's total plus next round's at half (an ally that
falls this round isn't counted next round).

## What the forecast values

- **Protection and speed.** Putting an effect on someone is worth the
  danger before less the danger with the effect on (worked out by trying
  it). That covers Invincible, shields, barriers, taunts, damage caps,
  Greater Health Potions and speed up on an ally, and speed down on a foe.
  An effect that also changes attack or defense keeps the Tactician's value.
  Pixie Dust adds up a shield on each ally, so it beats Ring only when the
  forecast spreads the damage.
- **Heals get a rescue bonus:** the danger a move's heals take away, by
  trying the healed HP. Since HP lost doesn't depend on HP, that's only the
  falls they prevent; the HP restored is already in the score.
- **Knockouts** are worth the foe's best hit × 3 instead of a flat 50.

Protection on an ally the forecast leaves alone is worth nothing, so the
Guardian stops spending turns on it.

## What it means for the characters

- **Fairy:** Ring on whoever is about to be focused; in round 1 that's
  usually the frail carry. Pixie Dust when an area hit is coming.
- **Alchemist:** Greater Health Potion on the ally the forecast takes below
  the line.
- **Tanks (Knight, Construct, Frost Giant, Paladin):** taunts when a frail
  ally is about to be focused, held when not. Bulwark on the ally under
  threat.
- **Bard:** Allegro's speed up is worth the action of an ally that would
  otherwise fall first; Largo's speed down the same for a foe.
- **Cleric:** heals that keep someone standing.

## Not covered yet

- Cleanses are still worth 15 a debuff (the Greedy value).
- The forecast doesn't know about the Fairy's Fae Bargain: it counts the
  first ally to fall as fallen.
- Overclock and other cooldown cuts are still worth 5 a cooldown a turn.
- Allegro's attack up lasts one turn but the Tactician counts it as 2
  rounds.

## Cost

The forecast scores every foe's moves Greedy-style: once at the start of the
Guardian's turn, then once more for each protective option it tries. A game
takes a little longer than a Tactician's.

## Placeholders

- Next round counts at half.
- Save value: best hit × 3 + 25. Kill weight: 3.
- The same tier counts as acting first.
