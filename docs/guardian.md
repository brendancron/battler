# Guardian (generation 4)

Design for the next AI ([balance.md](balance.md#generations)). Not built yet.

**Its one new idea: it predicts what the foes will do this round, and values
protection by the damage it would actually stop.** Otherwise it plays like
the Strategist (generation 3): the Tactician's scores, the cooldown cost and
knockouts valued by the foe's threat.

## Why

The AIs today value protection blind. In `scoring.cx`:

| Effect | Worth today | So |
|---|---|---|
| Invincible (Fairy Ring) | a flat 15 | the same on every ally: it lands on a random one |
| Shield (Pixie Dust, Bulwark) | half its size | the same on an ally nobody is hitting |
| Barrier (Sanctuary) | 15, then 6 | no idea which hit it blocks |
| Taunt (Lockdown, Guard, Shield Bash) | 10 | no idea whose hits it pulls away |
| Greater Health Potion | by how close the ally is to the line | the closest to the design; it guesses "one hit is about 25" |
| Cleanse | 15 a debuff | the same for a harmless debuff as for Heal Block on a healer |

Staged games (Stormbringer, Assassin, Monk, Grim Reaper against Warlock,
Fairy, Knight, Cleric; Tactician on both sides) showed what that costs:

- Fairy Ring went to the Witch, the Knight, the Fairy itself, the Cleric;
  once in 14 games to the Warlock.
- In round 1 the Fairy always chose Pixie Dust (worth about 30: 7.5 on each
  of 4 allies) over Ring (15), with Thunderstorm and Backstab about to land
  on the Warlock. Ring would have stopped the Backstab: 0 damage, not 63.
- In round 2 the Assassin, acting first in the Fast phase, finished the
  Warlock with Execution while the Fairy's Ring went on a Cleric at full HP.

The Fairy (33% in v0.0.1) and the Alchemist (43%) are the characters whose
value is mostly protection and timing, so they suffer most. Every support
with a shield, barrier, taunt or potion does too.

## The prediction

At the start of each of its turns the Guardian works out an **expected
damage** for each of its allies: how much the foes still to act will deal
to that ally before the protection it's thinking of runs out.

1. **Which foes still act.** The foes that haven't acted this round, in the
   turn order (`order.cx`). For an effect that lasts into the next round
   (a shield, a taunt for 2 rounds), every foe next round counts too, at
   half (placeholder): next round is further off and less sure.
2. **What each foe does.** Each of those foes is assumed to make its Greedy
   pick (`score` in `scoring.cx`): the move and target that score best for
   it right now. Greedy is cheap, it's what most foes' choices look like,
   and it already goes after the low, the frail and the big hits.
   Targeting tiers are respected, as they are for the foe: a taunt or
   Shadowed changes who it can pick.
3. **Damage on each ally.** Each predicted move's damage to each of the
   Guardian's allies, added up: a single-target move on its target, an area
   move on every ally. Poison and fatigue due at round end are added too.

Expected damage is a forecast, not a promise, so a protection is worth the
damage it would stop under the forecast, plus a **save bonus** when the
forecast says the ally falls without it and lives with it.

The save bonus is the ally's own threat, the way the Strategist values
knocking a foe out: its best hit (`output`) × the kill weight, more again
(placeholder × 1.5) if it hasn't acted yet this round, since a save then
buys its turn. A Warlock that hasn't fired is the biggest save there is.

## What it changes

Each is a `revalue` (like the Tactician's `scaling_worth`): it replaces the
Greedy value of putting an effect on an ally, or of an action.

| Effect or action | Worth to the Guardian |
|---|---|
| **Invincible** | all the ally's expected damage before the round ends, plus the save bonus if it's lethal. Nothing if no foe still to act can reach it. |
| **Shield** | the expected damage it would soak before it decays, no more than its size, plus the save bonus if it turns a knockout into a survival. Pixie Dust adds this up over every ally, so it beats Ring only when the damage really is spread out. |
| **Barrier** | the biggest single expected direct hit on the ally (the one it would block), plus the save bonus. |
| **Taunt** | the expected damage it pulls off the allies (the single-target hits on them that would go to the taunter instead), less what that damage does to the taunter, plus the save bonus for any ally it saves. Taunting is worth a lot when a carry is about to be focused, and little when the foes would hit the taunter anyway. |
| **Greater Health Potion** | the heal, if the forecast takes the ally below the line, in full; plus the save bonus if the heal is what keeps it alive. Nothing if the forecast never reaches the line. |
| **Heal** | as now (HP restored), plus the save bonus when it lifts the ally out of the forecast's kill range. |
| **Cleanse** | each debuff by what it does: an attack or defense change through `pressure` (as the Tactician values it), Heal Block by the healing it would stop this round, a Hex by the turn it would cost. |

Protection on an ally the forecast leaves alone is worth little, so the
Guardian stops wasting turns on it and attacks instead.

## Effects on the characters

What should change, for checking against the balance checker:

- **Fairy.** Ring on whoever is about to be focused, before they're hit;
  in round 1 that's usually the frail carry, so it beats Pixie Dust there.
  Pixie Dust when the forecast spreads damage (an area hit coming).
- **Alchemist.** Greater Health Potion on the ally the forecast takes below
  the line, not the one that's merely lowest. Energize is already valued
  well (the Tactician).
- **Knight, Construct, Frost Giant, Paladin.** Taunts and Guard used when a
  frail ally is about to be focused, and held when it isn't. Bulwark on the
  ally under threat.
- **Cleric.** Sanctuary's barrier on the ally facing the biggest hit; Purify
  on the debuff that matters.

## Cost

Predicting means scoring each foe's moves on each of the Guardian's turns:
up to 4 foes × their moves × their targets, Greedy-style, once per turn (not
once per move it's considering). That's about the same work as one more
Greedy turn per foe, so a game should take well under twice as long as a
Tactician's. If it's slower than that, the forecast can skip foes with no
damaging move ready.

## Testing

As for every AI, test scoring rules, not which of two tuned moves wins:

- Invincible is worth the expected damage to the ally, and nothing on an
  ally no foe still to act can reach.
- A save is worth more than the HP it saves; more again for an ally that
  hasn't acted.
- A shield is worth no more than its size or the expected damage, whichever
  is smaller.
- A taunt is worth the damage it pulls off the others, less what it costs the
  taunter.
- The forecast respects taunts and targeting tiers.

Then the balance checker: `--ai Strategist,Guardian` should show the
Guardian winning. To see what it does for the Fairy and the Alchemist, play
games with only the Guardian (`--ai Guardian`, into a version of its own)
and compare their win rates with a Tactician-only run.

## Placeholders

- Next round's foes count at half.
- The save bonus: the ally's best hit × the kill weight (3), × 1.5 if it
  hasn't acted.
