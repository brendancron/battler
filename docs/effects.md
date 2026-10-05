# Buffs and debuffs

In battle each character has a list of effects on it. A **buff** helps the
character and a **debuff** hurts it.

## Examples

| Effect          | Kind   | What it does                                          |
|-----------------|--------|-------------------------------------------------------|
| Taunt           | buff   | Raises the targeting tier to 1 ([targeting.md](targeting.md)) |
| Attack up       | buff   | The character's attack × 1.5 (50% higher)             |
| Defense down    | debuff | The character's defense ÷ 1.5 (33% lower)             |
| Poison 5        | debuff | The character takes 5 damage at the end of each round |

Some effects change a stat, some change a rule such as targeting, and some do
something on their own each round, like poison.

## Duration

An effect stays until it removes itself, or until a cleanse, dispel or steal
takes it off. How long that is depends on the effect:

- Many effects tick at the end of the round ([events.md](events.md)): they
  update themselves, such as counting down, and remove themselves when they
  are done. A **regen** that lasts 3 rounds counts down from 3.
- Some effects end on an event instead. A **barrier** blocks one hit and
  then goes away.
- Some never remove themselves. **Poison** stays until it is cleansed.

## Stacking

Every effect has a **type**, such as poison or attack up. A character holds at
most one effect of each type, and each type decides what happens when it is
applied to a character that already has it: add to it, refresh its duration,
replace it, leave it alone, or anything else. (Whether a type is an id or an
actual type in the code is left to the implementation.)

- **Poison** stacks by adding: 3 poison then 5 poison is one 8 poison.
- **Regen** doesn't stack.
- **Attack up** doesn't stack: a second attack up leaves it at × 1.5.

An up and a down are different types, so they stay separate entries. Both apply
to the stat, and since a down divides by what an up multiplies by, they cancel
out. They stay separate so either one can be cleansed or dispelled on its own,
leaving the other in place.

| Effects                         | Entries                            | Attack multiplier |
|---------------------------------|------------------------------------|-------------------|
| Attack up                       | attack up × 1.5                    | 1.5               |
| Attack up, attack up            | attack up × 1.5                    | 1.5               |
| Attack down                     | attack down ÷ 1.5                  | ≈ 0.67            |
| Attack up, attack down          | attack up × 1.5, attack down ÷ 1.5 | 1                 |
| Same, then attack down cleansed | attack up × 1.5                    | 1.5               |

## Shield

A shield is temporary HP on top of a character's HP, and it can go above max
HP: a 30 shield on a character at 100/100 makes its effective HP about 130.

- Damage takes the shield first, direct or indirect (poison included);
  whatever is left over comes off HP.
