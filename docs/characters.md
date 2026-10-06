# Characters

Tiers come from [speed.md](speed.md).

The numbers below are placeholders. The live values are in
`src/content/tuning.cx` (moves and passives) and `src/content/roster.cx`
(stats and basic attacks); when they differ, the code is current.

Each character has stats, moves and a **passive**. Characters with a section
under [Designs](#designs) are designed; the "Placeholder" tables are kept only
so there is something to build against until every character is.

## Roster

Each class is a fixed character. A team has 4 of them in battle and no
reserves.

- Barbarian
- Ranger
- Warlock
- Paladin
- Cleric
- Monk
- Witch
- Alchemist
- Grim Reaper
- Shaman
- Knight
- Assassin
- Bard
- Fairy
- Necromancer
- Vampire
- Cryomancer
- Frost Giant
- Construct
- Stormbringer
- Ninja
- Swashbuckler
- Jester

## What a character has

- **Stats:** attack, defense and speed tier ([speed.md](speed.md)).
- **Moves:** 3 each, for now.
- **Passive:** an ability that is always on, without being used. It is a buff
  that can't be removed ([effects.md](effects.md#passives)).

## Strong strengths, big weaknesses

Every character should be very good at something and clearly bad at
something else, rather than solid all round. Balance comes from those trading
off against each other and against the team, not from evening every
character out.

The Warlock is the model: Writhing Depths and The Final Offering are area
nukes, but it's frail (defense 45) and Slow, so it acts last and can easily
die before it gets to. Its team has to keep it alive until it fires.

When a character is too strong, first look for the weakness it's missing and
sharpen that (the Warlock's fix was its defense, not its nuke's power). When
one is too weak, look for the strength that isn't strong enough.

Synergy is a strength too. Some characters are built to make a partner
better: the Fairy keeps a Warlock alive until it fires, and the Cryomancer
and Frost Giant chill for each other. Judge those on the team they're built
for, not only on random teams ([balance.md](balance.md#character-pairs)).

## Designs

### Barbarian

Warrior ([archetypes.md](archetypes.md)).
Stats (placeholders): attack 125, defense 72 (attack 130 until v0.0.6).

**Passive, Rage: the lower its HP, the harder it hits.** A Barbarian left at
1 HP hits like a truck, and a healthy one hits softer than its attack stat
suggests: it relies on being worn down to deal real damage. It punishes teams
that wear it down with chip damage, such as a Mage's area attacks or poison,
instead of finishing it off.

Its attack is multiplied by

```
y = c + a(1 − x/100)^b
```

where x is its current HP. `c` is the multiplier at full HP (below 1, so a
healthy Barbarian is held back), `a` sets the bonus at 0 HP (the most it can
reach is c + a) and `b` sets the shape: above 1 the bonus stays small until HP
is low and then climbs sharply.

It is an attack multiplier like attack up ([effects.md](effects.md)): it
multiplies the Barbarian's attack, so it applies to any damage that uses its
attack, and it multiplies together with attack ups and downs.

Values (v0.0.2): **c = 0.77, a = 1.3, b = 1.75**, aimed at Cleave doing about
30 damage at full HP, 45 at half and 80 near 0 to a 100-defence target.

| HP | 100  | 75   | 50   | 25   | 1    |
|----|------|------|------|------|------|
| y  | 0.77 | 0.87 | 1.16 | 1.53 | 2.05 |

**Moves**

| Move   | Target  | Effect |
|--------|---------|--------|
| Cleave | one foe | A direct hit, power 30 ([damage.md](damage.md)). Placeholder power. |
| —      |         | To be designed. |
| —      |         | To be designed. |

Rejected so far:

- **Whirlwind** (all foes): Warriors aren't meant to deal area damage.
- **A self attack-up move**: spending a turn to set up is too slow.

### Paladin

Tank ([archetypes.md](archetypes.md)). Normal ([speed.md](speed.md); Slow
until v0.0.9). All numbers here are placeholders to tinker with.
Stats (placeholders): attack 85, defense 137 (90 and 156 until v0.0.9).

**v0.0.9: protect sooner, close harder.** In v0.0.8 it won 78% of games
over in 4 rounds and 58% past round 15, but only **32% in rounds 9-14**
(the other tanks held 41-56% there), and 20% there against the Cleric. Slow,
its Guard always came after the foes had acted, so its team lost characters
mid-game, and once thinned out it couldn't close (Smite did about 29 a
turn). So it's Normal now, and Smite hits harder (25); Guard's cooldown went up
by one to pay for the earlier, more useful taunt.

**Passive: Last Rites.** When the Paladin falls, it resurrects the first
fallen ally in slot order at 50% of its max HP ([fainting.md](fainting.md#resurrection)).
(The name and the 50% are placeholders.)

- Only allies with a corpse count: one whose corpse was removed (by Soul
  Harvest, say) is skipped, and so are minions, which leave no corpse.
- If no ally has fallen, nothing happens.
- It happens every time the Paladin falls, so a Paladin that is itself
  resurrected and falls again brings back another ally.
- If the Paladin falls at the same time as the rest of its team, Last Rites
  brings one of them back and the team plays on.

**Moves**

| Move         | Target              | Effect |
|--------------|---------------------|--------|
| Smite        | one foe             | Dispels the foe's buffs ([effects.md](effects.md#removing-and-moving-effects)), then a direct hit, power 25 (20 until v0.0.9; 30 in v0.0.9's first games, about 74% for the Paladin; [damage.md](damage.md)). It used to add attack down for 2 rounds too. Cooldown 2. |
| Guard        | the user            | Taunt with defense up: the Paladin's targeting tier goes to 1 ([targeting.md](targeting.md)) and its defense is × 1.25 (× 1.5 until v0.0.9), both for 2 rounds. Cooldown 4 (3 until v0.0.9). |
| Lay on Hands | one ally, or itself | Heals the ally 15 and the Paladin 15. Used on itself, the Paladin heals 30. |

- Smite dispels first, so a shield or barrier is gone before the hit lands.
  Every buff goes, taunts included: Smiting a taunting Tank frees the
  Paladin's team to pick anyone. Passives can't be dispelled.
- Guard's taunt and defense up are one effect that counts down at the end of
  each round, like other effects ([effects.md](effects.md)). Since v0.0.9 the defense up is its own × 1.25, not a stat step, so it
  multiplies with any other defense up rather than replacing it. Used in round 1,
  it lasts through the end of round 2. Other taunts, like the Knight's, don't
  raise defense.
- Before this, the defense up came from the passive, Steadfast (defense ×1.5
  while taunting).

### Ranger

Rogue ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md)).
Stats (placeholders): attack 112, defense **45** (attack 115 and defense 55 until v0.0.9).

**Strength and weakness (v0.0.9).** It out-tempos and snipes: Fast, hard
hitting, and Keen Eye lets no taunt protect its target. But it can't take a
hit: at defense 45 it's as frail as the Warlock. A turn-1 Thunderstorm takes
42 off it and a Stormbringer-then-Assassin opener kills it outright, so
Fast teams with area damage, and Assassins, are its counters. Before v0.0.9
it topped every balance run (56-57%) and nothing really countered it (its
worst matchup was about 48%).

**Passive: Keen Eye.** The Ranger ignores targeting restrictions
([targeting.md](targeting.md#ignoring-restrictions)): it can pick any foe still
standing, so a taunt can't protect the foe it is hunting. (The name is a
placeholder.)

**Moves**

| Move          | Target  | Effect |
|---------------|---------|--------|
| Aimed Shot    | one foe | A direct hit, power 30 ([damage.md](damage.md)). |
| Hunter's Mark | one foe | A direct hit, power 15, then **Marked** for 2 rounds: hits on the foe deal × 1.3 (its defense × 10/13). Until v0.0.9 it was no hit and plain defense down (× 1.5) for 2 rounds. |
| Venom Arrow   | one foe | A direct hit, power 18, and 4 poison ([effects.md](effects.md#poison)): 10 more damage over four rounds. Cooldown 2. |

Venom Arrow's name, power 18, 4 poison and cooldown 2 are placeholders
(new in v0.0.7). It gives the Ranger a slow-burning hit
alongside Aimed Shot's burst, and it feeds the poison that the Alchemist's
Acid Flask also lays: poison adds up, so the two together stack deep on one
foe.

- **Marked is its own effect,** a debuff, not defense down: it stacks with
  defense down from anyone else (the Bard's Heckle, the Monk's Disarming
  Palm), multiplying. A second Mark on the same foe doesn't stack; it
  starts the 2 rounds again. A Panacea blocks it; a cleanse takes it off.
- The 15, the × 1.3 and the defense 45 are placeholders (v0.0.9; the user
  chose to try them and tweak later).

Rejected so far:

- **Volley** (all foes): Rogues aren't meant to deal area damage.

### Cleric

Support ([archetypes.md](archetypes.md)).

Stats (placeholders): attack 67, defense 69 (72 until v0.0.9; 70 and 75 until v0.0.6; defense 81 until v0.0.4).

**Passive: Vigil.** At the end of each round, heals the ally with the lowest
HP for 8. The Cleric counts as an ally, and a tie goes to the lowest slot.
(The name and the 8 are placeholders; it was 10 until v0.0.4.)

**Moves** (names are placeholders)

| Move    | Target                  | Effect |
|---------|-------------------------|--------|
| Prayer  | all allies, itself too  | Heals 20 each. |
| Purify  | one ally, or itself     | Cleanses the ally of debuffs ([effects.md](effects.md)), then heals it 35. Cleansing first means a Heal Block is gone before the heal lands. |
| Resurrection | a fallen ally        | Resurrects it at 40% of its max HP ([fainting.md](fainting.md)). Cooldown 7. |

Purify is meant to heal more than the Paladin's Lay on Hands: the Cleric is the
healer, and the Paladin isn't picked for its heal. 35 is a placeholder (40 until v0.0.6).

Resurrection was the Shaman's Ancestral Call until v0.0.3; bringing allies
back suits the healer. It replaced Sanctuary (heal 20 and a barrier against
the next direct hit, cooldown 3).

### Witch

Mage ([archetypes.md](archetypes.md)).

**Passive:** to be designed.

**Moves** (names and powers are placeholders)

| Move      | Target   | Effect |
|-----------|----------|--------|
| Brimstone | all foes | A direct hit on every foe standing, power 25 ([damage.md](damage.md)). Then every ally, the Witch included, heals 17% of the total damage dealt. Cooldown 5 (4 until v0.0.7). |
| Hex       | one foe  | The foe skips its next turn, then the debuff goes away. Shown as turning the foe into a frog. |
| —         |          | To be designed. |

Brimstone details:

- "Damage dealt" is the HP the foes actually lost, as for the Grim Reaper's
  Soul Siphon: damage a shield soaks doesn't count, and neither does damage
  past a foe's last HP.
- Each ally heals the full 17% (it isn't split between them), so the heal is
  worth most early, when there are many foes to hit and many allies to heal.
  Heal Block stops it as it stops any heal.
- 17% is a placeholder (20% until v0.0.6).

Hex details:

- "Next turn" is the foe's next chance to act. If it hasn't acted yet this
  round, it loses this round's action; if it already has, it loses next
  round's.
- Its turn still comes up in the usual order, so the order of everyone else
  doesn't change; it just does nothing.
- It's a debuff, so it can be cleansed before the turn is lost.

### Alchemist

Support ([archetypes.md](archetypes.md)).
Stats (placeholders): attack 75, defense 90 (85 until v0.0.9, 80 until v0.0.8; 60 before that).

Each move brews something and throws it. Its potions go to its allies (or
the Alchemist itself) and its acid goes on the foes. Since v0.0.7 its
strength is scaling: Energizer Potion builds up an ally a little at a time
for the rest of the battle, so the longer the fight goes, the more the
Alchemist has added. Its healing is light: Panacea Mist's 15 per ally, and
Energizer Potion's 30 on the ally it energizes. It can't keep a team up the
way the Cleric can; that's the weakness. (v0.0.9's first design swapped
Energizer for Last Breath, below; it came back in testing. The Shaman's
Attune is a team-wide cousin of Energized.)

**Passive: Panacea Supply.** At the start of the battle, every teammate (the
Alchemist included) gets a Panacea, which blocks the next debuff that would
be put on its holder. It happens once; after that, Panacea Mist hands out
more. (Until v0.0.3 it topped up every round, to one each.)

**Moves**

| Move             | Target   | Potion |
|------------------|----------|--------|
| Panacea Mist     | all allies, itself too | Heals each ally 15 and gives it a Panacea, on top of any it holds. Cooldown 3. |
| ~~Last Breath~~ | one ally, or itself | *In v0.0.9's first design only; the Alchemist went back to Energizer Potion in testing, and no one has Last Breath now.* A potion that holds off death for the round: if damage would knock the ally out before the round ends, it **doesn't fall: it's left at 1 HP** instead. Unused, it wears off at the end of the round. Cooldown 3. (v0.0.9; it replaced Energizer Potion, whose stacking buff went to the Shaman's Attune.) |
| Energizer Potion | one ally, or itself | A stack of Energized: each stack multiplies the ally's attack and defense by 1.15 (1.1 until v0.0.8) for the rest of the battle. Then heals the ally 30 (placeholder; since v0.0.8). Cooldown 3. |
| Acid Flask       | all foes | 6 poison on every foe standing ([effects.md](effects.md#poison)): 21 damage each over six rounds. Cooldown 4 (3 until v0.0.7). |

Its basic attack is Bottle Bash (power 8). The 15, 6 poison and cooldowns
are placeholders, and so is Energizer's 1.15. Acid Flask is the exception to
single-target moves: it's the Alchemist's way to pressure the whole foe team,
slowly.

- **Last Breath is built for the Barbarian.** Rage grows as the Barbarian's
  HP falls, so one left at 1 HP swings at full Rage (about × 2.05 attack),
  if it gets to swing: anything at all finishes it. Any ally works: it's the Alchemist's answer to a big hit
  it sees coming.
- **It stops the fall; it isn't a revival.** Like the Fairy's Fae Bargain,
  the ally never falls, so nothing that waits for a fall happens: no Soul
  Harvest on its corpse, no Death Throes, no Last Rites, nothing for a
  Jester to puppet. Its HP is set to 1, and the potion is used up.
- **Only a knockout by damage:** a hit, poison, bleed, Frostbite. It
  doesn't stop a character knocking itself out (the Warlock's Final
  Offering still takes the Warlock), the Fairy falling in an ally's place
  through Fae Bargain, or fatigue, which takes max HP rather than dealing
  damage (at 0 max HP there's no 1 HP to stay at).
- **It comes before Fae Bargain:** an ally holding Last Breath, about to be
  knocked out, is left at 1 HP by the potion, so the Fairy doesn't step in.
- It's a buff on the ally until the round ends: a dispel or steal takes it,
  a Jester's Chaos eats it, and a Panacea has nothing to block. Once used,
  it's gone.
- The cooldown 3 is a placeholder.
- **Energized stacks multiply.** Two stacks is × 1.32, three × 1.52, on both
  attack and defense. It works with other multipliers like any other.
- **Energized is a buff, immune to removal** ([effects.md](effects.md#removing-and-moving-effects)):
  it can't be cleansed, dispelled or stolen, so a Paladin's Smite or a
  Swashbuckler's Plunder can't touch it. Being a buff, a Panacea has nothing
  to block.
- **It's one effect with a stack count**, like poison's amount: a second
  Energizer Potion adds a stack to the ally's Energized.
- **Potions stack, with no limit.** The Mist adds a Panacea to whatever each
  ally holds, so a team can hold one, two or many and go into a debuff-heavy
  fight (a Monk, a Witch's Hex, a Jester's Silence) blocking several each.
  Two Panaceas block the next two debuffs.
- Panacea never acts straight away: it doesn't remove debuffs the holder
  already has, only blocks the next one put on it.

History: the first kit had Health Potion (all allies, heal 25 when more than
20 below max HP) and Panacea (all allies, block the next debuff), with
Energize at all allies, +25%. Panacea became the passive, Energize became one
ally at +75%, and the defense went from 60 to 80 after the Alchemist won only
28% of balance games. Health Potion still lost to the Shaman's Healing Rain
(54 per ally over 3 rounds) and the Cleric's bigger heals, and the Alchemist
won only 34% of 2,000 games, so it became the single-target Greater Health
Potion. Until v0.0.3 the third move was Energize (the ally's next attack
+75%), and the basic attack was Acid Splash; Energize gave way to Panacea
Mist.

Until v0.0.7 the Alchemist's strength was timing, and the second move was
Greater Health Potion (one ally, cooldown 3): when damage took the ally below
65% of its max HP, it healed 45 at once, mid-round, before anyone else acted.
It gave way to Energizer Potion, because the Alchemist had no scaling to
compete with the other supports.

### Monk

Warrior ([archetypes.md](archetypes.md)). Its moves are single-target hits that
also put a debuff on the foe.

Its identity: it shuts down one foe (attack down, defense down, Heal Block)
and sets it up for the team's damage dealers, and it shakes off the same kind
of disruption itself. It counters tanks and healers. Its weakness is that it
deals little damage of its own and has no sustain, so it loses long games.

**Passive: Inner Peace.** At the end of each of its turns, the Monk purges its
oldest debuff (the one put on it first). A turn lost to Hex still counts.
Passives and other immune effects aren't debuffs it can purge. (Before
v0.0.2 it healed itself 8 instead.)

**Moves** (names and numbers are placeholders)

| Move            | Target  | Effect |
|-----------------|---------|--------|
| Disarming Palm  | one foe | A punch, power 20 ([damage.md](damage.md)), and attack down and defense down for 2 rounds ([effects.md](effects.md)). |
| Crippling Blow  | one foe | A punch, power 20, and Heal Block for 2 of the foe's turns ([effects.md](effects.md#heal-block)). |
| Flurry of Blows | three picks | Pick a foe three times; the same foe can be picked more than once. Each pick takes one punch, power 12. |

- Each pick follows targeting tiers ([targeting.md](targeting.md)), like any
  single-target move.
- Flurry of Blows is a move that hits more than once, so Energize boosts only
  its first punch.
- If an earlier punch knocks out a foe that was picked again, the later punch
  is lost.

### Grim Reaper

Warrior ([archetypes.md](archetypes.md)). Slow ([speed.md](speed.md)).
Stats (placeholders): attack 105, defense 65 (115 and 70 until v0.0.9).

**Passive: Soul Harvest.** Any foe that falls while the Grim Reaper is
standing leaves no corpse: it's removed at once, so the foe can never be
resurrected ([fainting.md](fainting.md#removing-corpses)), and the Grim
Reaper heals 20 (placeholder) for each one. (The name is a placeholder.)

- It counts every fall, however it happens: the Grim Reaper's hits, an
  ally's, poison, fatigue, and a foe that knocks itself out (the Warlock's
  Final Offering). Until v0.0.8 it counted only foes the Grim Reaper's own
  damage knocked out, and didn't heal.
- No corpse, no heal: a minion (a skeleton, a totem, a puppet) leaves no
  corpse, so it gives nothing, and neither does a foe whose max HP was down
  to 0. A fallen Grim Reaper harvests nothing.
- Heal Block stops the heal, as it stops any heal; the corpse still goes.
- It takes corpses from the Grim Reaper's own allies' reach too: a Jester on
  its team has no foe corpses to puppet.
- It replaces Death's Door, which knocked out any foe left below 10% of its
  max HP by anyone's damage. That mechanic is gone from the game.

**Moves** (names and numbers are placeholders)

| Move        | Target  | Effect |
|-------------|---------|--------|
| Reap        | one foe | A scythe hit, power 30 ([damage.md](damage.md)). A third of the damage also comes off the foe's max HP. |
| Soul Siphon | one foe | A hit, power 25. The Grim Reaper heals half the damage dealt. Cooldown 2. |
| —           |         | To be designed. |

Max HP lost to Reap can't be healed back: a foe Reaped for 30 has max HP 90,
so heals stop at 90. Every character still starts at 100 max HP; this is the
only way it goes down.

Soul Siphon heals half the HP the foe actually lost, rounded down: hitting a
foe with 10 HP left for 30 heals 5, not 15. (v0.0.9's first design gave the
Grim Reaper the Shaman's Spirit Link in its place; it went back to Soul
Siphon in testing, and no one has Spirit Link now.)

### Warlock

Mage ([archetypes.md](archetypes.md)). Slow ([speed.md](speed.md)).

Eldritch horror: the Warlock doesn't cast its own magic, it calls on a
tentacled being from beyond and channels its power. Its moves and passive
should read as that being reaching through. Calling it takes time, which is
why it's Slow.

Stats (placeholders): attack 110, defense 45. It's frail on purpose: it hits
like a truck if it's left alive, but it relies on its team (or a foe team
that can't reach it) to get there. Its defense was 75 (the Pyromancer's)
until it won over 80% of balance games, then 50 until v0.0.2.

It's a reskin of the Pyromancer: same stats, and Inferno renamed. Its basic
attack, Firebolt, is now Void Bolt.

**Passive:** to be designed.

**Moves** (names and numbers are placeholders)

| Move            | Target   | Effect |
|-----------------|----------|--------|
| Writhing Depths | all foes | Tentacles burst up under every foe standing: a massive direct hit on each, power 35 ([damage.md](damage.md)). Cooldown 5. |
| The Final Offering | all foes | The Warlock gives itself to the being: a direct hit on every foe standing, power 45, then the Warlock falls. Cooldown 5. |

Writhing Depths hits harder than the Witch's Brimstone (35 against 25)
without its heal; being Slow is the price, since the Warlock acts after
nearly everyone.

The Final Offering details:

- **The hits land first, then the Warlock falls.** If the hits knock out the
  last foe, the foes have lost before the Warlock falls, so its team wins
  (not a draw), even if the Warlock was its team's last character.
- **The fall isn't damage.** The Warlock simply faints, so shields,
  barriers, Invincible and Cryosleep don't stop it.
- **It leaves a corpse on purpose,** like any fall, so it can be
  resurrected: Resurrection, or Last Rites when the Paladin later falls,
  can bring the Warlock back for another go. That combo is intended. Cooldowns survive resurrection
  ([fainting.md](fainting.md#resurrection)), so the 5-turn cooldown keeps a
  revived Warlock from offering itself again straight away.
- Power 45 is a placeholder: it should be above Writhing Depths, since it
  costs a character. It started at 60, which made the Warlock win 80% of
  balance games: about 88 damage to every foe wiped the frail ones outright.

### Shaman

Support ([archetypes.md](archetypes.md)). Slow ([speed.md](speed.md)).
Stats (placeholders): attack 65, defense 55 (50 until v0.0.8; 75 until
v0.0.3, 70 until v0.0.4).

**Reworked in v0.0.9.** The Shaman is in the Warlock's niche: frail and
Slow, but devastating if its team keeps it alive. Every Attune makes the
whole team half again stronger for good, so a Shaman left alone for a few
turns snowballs its team out of reach; a foe that gets to it early stops
that. The totem draws the foes' attacks away from it and keeps healing, and
Healing Rain wipes the slate. It
lost every balance run before (38-41% in v0.0.8, last in each, and 27% in
short games): it's Slow and frail, and its old kit only paid off late.

**Passive:** none yet.

**Moves** (names and numbers are placeholders)

| Move         | Target     | Effect |
|--------------|------------|--------|
| Attune       | all allies, itself too | A stack of Attuned on every ally: attack and defense × 1.5 for the rest of the battle, stacking like the Alchemist's Energized (two stacks × 2.25, three × 3.38), and a heal of 10 at once. Cooldown 3. |
| Totem        | —          | Summons a totem that **taunts** while it stands ([targeting.md](targeting.md)) and **heals every ally 5** at the end of each round. No aura any more. One at a time; the cooldown (3) starts when it's destroyed. |
| Healing Rain | all allies | Cleanses every ally's debuffs, then heals each a flat 30. No regen any more. Cooldown 4. |

- **Attuned** works like Energized ([Alchemist](#alchemist)): one effect with
  a stack count, each stack multiplying attack and defense, immune to
  cleanse, dispel and steal. They're separate effects, so an ally with both
  multiplies by each. It reaches every ally standing, minions (the totem)
  included, but not one that joins later.
- **× 1.5 a stack is meant to be powerful** (the user's call: × 1.07 to
  start, × 1.25 in v0.0.9's first games, where the Shaman won about 26%):
  it's the Shaman's whole payoff, as Writhing Depths is the Warlock's.
- **The totem's taunt** puts it at targeting tier 1 for as long as it
  stands, like the Knight's Shield Bash but with no end. It's part of the
  totem, so it can't be dispelled (Smite doesn't free the foes from it) or
  stolen; killing the totem ends it. Its heal is an
  effect on the totem that fires at the end of every round while it stands,
  on every ally standing, the Shaman and the totem included.
- **Healing Rain cleanses first,** so a Heal Block is gone before the heal,
  as with the Cleric's Purify.
- Until v0.0.9: Healing Rain was the first move (a heal of 25 at once, then
  regen 25 for 5 rounds), the totem's aura gave the team +35% attack and
  defense, and the second move was **Spirit Link**. It went to the Grim
  Reaper in v0.0.9's first design, then out of the game.

### Knight

Tank ([archetypes.md](archetypes.md)). Normal ([speed.md](speed.md)).

**Passive:** to be designed.

**Moves** (names and numbers are placeholders)

| Move        | Target  | Effect |
|-------------|---------|--------|
| Shield Bash | one foe | A direct hit, power 15 ([damage.md](damage.md)), and the Knight taunts for 2 rounds ([targeting.md](targeting.md)). |
| Bulwark     | one ally, or itself | A 30 shield ([effects.md](effects.md#shield)). |

Where the Paladin spends a whole turn on Guard, the Knight deals a little
damage and taunts in the same move. Being Normal rather than Slow, its taunt
also goes up earlier in the round.

### Assassin

Rogue ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md)).
Stats (placeholders): attack 80, defense 52 (attack 83 until v0.0.9, 88
until v0.0.6; 77 in v0.0.9's first games).

**Passive: Predator.** The Assassin deals **50% more damage** (placeholder; 25% before v0.0.2)
to a target **below 75%** of its max HP (50% until v0.0.9). It finishes off
wounded foes, and anything that has taken one solid hit counts as wounded.

- **Since v0.0.9 it's built to follow an area hitter.** A Thunderstorm
  (power 18, attack 105) takes 25 or more off any foe with defense 75 or
  less, so after one, the Assassin's hits get the bonus on most of the foe
  team. At 50%, Thunderstorm never set it up: that took 51 damage, which
  needs defense 37 or less, and no character is that frail. The Assassin's
  attack came down from 83 to 77 to pay for it, so it hits a fresh foe a
  little softer.
- Below means strictly below: a target at exactly 75% of its max HP takes
  normal damage.
- It's measured against the target's current max HP, so max HP lost to Reap
  or fatigue doesn't count as missing: a character at 50/50 is at full HP.
- It's checked for each target when each hit is calculated, so a hit that
  takes a foe below half doesn't boost that same hit, but it boosts the next.

Before this, Predator grew smoothly with the target's missing HP,
× (1 + (1 − HP/max)), up to double damage at 1 HP.

(The name is a placeholder.)

**Moves** (powers and cooldowns are placeholders)

| Move      | Target              | Effect |
|-----------|---------------------|--------|
| Backstab  | one foe             | A heavy direct hit, power 36 (40 until v0.0.9; [damage.md](damage.md)). Cooldown 3. |
| Execution | the weakest foe     | A direct hit, power 20, on the foe with the lowest HP. If it knocks that foe out, it hits the foe with the next lowest HP, and so on until a hit doesn't knock its target out or no foes are left. Cooldown 4. |

- Execution picks its own targets, so the player doesn't choose. "Lowest HP"
  is current HP, and a tie goes to the lowest slot.
- Execution ignores taunts and other targeting tiers, as area moves do
  ([targeting.md](targeting.md)): a Tank can't shield a wounded ally from it.
- Each Execution hit is a first hit on a new foe, so Predator applies to each
  foe below 75% of its max HP.

### Bard

Support ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md)).
Stats (placeholders): attack 70, defense 65 (75 until v0.0.9).

The Bard is the **early spike**: its team is at its strongest in the first
rounds and the Bard's power fades after that. It sets up a fast start
(everyone acting sooner and hitting harder) so its team can take the lead
before the foes get going, and it has little to offer a long fight. It
controls tempo through speed tiers ([speed.md](speed.md)), and is Fast, so it
sings before most characters act.

**Reworked in v0.0.9.** Until then its power was Crescendo, which grew with
every foe that fell: it scaled into the mid and late game, the opposite of
an early support, and the Bard topped every balance run (56-57% in v0.0.8,
64% of short games).

**Passive: Overture.** A team aura ([effects.md](effects.md#auras)) that
opens the battle strong and fades: the whole team, the Bard included, has
× 1.4 attack in round 1, × 1.3 in round 2, × 1.2 in round 3 and × 1.1 in
round 4, and nothing from round 5 on. Kills don't matter. (The 1.4, the 0.1
a round and attack only are placeholders; in v0.0.9's testing it also
opened at × 1.3 and × 1.5.)

- **It's an aura, so it lives with the Bard.** While the Bard is down the
  bonus stops. It fades at each round end the Bard sees standing, so a Bard
  that was down for a round end and is resurrected carries on from where it
  stopped. It can't be dispelled or stolen.
- It's its own multiplier, separate from the × 1.5 stat ups, the Attuned and
  Energized stacks, and multiplies with them.

**Moves** (names, durations and cooldowns are placeholders)

| Move    | Target     | Effect |
|---------|------------|--------|
| Allegro | all allies | Speed up for each ally's next turn ([effects.md](effects.md#speed-up-and-speed-down)): every ally moves one tier faster, then it goes away. Cooldown 4. |
| Heckle  | one foe    | The Bard mocks the foe until everyone turns on it: a **taunt** and **defense down** ([effects.md](effects.md)) on the foe for 2 rounds. Cooldown 4. |
| Curtains | all foes  | Every foe **below 50% of its max HP** takes **double damage from hits** until the end of the round. Cooldown 5. |

The cooldowns are long on purpose: the Bard gets each move about once in
the early window, so it can't keep cycling them into a long fight. Only a
Construct's Overclock (which cuts its team's cooldowns) gets them back
sooner.

- **Curtains closes a fight; it doesn't start one.** It needs foes already
  below half, so it's the round 2 or 3 move, after the opening has done its
  damage, unless a team can front-load enough damage to set it up in round
  1. The Bard is Fast, so it usually lands before most of its allies act.
- **Double damage is defense halved**: hits are power × attack ÷ defense
  ([damage.md](damage.md)), so every hit on the foe deals twice as much (it
  multiplies with defense down). Damage that isn't a hit (poison, bleed,
  fatigue) isn't doubled.
- It's a debuff that ends at the end of the round: a Panacea blocks it and
  a cleanse takes it off. A foe healed back over half keeps it; one that
  drops below half after the Bard has acted doesn't get it.
- The 50% and the cooldowns are placeholders.

- **One tier only.** Allegro takes Slow to Normal or Normal to Fast, never
  Slow to Fast.
- **Next turn.** If an ally hasn't acted yet this round, Allegro speeds up
  this round's action; if it already has, it speeds up next round's. Either
  way the speed up ends when that turn does. A turn lost to Hex still counts.
- **A tier change takes effect at once.** Who can act is checked fresh at
  every step ([speed.md](speed.md#phases)), so a Slow ally sped up during the
  Fast phase acts in this round's Normal phase.
- The Bard is already Fast, so Allegro's speed up does nothing for it (it
  still gets it, and loses it after its next turn).
- **Heckle turns a taunt around.** A taunt makes its owner the one its foes
  must pick, so on a foe it makes the *Bard's* team pick that foe: everyone
  focuses it, and the defense down makes it fall fast. After Allegro, a
  team that acts first all hits the heckled foe. The cost is the
  commitment: while it lasts, the Bard's team's single-target moves must
  pick that foe (the Ranger's Keen Eye ignores it).
- **A taunting tank on the foe's side** is at the same targeting tier, so
  the Bard's team may pick either: Heckle doesn't cut through a taunt the
  way Smite does, it makes the heckled foe a target alongside it.
- Both are debuffs, separate effects that each count down 2 rounds: a
  Panacea blocks each, and a cleanse (the Shaman's Healing Rain, the
  Cleric's Purify) takes them off. The 2 rounds and cooldown are
  placeholders.

History:

- **Crescendo** (passive, v0.0.3 to v0.0.8): a team aura gaining a stack for
  every foe character that fell while the Bard stood, each × 0.17 attack
  (× 0.2 until v0.0.8) and × 0.2 defense. Until v0.0.3 it was a move: × 1.2
  attack and defense on every ally for 2 rounds, cooldown 4.
- **Allegro** was one ally until v0.0.9, with attack up (× 1.5) as well as
  the speed up for its next turn. Until v0.0.2 it took 1 off each of the
  ally's running cooldowns instead; cooldown cutting is now the Construct's
  Overclock.
- **Largo** (one foe, v0.0.1 to v0.0.8): speed down for the foe's next turn,
  and attack down for 2 rounds. Cooldown 3.

### Fairy

Support ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md)).
Stats (placeholders): attack 65, defense 62 (70 until v0.0.8).

The Fairy protects. Being Fast, it acts before nearly everyone, so its
protection is in place before the foes swing.

Its niche is **getting the team through the opening** so it can scale into
a longer fight: Fairy Ring answers a Rogue rush on one ally, Pixie Dust
answers a team of Mages. It runs out of steam after its first couple of
turns on purpose; the team it kept alive wins from there. (Whether that's a
niche worth a slot may need changes elsewhere, such as characters that
scale into long fights.)

**Passive: Fae Bargain.** The first time a hit would knock out one of the
Fairy's allies, the Fairy falls instead: the ally stays standing with the
Fairy's remaining HP (no more than its own max HP), and the Fairy faints.
(The name is a placeholder.)

- **Instead, not after.** The ally never falls, so nothing that keys off a
  fall happens to it: no Soul Harvest on its corpse, no Crescendo stack for
  the foe's Bard, no Last Rites. The Fairy's fall is a fall like any other
  (it counts for a foe's Crescendo, and a foe Grim Reaper harvests its
  corpse).
- **The Fairy's HP is the price and the prize.** A healthy Fairy saves an
  ally with a lot of HP; a Fairy chipped down saves one with little. Foes
  that want to get past it hit the Fairy first.
- **Once, while the Fairy stands.** Not for the Fairy itself, and not for
  minions. If one hit would knock out several allies (an area move), the
  first one in slot order is saved. It leaves a corpse, so Last Rites or
  Resurrection can bring the Fairy back, and with it the bargain.
- Any damage counts: a hit, poison, bleed. It isn't healing, so Heal Block
  doesn't stop it, and a Construct is saved like anyone.
- Until v0.0.3 the passive was Faerie Light: every ally healed 5 at the end
  of every round, all game, which rewarded long games more than it got the
  team through the opening.

**Moves** (names, durations and cooldowns are placeholders)

| Move       | Target              | Effect |
|------------|---------------------|--------|
| Fairy Ring | one ally, or itself | Cleanses the ally's debuffs, heals it to full, then Invincible until the end of the round ([effects.md](effects.md#invincible)): the ally takes no damage for the rest of the round. Cooldown 7. |
| Pixie Dust | all allies, itself too | A 30 shield on each ([effects.md](effects.md#shield)). Cooldown 4. |
| —          |                     | To be designed. |

- Cast in the Fast phase, Fairy Ring covers the ally against everything the
  foes do that round. Cast later, it covers only what's left of the round.
- It ends at the end of the round, after the end-of-round damage (poison,
  fatigue), so it covers that too.
- **The cleanse and full heal make Ring a turn-2 move as well as a turn-1
  one:** after the first exchange, it resets the ally who took the burst. The
  cleanse goes first, so a Heal Block can't stop the heal (the Construct,
  which can never be healed, gets only the cleanse and Invincible). For that
  it has a long cooldown, 7: once a game, or twice in a long one. Until
  v0.0.3 Ring was Invincible alone, cooldown 4.
- Pixie Dust puts a shield on the whole team as big as the Knight's Bulwark
  puts on one ally. It's the answer to Mages: a 30 shield soaks most of an
  area hit on every ally (Writhing Depths deals about 35-55 a target).
  Shields add up and decay 20 a round, so it's down to 10 at the next round
  end. It reaches minions, like any team-wide move. (15 until v0.0.2, 22
  until v0.0.3.)

### Necromancer

Mage ([archetypes.md](archetypes.md)). Slow ([speed.md](speed.md); the tier
is a placeholder).
Stats (placeholders): attack 105, defense 60 (attack 110 until v0.0.9).

**Passive: Death Throes** (since v0.0.8). When the Necromancer
falls, it lashes out one last time: a direct hit of power 25 on every foe
standing ([damage.md](damage.md)). (The name and the 25 are placeholders.)

- **Any fall counts:** a hit, poison, fatigue. A fall the Fairy's Fae Bargain
  takes instead isn't one, so nothing happens.
- **It's a hit from the Necromancer,** using its attack as it stood when it
  fell, so it counts as its kills: a foe Death Throes knocks out counts for
  Crescendo and Soul Harvest like any other fall, and a parry or a Cold Aura
  answers it as they answer any direct hit (on a fallen attacker, a riposte
  has nothing to hit).
- **It doesn't take corpses;** only Wither does.
- **Every fall:** a resurrected Necromancer that falls again lashes out
  again.
- **It can draw a game.** If it knocks out the last foe as the Necromancer's
  team falls, both teams lose at the same moment, and the game is a draw
  ([fainting.md](fainting.md)).
- It gives the Necromancer something on the way out, so focusing it down
  costs the foes; it's the payoff for a Slow, frail Mage that often falls
  before Raise Dead has a corpse to use.

**Moves** (names, powers and cooldowns are placeholders)

| Move   | Target   | Effect |
|--------|----------|--------|
| Wither | all foes | A direct hit on every foe standing, power 20 ([damage.md](damage.md)). A foe it knocks out leaves no corpse ([fainting.md](fainting.md#removing-corpses)). Cooldown 4. |
| Raise Dead | an ally's corpse | Removes the corpse and summons a skeleton in its place ([summons.md](summons.md#the-necromancers-skeletons)): a minion with decent stats that only has a basic attack. Cooldown 5. |
| —      |          | To be designed. |

- Wither removes the corpse of a foe its own hit knocks out (the Grim
  Reaper's Soul Harvest takes the corpse of any foe that falls). A foe that falls to anything
  else, the Necromancer's other moves and basic attack included, leaves a
  corpse as usual.
- Its power is below Brimstone's 25 and Writhing Depths' 35: the corpse removal is
  the extra.
- **Raise Dead works against resurrection on purpose.** The corpse it uses is
  gone, so that ally can't be brought back by Resurrection or Last Rites.
  A Necromancer team trades a possible revival for a skeleton now.
- Raise Dead takes only the corpses of the Necromancer's own team's
  characters. Minions leave no corpse, so a skeleton can't be raised again.

### Vampire

Warrior ([archetypes.md](archetypes.md)). Normal ([speed.md](speed.md)).

The Vampire is frail and lives on what it drains. Its defense is low, a
little above the Rogues' (placeholder: attack 102, defense 62; attack 115
until v0.0.9, defense 50 until v0.0.2), so it takes a lot from
each hit, and gets its staying power from healing it back with every hit it
deals. It wins one-on-ones the way Warriors should, but through sustain
rather than raw toughness.

**Passive: Sanguine.** The Vampire heals **75%** of all the damage it deals
(30% until v0.0.9). (The name and the 75% are placeholders.)

- **v0.0.9: a lifesteal wall.** At 75% it heals most of every hit back, so
  while it keeps dealing damage it has a lot of effective HP; when it can't
  (frozen, Hexed, its hits soaked by shields, barriers or Ironclad's cap,
  or Heal Blocked) it's a frail Warrior. Its moves are being reworked to
  match (open question).

- "Damage dealt" is the HP the foe actually lost, as for Soul Siphon: damage
  a shield soaks doesn't count, and neither does damage past a foe's last HP.
  It's rounded down.
- It counts every hit the Vampire deals, its basic attack included, and any
  indirect damage it deals.
- **Its weaknesses are the counters to healing.** Heal Block (the Monk's
  Crippling Blow) shuts it off completely, and max HP loss (Reap, fatigue)
  lowers the ceiling it can heal back up to.
- **Shields suit it.** A shield soaks hits before they reach HP, and
  Sanguine restores the HP that gets through, so the Knight's Bulwark
  stretches its life a long way.

**Moves** (names, powers and cooldowns are placeholders)

| Move | Target  | Effect |
|------|---------|--------|
| Bite | one foe | A direct hit, power 30 (25 until v0.0.9; [damage.md](damage.md)). Cooldown 2. |
| Hemorrhage | one foe | **Bleed 15** for 3 rounds ([effects.md](effects.md#bleed)), and no hit. Cooldown 3. (Until v0.0.9: a hit of power 10 and bleed 10.) |
| Crimson Veil | all foes | A light direct hit on every foe standing, power 10. Cooldown 3. (v0.0.9.) |

Bite is the Vampire's plain hit, the steady source of damage that Sanguine
heals from. Its power sits between the Monk's strikes (20) and Cleave (30):
the lifesteal is the extra.

- **Crimson Veil is little damage but a lot of healing.** Each foe it
  reaches is a separate hit, and Sanguine heals 75% of each: about 17 a foe
  against defense 62 at attack 107, so about 50 healed off a full foe team.
  It's how the Vampire keeps feeding when one foe is shielded or taunting:
  something always gets hit. The name was once a rejected shield-on-itself
  move (below); the power 10 and cooldown 3 are placeholders.
- **Hemorrhage is all bleed (v0.0.9).** With no hit, it's 45 over three
  round ends instead of a hit up front, and each tick heals the Vampire
  about 11. Since it isn't a hit, a barrier doesn't stop it and it sets off
  nothing that waits for a hit (Cold Aura, parries, Retaliate); the bleed is
  a debuff, so a Panacea blocks it and a cleanse ends it. Each tick is its
  own instance of damage, so Ironclad caps each one separately.
- **Hemorrhage keeps the Vampire feeding between hits.** The bleed is damage
  the Vampire deals, so Sanguine heals from each tick at the end of the
  round, on top of the hit itself. If the Vampire is down when a tick lands,
  the foe still bleeds but nobody heals.

Rejected so far:

- **Crimson Veil** (a shield on itself): a turn spent on a shield is a turn
  without damage, and so without healing. The Vampire's shields should come
  from allies.

### Cryomancer

Mage ([archetypes.md](archetypes.md)). Normal ([speed.md](speed.md)).
Stats (placeholders): attack 95, defense 55 (attack 110 until v0.0.9).

The Cryomancer brings chill and freeze ([effects.md](effects.md#chill-and-freeze)).

**Passive: Frostbite (v0.0.9).** At the end of every round, each foe that is
**chilled or frozen** takes 8 damage (placeholder). It's indirect damage
([events.md](events.md#direct-and-indirect-damage)) from the Cryomancer, a
flat amount like poison, not a hit: defense doesn't change it, and it sets
off nothing that waits for a hit (Cold Aura, Retaliate, parries).

- It rewards keeping foes cold: every chill or freeze still on a foe at the
  round's end costs it 8, and a foe frozen by a second chill counts once,
  frozen.
- It stops while the Cryomancer is down, like any effect of a fallen
  character.
- Until v0.0.9 the Cryomancer had no passive, and Frostbite was the name of
  its second move (now Icicle Barrage).

**Moves** (names, powers and cooldowns are placeholders)

| Move     | Target   | Effect |
|----------|----------|--------|
| Blizzard | all foes | A direct hit on every foe standing, power 20 ([damage.md](damage.md)), and chill on each. A foe already chilled is frozen. Cooldown 4. |
| Icicle Barrage | three picks | Pick a foe three times, as with Flurry of Blows; the same foe can be picked more than once. Each pick is a direct hit, power 10, and a **chill**. Cooldown 3. (Until v0.0.9 this was Frostbite: one foe, a hit of power 20 and a chill, cooldown 2.) |
| Cryosleep | another ally | Puts the ally in Cryosleep ([effects.md](effects.md#cryosleep)): it skips its next turn and takes no damage until then. Heals it 15. Cooldown 4. (The Frost Giant's until v0.0.9.) |

- The chill goes on every foe Blizzard reaches, whether or not a barrier
  blocks the damage, as with any debuff a move adds.
- Blizzard's power is below Brimstone's 25: the chill is the extra.
- **Icicle Barrage stacks or spreads (v0.0.9).** Its three chills can go on one
  foe or several:
  - **Stacked,** they freeze one foe on their own: the first pick chills, the
    second freezes. A third pick on the same foe just hits, since a frozen
    character can't be chilled.
  - **Spread after Blizzard,** they freeze up to three foes: Blizzard chills
    the whole foe team, and on the Cryomancer's next turn, before the chill
    wears off, each pick on a different foe freezes it.
  - Or split: two on one foe to freeze it, one to chill another for later.
- Each pick follows targeting tiers ([targeting.md](targeting.md)), like any
  single-target move, so a taunting Tank takes all three (and is frozen).
- It's a move that hits more than once, so first-hit-only boosts (Energize)
  boost only its first hit, and if an earlier pick knocks out a foe that was
  picked again, the later pick is lost.
- Each chill lands whether or not a barrier blocks the hit, as with
  Blizzard; a Panacea blocks one chill (the first on its holder).
- **On a Frost Giant,** each hit chills the Cryomancer through Cold Aura, so
  two picks on it freeze the Cryomancer too.
- The power 10, three picks and cooldown 3 are placeholders. The cooldown
  went up from 2 because it can now freeze one foe by itself every time it's
  ready.
- **Cryosleep trades the ally's turn for safety.** The ally skips its next
  turn, and until that turn has passed it takes no damage; the heal comes at
  once. "Next turn" is as for any freeze: this round's if the ally hasn't
  acted yet, otherwise next round's. The Cryomancer is Normal, so a Slow ally
  put to sleep before it acts loses this round's turn and wakes for the next
  round; a Fast ally that has acted stays safe until its turn next round. It
  can't target the Cryomancer itself (as it couldn't target the Frost Giant).
  It's a buff: a Panacea doesn't block it and a cleanse doesn't end it; a
  dispel wakes the ally early.

### Frost Giant

Tank ([archetypes.md](archetypes.md)). Slow ([speed.md](speed.md)).
Stats (placeholders): attack 93, defense 150 (attack 85 until v0.0.6).

**Passive: Cold Aura.** A foe that hits the Frost Giant directly is chilled
([effects.md](effects.md#chill-and-freeze)). Hitting it twice before the chill
wears off freezes the attacker. (The name is a placeholder.)

- Only direct hits count ([events.md](events.md#direct-and-indirect-damage)):
  poison, bleed and Retaliate don't chill.
- A hit counts if it deals damage, shield included. A hit stopped by a
  barrier or Invincible isn't damage taken, so it chills no one, as it sets
  off no Retaliate.
- Each hit counts separately. An area move chills its user once (it hits the
  Giant once); a multi-hit move like Flurry of Blows chills with its first
  punch and freezes with its second, so the rest of the move still lands but
  the Monk loses its next turn.
- It's a chill like any other, so it combines with the Cryomancer's: a foe
  chilled by Blizzard that then hits the Frost Giant is frozen.
- Being Slow, the Frost Giant is usually hit before it acts, so the aura is
  working from round 1.

**Moves** (names, powers, heals and cooldowns are placeholders)

| Move         | Target   | Effect |
|--------------|----------|--------|
| Avalanche    | all foes | A light direct hit on every foe standing, power 19 ([damage.md](damage.md)), and chill on each ([effects.md](effects.md#chill-and-freeze)). A foe already chilled is frozen. Cooldown 4. |
| Glacial Roar | the user | Taunt for 2 rounds ([targeting.md](targeting.md#taunt)). Cooldown 3. |
| —            |          | None in v0.0.9: Cryosleep moved to the [Cryomancer](#cryomancer), and the Frost Giant plays this patch with two moves to see where it stands. |

- **Avalanche is light on purpose.** The Frost Giant is a Tank, so its area
  hit is just under Blizzard's 20 (19; 13 until v0.0.6, 10 until v0.0.2); the chill is what matters. Avalanche on one
  turn and Blizzard on the Cryomancer's next freezes the whole foe team.
- **Glacial Roar feeds the aura.** Foes that have to pick the Giant get
  chilled for it. Like the Knight's taunt it doesn't raise defense, unlike the
  Paladin's Guard. Its 2 rounds count down at the end of each round, like
  other taunts.
- Until v0.0.9 its third move was **Cryosleep** (one other ally: skips its
  next turn, takes no damage until then, heals 15; cooldown 4), now the
  Cryomancer's.

### Construct

Tank ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md)). (The
name is a placeholder.)

Its niche is the **Fast tank**: the only Tank that acts before the foes do,
so its taunt and protection are up before their first hits land. (Normal
until v0.0.2.) It's a **transient tank built to counter single-target
burst**: Ironclad blunts the big hits its taunt draws, but it can't be
healed, so it doesn't last. That's niche on purpose.
Stats (placeholders): attack 71, defense 75 (104 until v0.0.9; 75 and 110 until v0.0.8; 85 and 120 until v0.0.3).

**v0.0.9: can't be burst, can be ground down.** Its defense dropped from 104
to 75 and Ironclad's cap from 25% to 22%, so almost every real hit lands for
exactly the cap (22): what decides how fast it falls is how many hits it
takes, not how hard they are. Boosts (Predator, Marked, Heckle, Curtains,
Overture, Attuned) mostly go to waste on it, while chip (multi-hit moves,
basic attacks, poison, bleed, Frostbite) hurts it more. Against the game's
29 hits it takes about 7% more than before unboosted, and about 6% less when
every hit is boosted. In v0.0.8 it was 54-55% in both runs with no real
counter; revisit it with v0.0.9's data.

**Passive: Ironclad.** Two parts. (The name and the 22% are placeholders; 25% until v0.0.9.)

- **It can't be healed.** No healing of any kind restores its HP: heals from
  moves, regen, Vigil, Health Potions, drains, Cryosleep's heal.
  It's like a Heal Block that never ends and can't be cleansed.
- **No hit takes more than 22% of its max HP.** Any single instance of damage
  is capped at 22% of its current max HP, rounded down: 22 at 100 max
  HP. Bringing it down from full always takes at least five hits.

Details:

- **Every instance is capped,** direct or indirect: each hit of a multi-hit
  move, each target's hit from an area move, each poison or bleed tick, each
  Retaliate. So the cap blunts big hits like Backstab and Writhing Depths and does
  nothing against chip damage.
- **The cap comes first, then shields.** A hit is capped, and the capped
  amount goes to the shield and then HP. A shield adds to its staying power
  but doesn't raise the cap.
- **Shields still work**, since they aren't healing. They're the only way
  allies can keep it going.
- **Max HP loss isn't damage.** Fatigue's max HP loss isn't capped. Reap's
  max HP loss is a third of the damage dealt, so it's a third of the capped
  hit. Max HP lost also lowers the cap: at 80 max HP it's 17.
- **It can be revived.** It leaves a corpse like any character, and
  resurrection isn't healing: Resurrection and Last Rites bring it back at
  their usual percentage. A Necromancer can raise its corpse too.

**Moves** (names, powers and cooldowns are placeholders)

| Move        | Target   | Effect |
|-------------|----------|--------|
| Piston Slam | one foe  | A direct hit, power 20 ([damage.md](damage.md)), and attack down for 2 rounds ([effects.md](effects.md)). Cooldown 3. |
| Lockdown    | the user | Taunt for 1 round ([targeting.md](targeting.md#taunt)). Cooldown 4. |
| Overclock   | the whole team | Takes 2 off every running cooldown of every ally standing, the Construct included ([moves.md](moves.md#cooldowns)). Cooldown 6. |

- Piston Slam is modest, as a Tank's hit should be: below the Warriors' 30.
  Its attack down is the protection: the Construct is Fast, so it usually
  lands before the foe acts and blunts that foe's hit the same round.
- Overclock's cut happens at once: a cooldown at 2 or less is ready
  straight away, and the basic attack has none. Its own cooldown starts after
  the cut, so it doesn't shorten itself; its other moves (Lockdown, Piston
  Slam) do get the cut. The Shaman's Totem is cut only once its cooldown has
  started, after the totem is destroyed ([summons.md](summons.md)). The 2 and
  the cooldown are placeholders. Since Overclock cuts the Construct's own
  moves too, its cooldowns are each 1 longer than they'd otherwise be (from
  v0.0.3: Piston Slam 3, Lockdown 4, Overclock 6).
- Lockdown pulls the foes' single-target hits onto the Construct, where the
  cap blunts the biggest of them. It can't heal, so a taunting Construct
  slowly runs down unless allies shield it. Like the Knight's and the Frost
  Giant's taunts, it doesn't raise defense.
- Lockdown lasts 1 round (2 until v0.0.3): an immediate answer, not a
  standing wall. The Construct is Fast, so it's up before most foes act that
  round and gone at the round's end.

### Stormbringer

Mage ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md)).
Stats (placeholders): attack 105, defense 50.

A lightning caster, and the only Fast Mage: its bolts strike before nearly
anyone can act.

**Passive:** none for now.

**Moves** (powers and cooldowns are placeholders)

| Move            | Target                | Effect |
|-----------------|-----------------------|--------|
| Thunderstorm    | all foes              | A direct hit on every foe standing, power 18 ([damage.md](damage.md)); 15 until v0.0.7. Cooldown 4. |
| Chain Lightning | up to four foes, in order | The player picks foes one after another, and the bolt hits them in that order: power 20, then 15, then 10, then 5. Cooldown 3. |

- **Thunderstorm is light because it's Fast.** The Stormbringer's area hit
  lands before nearly anyone acts, so it's weaker than Brimstone (25) and
  Writhing Depths (35).
- **Chain Lightning's picks follow targeting tiers, one pick at a time**
  ([targeting.md](targeting.md)). Each pick must be a foe not already picked
  for this cast, and among the foes standing that haven't been picked, it
  must be one in the highest targeting tier. So a taunting foe has to be
  picked first and takes the 20, a shrouded one can only be picked last, and
  the order among foes in the same tier is the player's choice.
- Each foe is hit once. With fewer than four foes standing the chain stops
  early: against two foes it deals 20 and 15 and the rest is lost.
- Minions are foes like any other, so a totem or skeleton can be picked, and
  takes one of the bolts.
- All picks are made before the bolt strikes, as for Flurry of Blows. A
  barrier blocks only the hit on the foe that has it, and the chain goes on.
- Each foe's hit is that foe's first hit from the move, so Energize boosts
  every bolt, as with an area move.

### Ninja

Rogue ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md); the tier
is a placeholder, as Rogues tend to be Fast).
Stats (placeholders): attack 110, defense 57 (50 until v0.0.8).

The Ninja hides between strikes. Its shroud ([targeting.md](targeting.md#shroud))
puts it in targeting tier -1, so foes can only pick it with a single-target
move once no one else on its team is left standing. But a shroud lasts only
until the start of the Ninja's next turn: then it drops, and the Ninja is
targeted as usual until it uses Shadow Strike to hide again. Since v0.0.8;
until then it was shrouded all the time.

**Passive: Shadowed.** The Ninja starts the battle shrouded: at the start of
the first round it gets a shroud, which drops when its first turn starts like
any other. (The name is a placeholder.)

**The shroud** ([effects.md](effects.md)):

- **It ends when the Ninja's turn starts,** before it acts. A turn it loses
  (frozen, hexed) still starts, so that ends it too. Used on a turn, Shadow
  Strike hides the Ninja from then until its next turn: the rest of that
  round and, the Ninja being Fast, little of the next.
- **Only single-target picks.** Area moves hit it as usual, and so do moves
  that choose their own targets, like the Assassin's Execution. The Ranger's
  Keen Eye ignores targeting tiers, so the Ranger can pick it any time.
- **Minions count as allies standing.** A totem or skeleton is in tier 0, so
  it keeps the Ninja hidden even after the Ninja's teammates have fallen.
- **Two shrouded characters on a team** are both in tier -1, so once
  everyone else is down, foes can pick either.
- In Chain Lightning, the Ninja can only be picked after every foe in a
  higher tier, so it takes the smallest bolt left.
- **It's a buff,** so a dispel (the Paladin's Smite) removes it and the
  Swashbuckler's Plunder steals it, shroud and all, until the start of the
  Swashbuckler's own next turn. Both are single-target moves, so they can
  only reach a shrouded Ninja once it stands alone. A cleanse removes only
  debuffs, so it leaves the shroud.

**Moves** (names, powers and cooldowns are placeholders)

| Move          | Target  | Effect |
|---------------|---------|--------|
| Shuriken      | one foe | A direct hit, power 25 ([damage.md](damage.md)). Cooldown 2. |
| Shadow Strike | one foe | A direct hit, power 20, then the Ninja is shrouded until its next turn starts. Cooldown 2. Since v0.0.8. |

Shuriken is a plain hit, a little under the Ranger's Aimed Shot (30). Shadow
Strike hits less (20) for the shroud. With both on cooldown 2 the Ninja can
alternate them: hide one turn, hit harder the next.

### Swashbuckler

Warrior ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md)).
Stats (placeholders): attack 87, defense 68 (attack 110 until v0.0.4, 95 until v0.0.6).

The only **Fast Warrior**: it wins the war of attrition rather than picking
foes off. Parry and riposte punish the foe for hitting it, Plunder strips
its shields and buffs, and being Fast it has En Garde up before the foes
act. (A Rogue with defense 55 until v0.0.3.)

A pirate duelist that takes what it wants: the first character that steals
([effects.md](effects.md#removing-and-moving-effects)).

**Passive:** none since v0.0.5. In v0.0.3 and v0.0.4 it had On Guard, a
parry ready at the start of the battle; it went while the Swashbuckler still
won most of its games.

**Moves** (names, powers and cooldowns are placeholders)

| Move    | Target  | Effect |
|---------|---------|--------|
| Pistol Shot | any foe | A direct hit, power 30 ([damage.md](damage.md)), on any foe standing: taunts and other targeting tiers don't apply. Cooldown 3 (2 until v0.0.6). |
| Plunder | one foe | Steals every buff the foe has and puts them on the Swashbuckler, then a direct hit, power 20 ([damage.md](damage.md)). Cooldown 4 (3 until v0.0.6). |
| En Garde | the user | Parry: the next direct hit a foe lands on the Swashbuckler is blocked, and it ripostes, a direct hit of power 12 back on the attacker. It lasts until it's used, however many rounds that takes. Cooldown 5 (4 until v0.0.6). |

- **Pistol Shot reaches the backline.** It ignores targeting tiers like an
  area move does, but hits one foe of the Swashbuckler's choosing: a Fairy,
  Bard or Mage behind a taunting tank. It deals more than Plunder and does
  less.
- **A parry waits for its hit.** It blocks the next direct hit from a foe
  (a drain counts), not indirect damage like poison or bleed, which goes
  through and leaves the parry up. The riposte is a direct hit, so a foe's
  own parry can answer it. A second En Garde while one is up does nothing.
- Until v0.0.3 the Swashbuckler had only Plunder and its basic attack.
- v0.0.4 toned it down after it won 84% of v0.0.3's games: attack 110 to
  95, riposte power 20 to 12, En Garde's cooldown 3 to 4.
- v0.0.6 toned it down again after it won 63% of v0.0.5's games: attack 95
  to 87, and every cooldown up by one (Pistol Shot 3, Plunder 4, En Garde 5).
- **Every buff, for now.** Stealing just one (the player's pick, or the
  newest) was considered; it can be revisited after balance testing.
- **Steal first, then hit,** as Smite dispels first: a shield, barrier or
  Invincible is taken before the hit lands, so it doesn't block it, and the
  Swashbuckler has it instead.
- **A stolen buff keeps what's left of it:** its remaining rounds, its shield
  amount, its charges. On the Swashbuckler it stacks by its type's usual rule
  ([effects.md](effects.md#stacking)): a stolen shield adds to the
  Swashbuckler's shield, a stolen barrier adds a charge.
- **Some buffs can't be stolen.** Passives never can. Neither can the totem's
  blessing (an aura on the totem, [effects.md](effects.md#auras)) or Cryosleep (an ally's ice
  sleep makes no sense on a foe). Those stay on the foe. Which other buffs
  opt out is an open question ([questions.md](questions.md)).
- Being Fast, the Swashbuckler usually steals before the foe's team has
  acted, so it takes buffs left over from the previous round, like a
  Panacea or a Bulwark shield, before the foe gets to use them.
- Stealing a taunt makes the Swashbuckler taunt for the rest of it, which
  pulls the foes' single-target hits onto a frail Rogue. The player decides
  whether that's worth it.

### Jester

Rogue ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md)).

Weak on its own on purpose: Puppeteer is the strongest thing it does, so its
stats are low (placeholder: attack 94, defense 52; attack 90 until v0.0.6, defense 50 until v0.0.8) and its hit is light.

**Passive:** to be designed.

**Moves** (names, numbers and cooldowns are placeholders)

| Move      | Target          | Effect |
|-----------|-----------------|--------|
| Trick Blade | one foe       | A direct hit, power 20 ([damage.md](damage.md)), and Silence for the foe's next turn ([effects.md](effects.md#silence)): only its basic attack. Cooldown 2. |
| Puppeteer | a foe's corpse  | Raises the fallen foe as a minion on the Jester's team ([summons.md](summons.md#the-jesters-puppets)), at 33% of its max HP. One puppet at a time; cooldown 5, from when the puppet falls. |
| Pandemonium | all foes     | A light direct hit on every foe standing, power 10 (well under Thunderstorm's 18), and a stack of **Chaos** on each ([effects.md](effects.md#chaos)). Cooldown 3. (v0.0.9.) |

- **Trick Blade disables before the first corpse.** The Jester is Fast, so it
  usually lands before the foe acts that round: a Warlock or Cryomancer
  silenced in round 1 loses its nuke for that turn. It's the Jester's early
  job, until Puppeteer has a corpse to work with. The Silence is a debuff,
  so a cleanse or Panacea answers it. Until v0.0.3 Trick Blade was a plain
  hit, and puppets rose at 25%.

- **Pandemonium spoils the foes' setup (v0.0.9).** Chaos eats the next buff
  each foe would get, so it lands best just before the foes buff up: before
  a Shaman's Attune, a Paladin's Guard, a Fairy's Pixie Dust or a Knight's
  Bulwark. It gives the Jester a job from round 1, before Puppeteer has a
  corpse. Its name, power 10 and cooldown 3 are placeholders.

- **The corpse is used up.** It's gone from the foe's side, so the foe can
  no longer resurrect that character. Like Raise Dead and Soul Harvest, it's
  a counter to resurrection; unlike them, it turns the foe's loss into the
  Jester's gain.
- **The puppet is a minion**: it doesn't count for winning or losing, and
  when it falls it leaves no corpse ([summons.md](summons.md#minions)), so
  it can't be puppeted again, resurrected by its old team, or raised by a
  Necromancer.
- **The puppet has its full kit:** its moves, its basic attack and its
  passive, now working for the Jester's team ([summons.md](summons.md#the-jesters-puppets)).
- **One puppet at a time, like the Shaman's totem.** Puppeteer can't be used
  while the Jester's puppet stands, and its cooldown starts only when the
  puppet falls: 5 of the Jester's turns after that. A puppet that lasts many
  turns doesn't use them up.
- **When the Jester falls, its puppet falls with it.** The strings are cut.
- Only foes' corpses: the Jester can't puppet its own team's fallen.
- A foe the Grim Reaper or Wither knocked out leaves no corpse, so it can't
  be puppeted.

## Placeholder: tier and role

| Character   | Tier   | Role                                   |
|-------------|--------|----------------------------------------|
| Barbarian   | Normal | heavy hitter, low defense              |
| Ranger      | Fast   | single-target damage from range        |
| Paladin     | Normal | tank, protects allies                  |
| Cleric      | Normal | healer                                 |
| Monk        | Normal | quick hits, changes foes' tiers        |

## Placeholder: moves

Each has three moves. Target shapes today are the user, one foe and all foes;
moves marked with * need a new shape (one ally, all allies).

| Character   | Moves                                                         |
|-------------|---------------------------------------------------------------|
| Barbarian   | Cleave (one foe), attack up (user), Whirlwind (all foes) |
| Ranger      | Aimed Shot (one foe), Volley (all foes), Hunter's Mark (one foe: takes more damage) |
| Paladin     | Smite (one foe), Guard* (one ally: redirect hits to the Paladin), Lay on Hands* (one ally: heal) |
| Cleric      | Heal* (one ally), Prayer* (all allies: small heal), Sacred Flame (one foe) |
| Monk        | Flurry (one foe), Stunning Strike (one foe: down a tier), Focus (user: up a tier) |
