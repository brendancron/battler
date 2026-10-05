# Marshal (generation 4)

The fourth AI generation ([balance.md](balance.md#generations)),
`src/agents/marshal.cx`.

**Its one new idea: a character is worth everything it brings, not just its
best hit.** Otherwise it plays exactly like the Guardian
([guardian.md](guardian.md)): the same forecast of the foes' moves, the same
danger, the same value for protection.

## Why

The Guardian weighs two things by a character's damage:

- **Saving an ally**: a fall costs the ally's best hit × 3 + 25, and its best
  hit again if it falls before acting.
- **Knocking out a foe**: worth the foe's best hit × 3, instead of a flat 50.

A healer, a protector or an aura-holder hits for almost nothing, so to the
Guardian it was hardly worth saving and hardly worth killing. In v0.0.4
staged games a Knight put Bulwark on a full-HP Ranger while the Shaman beside
it was about to fall, and nobody went after the foe's Cleric (63% in v0.0.3).

## Contribution

What a turn of the character's is worth on average (`contribution`):

1. Each move's **face value**: the most one use could bring now, over every
   target it allows. Each action counts as:

   | Action | Face value |
   |---|---|
   | Damage, a drain, an area drain | what can land on each foe (no more than its HP and shield) |
   | A heal | the amount, up to the ally's max HP (not what it's missing now: a healer is worth its heals before anyone is hurt) |
   | A team heal | the amount for each standing ally |
   | A shield | its size |
   | Regen | half its total (it comes slowly) |
   | Invincible | 30 |
   | Anything else | what the scores say (`worth`, `effect_worth`), e.g. a summon with an aura is 15 + 10 per ally |

2. The **contribution** is the basic attack's face value, plus, for each
   other move, what it adds over the basic attack shared over the turns its
   cooldown takes (cooldown 3: every 4th turn). Cooldowns that are running
   aren't checked: a character is worth its moves whether or not one is
   ready this turn.

A plain hitter's contribution is its hits, close to the Guardian's value. A
healer with Prayer and Purify is worth about two to three times its jab; a
Shaman with Healing Rain ready is worth a lot.

It's used everywhere the Guardian used the best hit: the save value, the
action lost by falling first, and the value of a knockout.

## What it changes

- Protection (shields, Invincible, taunts, barriers, potions, speed) goes on
  the ally that brings the most, not just the one that hits hardest: a
  Shaman about to cast Healing Rain, a Cleric.
- Damage goes to the foe that brings the most: the foe's Cleric or Shaman,
  not only its biggest hitter.

## Testing

- A healer's contribution is more than its best hit; a plain hitter's is its
  basic attack; a fallen or idle character's is nothing.
- With a healer and a weaker hitter both about to fall, the Guardian shields
  the hitter and the Marshal the healer.
- With a foe healer and a foe hitter both in reach of a knockout, the
  Guardian takes the hitter and the Marshal the healer.

Then the balance checker: `--ai Guardian,Marshal`.

## Placeholders

- Regen counts at half its total; Invincible at 30.
- The rest is the Guardian's: next round at half, save value × 3 + 25, kill
  weight 3.