- A shield isn't HP. Anything that looks at HP sees only real HP, so a
  Barbarian at 20 HP with a 50 shield still has Rage at its 20-HP
  strength ([characters.md](characters.md#barbarian)).
- Shields stack by adding: a 20 shield and then a 30 shield is a 50 shield.
- Shields decay: at the end of every round, each character's shield loses 20
  (a placeholder). A 50 shield lasts three round ends: 50 → 30 → 10 → gone.
- Unlike a barrier, which blocks one hit whatever its size, a shield soaks a
  set amount of damage spread over any number of hits.

## Barrier

A barrier is a buff that blocks the next **direct** hit on the character
([events.md](events.md#direct-and-indirect-damage)) completely, whatever its
size, and then is used up. It isn't a shield.

- **Only direct hits.** A move hitting the character is blocked. Indirect
  damage, like poison or Retaliate, goes past it and doesn't use it up.
- **One hit per barrier.** Each hit of a multi-hit move is a separate hit, so
  a barrier stops only the first of Flurry of Blows' hits. Each target of an
  area move is hit separately, so Brimstone is blocked only on the targets
  that have a barrier.
- **The rest of the move still lands.** Only the damage is blocked: Reap's
  max HP loss, a debuff the move adds, and so on all still happen. A blocked
  hit deals 0 damage, so a drain heals nothing from it.
- **Barriers come before shields.** A blocked hit never reaches the shield.
- A blocked hit doesn't count as damage taken, so it sets off nothing that
  reacts to being hit, like Retaliate.
- **Barriers stack as charges**, like potions: a second barrier blocks a
  second hit. They don't wear off over time.

## Speed up and speed down

**Speed up** is a buff that moves the character one speed tier faster
([speed.md](speed.md)); **speed down** is a debuff that moves it one tier
slower. The Bard's Allegro and Largo give them
([characters.md](characters.md#bard)).

- They last for the character's next turn: they go away when that turn ends,
  like Heal Block counting down on the character's own turns.
- They move one tier, never more, and don't stack: a second speed up still
  moves one tier, as attack up stays at × 1.5.
- Tiers stop at the ends: a Fast character with speed up stays Fast, and a
  Slow one with speed down stays Slow.
- Up and down are separate types, like attack up and down, so a character
  with both is at its own tier, and cleansing or dispelling one leaves the
  other.

## Invincible

A buff: the character takes no damage at all while it has it. The Fairy's
Fairy Ring gives it until the end of the round
([characters.md](characters.md#fairy)).

- **All damage.** Direct hits, area hits and indirect damage (poison,
  Retaliate) all do nothing. Max HP loss from Reap and fatigue doesn't happen
  either.
- **It comes before barriers and shields.** A hit on an invincible character
  never reaches them, so it uses up no barrier and none of the shield.
- **Prevented damage isn't damage taken,** so it sets off nothing that reacts
  to being hit, like Retaliate or a Greater Health Potion, and a drain like
  Soul Siphon heals nothing from it.
- **Only damage.** The rest of a move still lands, as with a barrier: Hex,
  Heal Block, Largo and any other debuff still go on. Blocking debuffs is the
  Alchemist's Panacea's job. (Settled for now; could change later.)
- It goes away after the round's end-of-round damage, so poison and fatigue
  that round are covered.
- Like any buff, it can be dispelled or stolen.

## Bleed

A debuff that deals a set amount of damage at the end of each round for a
set number of rounds, then goes away. The Vampire's Hemorrhage gives
**bleed 10 for 3 rounds** ([characters.md](characters.md#vampire)): 30 damage
in all.

- Its damage is indirect, like poison's: a shield soaks it, and a barrier
  doesn't stop it.
- **It's damage dealt by whoever put it on**, so it counts for that
  character's effects. The Vampire's Sanguine heals from each tick.
- It doesn't stack: bleeding again resets it to 3 rounds rather than adding.
- A cleanse takes it off at once.
- It's separate from poison. Poison wears down by 1 a round and merges by
  adding, and has no owner; bleed is flat, refreshes, and belongs to the
  character that caused it.

## Chill and freeze

Two debuffs that work as a pair. The Cryomancer gives them
([characters.md](characters.md#cryomancer)).

**Chill** does nothing on its own. Chilling a character that is already
chilled freezes it: the chill is used up and replaced with a freeze.

**Freeze** makes the character skip its next turn, then goes away, like the
Witch's Hex ([characters.md](characters.md#witch)):

- "Next turn" is its next chance to act: this round's if it hasn't acted yet,
  otherwise next round's.
- Its turn still comes up in the usual order and it does nothing, so no one
  else's order changes.

Details (placeholders, to revisit once a character uses them):

- **Chill lasts 2 rounds:** applied in round 1, it wears off at the end of
  round 2, so the second chill has to come soon after the first.
- **A frozen character can't be chilled.** Chilling it while it's frozen does
  nothing, so freezes can't be chained back to back by one team.
- Both are debuffs, so a cleanse removes them, and a Panacea blocks a chill
  like any debuff: a Panacea on a chilled character stops the freeze.
- Freeze and Hex are separate types. A character with both loses its next
  two turns, one to each.
- A chilled character hit by an area move that chills is chilled once by it,
  so one area move freezes only the foes that were already chilled.

## Cryosleep

A buff from the Frost Giant's Cryosleep ([characters.md](characters.md#frost-giant)).
The character sleeps in ice: it skips its next turn, and until that turn has
passed it takes no damage. Then it wakes and the buff goes away.

It works like a freeze and Invincible together, but it's its own effect:

- **It's a buff,** because an ally gives it to help. A Panacea doesn't block
  it, and a cleanse doesn't remove it. A dispel does, and wakes the character
  at once: it can act on its next turn and takes damage again.
- **No damage** works as for Invincible: direct and indirect damage and max
  HP loss are all prevented, it comes before barriers and shields, and
  prevented damage isn't damage taken. Debuffs still land.
- **The skipped turn** works as for a freeze: "next turn" is this round's if
  the character hasn't acted yet, otherwise next round's, and its turn still
  comes up in the order with nothing done. Like freeze and Hex, it takes a
  turn of its own: a character in Cryosleep and Hexed loses two.
- It isn't a freeze, so the character can still be chilled while it sleeps.

## Silence

A debuff: the character can only use its basic attack
([moves.md](moves.md#silence)).

## Heal Block

A debuff: the character can't be healed at all while it lasts. (The
Construct can never be healed, by its passive
([characters.md](characters.md#construct)).) Every kind of
healing is blocked: heals from moves, regen, Health Potions, Vigil and Soul
Siphon's drain. Shields aren't healing, so they still work.

It lasts a set number of the character's own turns and counts down when each of
its turns ends; the Monk's Crippling Blow gives 2.

## Removing and moving effects

Moves can act on the effects themselves:

- **Cleanse** removes debuffs from an ally.
- **Dispel** removes buffs from a foe (the Paladin's Smite). Passives can't be
  dispelled.
- **Steal** takes a buff off a foe and puts it on the user (the
  Swashbuckler's Plunder, [characters.md](characters.md#swashbuckler)).

Some effects won't make sense to cleanse, dispel or steal, so effects will need
a way to opt out of some or all of these.

## Passives

A character's passive ([characters.md](characters.md)) is a buff on it from
the start of the battle. It can use triggers like any other effect, but it is
immune to removal: it can't be cleansed, dispelled or stolen.

## Poison

Poison has an **amount**: at the end of every round, a character with "5
poison" takes 5 damage, and then its poison drops by 1. So 5 poison deals
5 + 4 + 3 + 2 + 1 = **15** over five rounds and then is gone. In general, n
poison deals n(n+1)/2 in total.

- It's a trigger on the end-of-round event ([events.md](events.md)), and its
  damage is indirect: a shield soaks it, and a barrier doesn't stop it.
- Poison merges by adding to what's left: 5 poison, one round end (now 4),
  then 5 more is 9 poison.
- A cleanse takes it off at once.
- Poison may get synergies later (effects that read or spend a character's
  poison), so the amount is kept as one number on one effect.

Before this, poison dealt the same amount every round and never wore off.
