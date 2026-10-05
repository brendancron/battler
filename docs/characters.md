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

Tank ([archetypes.md](archetypes.md)). All numbers here are placeholders to
tinker with.

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
| Smite        | one foe             | Dispels the foe's buffs ([effects.md](effects.md#removing-and-moving-effects)), then a direct hit, power 20 ([damage.md](damage.md)). It used to add attack down for 2 rounds too. |
| Guard        | the user            | Taunt with defense up: the Paladin's targeting tier goes to 1 ([targeting.md](targeting.md)) and its defense is × 1.5, both for 2 rounds. |
| Lay on Hands | one ally, or itself | Heals the ally 15 and the Paladin 15. Used on itself, the Paladin heals 30. |

- Smite dispels first, so a shield or barrier is gone before the hit lands.
  Every buff goes, taunts included: Smiting a taunting Tank frees the
  Paladin's team to pick anyone. Passives can't be dispelled.
- Guard's taunt and defense up are one effect that counts down at the end of
  each round, like other effects ([effects.md](effects.md)). Used in round 1,
  it lasts through the end of round 2. Other taunts, like the Knight's, don't
  raise defense.
- Before this, the defense up came from the passive, Steadfast (defense ×1.5
  while taunting).

### Ranger

Rogue ([archetypes.md](archetypes.md)).

**Passive: Keen Eye.** The Ranger ignores targeting restrictions
([targeting.md](targeting.md#ignoring-restrictions)): it can pick any foe still
standing, so a taunt can't protect the foe it is hunting. (The name is a
placeholder.)

**Moves**

| Move          | Target  | Effect |
|---------------|---------|--------|
| Aimed Shot    | one foe | A direct hit, power 30 ([damage.md](damage.md)). |
| Hunter's Mark | one foe | Defense down for 2 rounds ([effects.md](effects.md)), so the foe takes 50% more damage from everyone. |
| —             |         | To be designed. |

Rejected so far:

- **Volley** (all foes): Rogues aren't meant to deal area damage.

### Cleric

Support ([archetypes.md](archetypes.md)).

Stats (placeholders): attack 70, defense 75 (81 until v0.0.4).

**Passive: Vigil.** At the end of each round, heals the ally with the lowest
HP for 8. The Cleric counts as an ally, and a tie goes to the lowest slot.
(The name and the 8 are placeholders; it was 10 until v0.0.4.)

**Moves** (names are placeholders)

| Move    | Target                  | Effect |
|---------|-------------------------|--------|
| Prayer  | all allies, itself too  | Heals 20 each. |
| Purify  | one ally, or itself     | Cleanses the ally of debuffs ([effects.md](effects.md)), then heals it 40. Cleansing first means a Heal Block is gone before the heal lands. |
| Resurrection | a fallen ally        | Resurrects it at 40% of its max HP ([fainting.md](fainting.md)). Cooldown 7. |

Purify is meant to heal more than the Paladin's Lay on Hands: the Cleric is the
healer, and the Paladin isn't picked for its heal. 40 is a placeholder.

Resurrection was the Shaman's Ancestral Call until v0.0.3; bringing allies
back suits the healer. It replaced Sanctuary (heal 20 and a barrier against
the next direct hit, cooldown 3).

### Witch

Mage ([archetypes.md](archetypes.md)).

**Passive:** to be designed.

**Moves** (names and powers are placeholders)

| Move      | Target   | Effect |
|-----------|----------|--------|
| Brimstone | all foes | A direct hit on every foe standing, power 25 ([damage.md](damage.md)). Then every ally, the Witch included, heals 20% of the total damage dealt. |
| Hex       | one foe  | The foe skips its next turn, then the debuff goes away. Shown as turning the foe into a frog. |
| —         |          | To be designed. |

Brimstone details:

- "Damage dealt" is the HP the foes actually lost, as for the Grim Reaper's
  Soul Siphon: damage a shield soaks doesn't count, and neither does damage
  past a foe's last HP.
- Each ally heals the full 20% (it isn't split between them), so the heal is
  worth most early, when there are many foes to hit and many allies to heal.
  Heal Block stops it as it stops any heal.
- 20% is a placeholder.

Hex details:

- "Next turn" is the foe's next chance to act. If it hasn't acted yet this
  round, it loses this round's action; if it already has, it loses next
  round's.
- Its turn still comes up in the usual order, so the order of everyone else
  doesn't change; it just does nothing.
- It's a debuff, so it can be cleansed before the turn is lost.

### Alchemist

Support ([archetypes.md](archetypes.md)).

Each move brews something and throws it. A potion goes to one ally (or the
Alchemist itself): it's a buff ([effects.md](effects.md)) that waits for its
moment, does its job once, and is used up. The Alchemist's strength is
timing: its potions act mid-round, the moment they're needed, where the Cleric
heals on its turn and the Shaman's regen pays out at round end. Its acid goes
on the foes.

**Passive: Panacea Supply.** At the start of the battle, every teammate (the
Alchemist included) gets a Panacea, which blocks the next debuff that would
be put on its holder. It happens once; after that, Panacea Mist hands out
more. (Until v0.0.3 it topped up every round, to one each.)

**Moves**

| Move                  | Target   | Potion |
|-----------------------|----------|--------|
| Panacea Mist          | all allies, itself too | Heals each ally 15 and gives it a Panacea, on top of any it holds. Cooldown 3. |
| Greater Health Potion | one ally | When damage takes the ally below 65% of its max HP, heals it 45 at once. Healing past max HP is lost. Cooldown 3. |
| Acid Flask            | all foes | 6 poison on every foe standing ([effects.md](effects.md#poison)): 21 damage each over six rounds. Cooldown 3. |

Its basic attack is Bottle Bash (power 8). The 65%, 45, 15, 6 poison and
cooldowns are placeholders. Acid Flask is
the exception to single-target moves: it's the Alchemist's way to pressure
the whole foe team, slowly.

- **Greater Health Potion fires early on purpose.** It triggers below 65%,
  not lower, so it's there before the ally is in real danger, even though
  some of the 45 is lost to max HP: an ally hit from 70 to 60 of 100 heals
  only 40. It's measured against the ally's current max HP, so after a Reap
  to 90 max it fires below 58.5 (under 59).
- It fires the moment the damage lands, before anyone else acts, so the ally
  is topped up before the next hit. If the ally is already below 65% when it
  gets the potion, it fires straight away.

History: the first kit had Health Potion (all allies, heal 25 when more than
20 below max HP) and Panacea (all allies, block the next debuff), with
Energize at all allies, +25%. Panacea became the passive, Energize became one
ally at +75%, and the defense went from 60 to 80 after the Alchemist won only
28% of balance games. Health Potion still lost to the Shaman's Healing Rain
(54 per ally over 3 rounds) and the Cleric's bigger heals, and the Alchemist
won only 34% of 2,000 games, so it became the single-target Greater Health
Potion.

- **Potions stack, with no limit.** The Mist adds a Panacea to whatever each
  ally holds, so a team can hold one, two or many and go into a debuff-heavy
  fight (a Monk, a Witch's Hex, a Jester's Silence) blocking several each.
- Until v0.0.3 the third move was Energize (the ally's next attack +75%), and
  the basic attack was Acid Splash. Energize gave way to Panacea Mist, which
  fits a support built on timing and debuff protection better.
- Panacea never acts straight away: it doesn't remove debuffs the holder
  already has, only blocks the next one put on it.
- Potions stack as charges. Tossing a potion an ally already holds adds a
  charge, and each time the potion does its job it uses one. Two Greater
  Health Potions heal twice, and two Panaceas block the next two debuffs.

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

**Passive: Soul Harvest.** A foe the Grim Reaper knocks out leaves no
corpse: it's removed at once, so the foe can never be resurrected
([fainting.md](fainting.md#removing-corpses)). (The name is a placeholder.)

- It counts any damage the Grim Reaper deals: its hits and Soul Siphon's
  drain. A foe that falls to someone else, or to poison or fatigue, leaves a
  corpse as usual.
- It replaces Death's Door, which executed any foe left below 10% of its max
  HP by anyone's damage. Execution is gone from the game.

**Moves** (names and numbers are placeholders)

| Move        | Target  | Effect |
|-------------|---------|--------|
| Reap        | one foe | A scythe hit, power 30 ([damage.md](damage.md)). A third of the damage also comes off the foe's max HP. |
| Soul Siphon | one foe | A hit, power 25. The Grim Reaper heals half the damage dealt. |
| —           |         | To be designed. |

Max HP lost to Reap can't be healed back: a foe Reaped for 30 has max HP 90,
so heals stop at 90. Every character still starts at 100 max HP; this is the
only way it goes down.

Soul Siphon heals half the HP the foe actually lost, rounded down: hitting a
foe with 10 HP left for 30 heals 5, not 15.

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
Stats (placeholders): attack 65, defense 50 (75 until v0.0.3, 70 until
v0.0.4).

Balanced like the Warlock: if it gets its first turns in, its totem and
Healing Rain carry the team, but it's frail and Slow, so the team has to keep
it alive until then.

**Passive:** to be designed.

**Moves** (names and numbers are placeholders)

| Move           | Target        | Effect |
|----------------|---------------|--------|
| Healing Rain   | all allies    | Heals each 25 at once (placeholder), then regen 20 for 5 rounds on each ([effects.md](effects.md)): 125 HP each in all. Shaman's big turn-1 move. (Until v0.0.5 it was regen only, for 3 rounds: 18 a round, then 24 in v0.0.3 and 30 in v0.0.4.) |
| Spirit Link    | one foe       | Links the Shaman to the foe for 2 rounds: 50% of the damage the Shaman takes goes to the foe instead, and 75% of the healing the foe takes goes to the Shaman. Cooldown 3. |
| Totem          | —             | Summons a totem with its own small HP pool ([summons.md](summons.md)). While it stands, the whole team has +35% attack and +35% defense (25% until v0.0.4). |

Regen heals at the end of each round and doesn't stack: casting it again resets
it to 5 rounds rather than doubling the healing.

The total is high on purpose: the Shaman is Slow, and regen pays out over
several rounds, so the healing arrives late.

- **Spirit Link is two ends,** a buff on the Shaman and a debuff on the foe.
  The link holds only while both do: dispelling the Shaman's end or
  cleansing the foe's ends the whole link, and so does either wearing off. A
  Panacea on the foe blocks its end, so the link never holds. The Shaman's
  end can't be stolen.
- **The damage share** is taken after any cap and before shields, and reaches
  the foe as indirect damage from the Shaman (so barriers and parries don't
  stop it). It's lost if the foe has fallen.
- **The healing share** is of the healing the foe could take: healing past
  its max HP isn't shared. Heal Block on the foe stops the heal, and so the
  share; healing past the Shaman's max HP is lost.
- Until v0.0.3 the Shaman's second move was Ancestral Call, now the
  Cleric's Resurrection. The 50%, 75%, 2 rounds and cooldown are
  placeholders.

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

**Passive: Predator.** The Assassin deals **50% more damage** (placeholder; 25% before v0.0.2)
to a target **below 50%** of its max HP. It finishes off wounded foes.

- Below means strictly below: a target at exactly half its max HP takes
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
| Backstab  | one foe             | A heavy direct hit, power 40 ([damage.md](damage.md)). Cooldown 3. |
| Execution | the weakest foe     | A direct hit, power 20, on the foe with the lowest HP. If it knocks that foe out, it hits the foe with the next lowest HP, and so on until a hit doesn't knock its target out or no foes are left. Cooldown 4. |

- Execution picks its own targets, so the player doesn't choose. "Lowest HP"
  is current HP, and a tie goes to the lowest slot.
- Execution ignores taunts and other targeting tiers, as area moves do
  ([targeting.md](targeting.md)): a Tank can't shield a wounded ally from it.
- Each Execution hit is a first hit on a new foe, so Predator applies to each
  foe below half its max HP.

### Bard

Support ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md)).
Stats (placeholders): attack 70, defense 75.

The Bard controls tempo. Its songs move characters between speed tiers
([speed.md](speed.md)), changing the order of the round. The Bard itself is
Fast, so it usually sings before the characters it moves have acted.

Its niche is the **early snowball**: it sets up one huge move early (a
Warlock's Writhing Depths, a Reaper's Reap) so its team gets a quick kill
and rolls on from there. It should be strongest early and fall off in long
games.

**Passive: Crescendo.** A team aura ([effects.md](effects.md#auras)) that
builds with every kill. It starts with no stacks; each foe character that
falls while the Bard stands adds one, and each stack gives the whole team,
the Bard included, × 0.2 more attack and defense: × 1.2 after one kill,
× 1.4 after two, × 1.6 after three. The 0.2 is a placeholder.

- **Any foe character counts,** whatever knocked it out: a hit, poison (which
  the engine doesn't tie to who applied it), fatigue, or a Warlock's Final
  Offering. Minions (totems, skeletons, puppets) don't.
- **It's an aura, so it lives with the Bard.** While the Bard is down the
  bonus stops; its stacks stay, and come back if it's resurrected. Nothing
  done to the allies touches it, and it can't be dispelled or stolen.
- **It's the snowball.** The first kill makes the next one easier, and
  Allegro is how the Bard gets that first kill early.
- It's its own multiplier, separate from the × 1.5 stat ups and the totem's
  aura, and multiplies with them.
- Until v0.0.3 Crescendo was a move: × 1.2 attack and defense on every ally
  for 2 rounds, cooldown 4.

**Moves** (names, durations and cooldowns are placeholders)

| Move    | Target   | Effect |
|---------|----------|--------|
| Allegro | one ally | Speed up and attack up (× 1.5, [effects.md](effects.md)) for the ally's next turn ([effects.md](effects.md#speed-up-and-speed-down)): it moves one tier faster and hits harder, then both go away. Cooldown 3. |
| Largo   | one foe  | Speed down for the foe's next turn: it moves one tier slower. Also attack down for 2 rounds ([effects.md](effects.md)). Cooldown 3. |

- **One tier only.** Allegro takes Slow to Normal or Normal to Fast, never
  Slow to Fast. Largo likewise moves a foe down one tier.
- **Next turn.** If the ally hasn't acted yet this round, Allegro speeds up
  this round's action; if it already has, it speeds up next round's. Either
  way the speed up ends when that turn does. The same goes for Largo's speed
  down on a foe. A turn lost to Hex still counts.
- **A tier change takes effect at once.** Who can act is checked fresh at
  every step ([speed.md](speed.md#phases)), so a Slow ally sped up during the
  Fast phase acts in this round's Normal phase, and a Normal foe slowed
  before it acts waits for the Slow phase.
- **Allegro is one boosted action.** The attack up lasts exactly as long as
  the speed up: the ally's next turn, then both wear off together. Rounds
  don't wear it off, so an ally that already acted this round gets it on
  next round's turn. A Slow Warlock sung to in the Fast phase fires Writhing
  Depths in the Normal phase at × 1.5.
- Until v0.0.2 Allegro took 1 off each of the ally's running cooldowns
  instead of the attack up. That paid off most in long games, the opposite
  of the Bard's niche; cooldown cutting is now the Construct's Overclock.
- Allegro can't target the Bard. It's already Fast, so the speed up would do
  nothing.
- Largo's two debuffs are separate effects, so a cleanse can take off one
  and leave the other. The attack down is the usual one, as from Disarming
  Palm.

### Fairy

Support ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md)).
Stats (placeholders): attack 65, defense 70.

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
  (it counts for a foe's Crescendo), but it isn't the hitter's kill, so the
  Fairy keeps its corpse even against the Grim Reaper.
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
Stats (placeholders): attack 110, defense 60.

**Passive:** to be designed.

**Moves** (names, powers and cooldowns are placeholders)

| Move   | Target   | Effect |
|--------|----------|--------|
| Wither | all foes | A direct hit on every foe standing, power 20 ([damage.md](damage.md)). A foe it knocks out leaves no corpse ([fainting.md](fainting.md#removing-corpses)). Cooldown 4. |
| Raise Dead | an ally's corpse | Removes the corpse and summons a skeleton in its place ([summons.md](summons.md#the-necromancers-skeletons)): a minion with decent stats that only has a basic attack. Cooldown 5. |
| —      |          | To be designed. |

- Wither removes the corpse of a foe its own hit knocks out, like the Grim
  Reaper's Soul Harvest but only for this move. A foe that falls to anything
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
little above the Rogues' (placeholder: attack 115, defense 62; 50 until
v0.0.2), so it takes a lot from
each hit, and gets its staying power from healing it back with every hit it
deals. It wins one-on-ones the way Warriors should, but through sustain
rather than raw toughness.

**Passive: Sanguine.** The Vampire heals 30% of all the damage it deals.
(The name and the 30% are placeholders.)

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
| Bite | one foe | A direct hit, power 25 ([damage.md](damage.md)). Cooldown 2. |
| Hemorrhage | one foe | A light direct hit, power 10, and bleed 10 for 3 rounds ([effects.md](effects.md#bleed)). Cooldown 3. |
| —    |         | To be designed. |

Bite is the Vampire's plain hit, the steady source of damage that Sanguine
heals from. Its power sits between the Monk's strikes (20) and Cleave (30):
the lifesteal is the extra.

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
Stats (placeholders): attack 110, defense 55.

The Cryomancer brings chill and freeze ([effects.md](effects.md#chill-and-freeze)).

**Passive:** to be designed.

**Moves** (names, powers and cooldowns are placeholders)

| Move     | Target   | Effect |
|----------|----------|--------|
| Blizzard | all foes | A direct hit on every foe standing, power 20 ([damage.md](damage.md)), and chill on each. A foe already chilled is frozen. Cooldown 4. |
| Frostbite | one foe | A direct hit, power 20, and chill. A foe already chilled is frozen. Cooldown 2. |
| —        |          | To be designed. |

- The chill goes on every foe Blizzard reaches, whether or not a barrier
  blocks the damage, as with any debuff a move adds.
- Blizzard's power is below Brimstone's 25: the chill is the extra.
- **Blizzard then Frostbite freezes one foe.** Blizzard chills the whole foe
  team, and a Frostbite on the Cryomancer's next turn, before the chill wears
  off, freezes the foe of its choice. Frostbite's cooldown is longer than a
  chill lasts, so two Frostbites alone can't freeze; it takes Blizzard, or
  another chill from a teammate.
- Frostbite follows targeting tiers, so a taunting Tank decides who it can
  reach. Its power is below a plain hit like Cleave (30) because of the chill.

### Frost Giant

Tank ([archetypes.md](archetypes.md)). Slow ([speed.md](speed.md)).
Stats (placeholders): attack 85, defense 150.

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
| Avalanche    | all foes | A light direct hit on every foe standing, power 13 ([damage.md](damage.md)), and chill on each ([effects.md](effects.md#chill-and-freeze)). A foe already chilled is frozen. Cooldown 4. |
| Glacial Roar | the user | Taunt for 2 rounds ([targeting.md](targeting.md#taunt)). Cooldown 3. |
| Cryosleep    | one ally | Puts the ally in Cryosleep ([effects.md](effects.md#cryosleep)): it skips its next turn and takes no damage until then. Heals it 15. Cooldown 4. |

- **Avalanche is light on purpose.** The Frost Giant is a Tank, so its area
  hit is well under Blizzard's 20 (13; 10 until v0.0.2); the chill is what matters. Avalanche on one
  turn and Blizzard on the Cryomancer's next freezes the whole foe team.
- **Glacial Roar feeds the aura.** Foes that have to pick the Giant get
  chilled for it. Like the Knight's taunt it doesn't raise defense, unlike the
  Paladin's Guard. Its 2 rounds count down at the end of each round, like
  other taunts.
- **Cryosleep trades the ally's turn for safety.** The ally skips its next
  turn, and until that turn has passed it takes no damage. The heal comes at
  once.
  - "Next turn" is as for any freeze: this round's if the ally hasn't acted
    yet, otherwise next round's. The Giant is Slow, so the ally has usually
    acted already, and it stays safe through the rest of this round
    and next round up to its skipped turn. A Fast ally thaws early next round;
    a Slow one stays safe for most of it.
  - Cryosleep is its own buff, not a freeze and not Invincible, though it
    works like both together. Being a buff, a Panacea doesn't block it and a
    cleanse doesn't end it; a dispel does, waking the ally early.
  - It can't target the Frost Giant itself: an untouchable taunting Tank would
    blank every single-target move the foes have.

### Construct

Tank ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md)). (The
name is a placeholder.)

Its niche is the **Fast tank**: the only Tank that acts before the foes do,
so its taunt and protection are up before their first hits land. (Normal
until v0.0.2.) It's a **transient tank built to counter single-target
burst**: Ironclad blunts the big hits its taunt draws, but it can't be
healed, so it doesn't last. That's niche on purpose.
Stats (placeholders): attack 75, defense 110 (85 and 120 until v0.0.3).

**Passive: Ironclad.** Two parts. (The name and the 25% are placeholders.)

- **It can't be healed.** No healing of any kind restores its HP: heals from
  moves, regen, Vigil, Health Potions, drains, Cryosleep's heal.
  It's like a Heal Block that never ends and can't be cleansed.
- **No hit takes more than 25% of its max HP.** Any single instance of damage
  is capped at a quarter of its current max HP, rounded down: 25 at 100 max
  HP. Bringing it down from full always takes at least four hits.

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
  hit. Max HP lost also lowers the cap: at 80 max HP it's 20.
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
| Thunderstorm    | all foes              | A direct hit on every foe standing, power 15 ([damage.md](damage.md)). Cooldown 4. |
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
Stats (placeholders): attack 110, defense 50.

**Passive: Shadowed.** The Ninja is always shrouded
([targeting.md](targeting.md#shroud)): its targeting tier is -1, so foes can
only pick it with a single-target move once no one else on its team is left
standing. (The name is a placeholder.)

- **Only single-target picks.** Area moves hit it as usual, and so do moves
  that choose their own targets, like the Assassin's Execution. The Ranger's
  Keen Eye ignores targeting tiers, so the Ranger can pick it any time.
- **Minions count as allies standing.** A totem or skeleton is in tier 0, so
  it keeps the Ninja hidden even after the Ninja's teammates have fallen.
- **Two shrouded characters on a team** are both in tier -1, so once
  everyone else is down, foes can pick either.
- In Chain Lightning, the Ninja can only be picked after every foe in a
  higher tier, so it takes the smallest bolt left.
- It's a passive, so it can't be dispelled or stolen.

**Moves** (names, powers and cooldowns are placeholders)

| Move     | Target  | Effect |
|----------|---------|--------|
| Shuriken | one foe | A direct hit, power 25 ([damage.md](damage.md)). Cooldown 2. |

Shuriken is a plain hit, a little under the Ranger's Aimed Shot (30): the
Ninja's safety behind its shroud is worth something. More moves later.

### Swashbuckler

Warrior ([archetypes.md](archetypes.md)). Fast ([speed.md](speed.md)).
Stats (placeholders): attack 95, defense 68 (attack 110 until v0.0.4).

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
| Pistol Shot | any foe | A direct hit, power 30 ([damage.md](damage.md)), on any foe standing: taunts and other targeting tiers don't apply. Cooldown 2. |
| Plunder | one foe | Steals every buff the foe has and puts them on the Swashbuckler, then a direct hit, power 20 ([damage.md](damage.md)). Cooldown 3. |
| En Garde | the user | Parry: the next direct hit a foe lands on the Swashbuckler is blocked, and it ripostes, a direct hit of power 12 back on the attacker. It lasts until it's used, however many rounds that takes. Cooldown 4. |

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
stats are low (placeholder: attack 90, defense 50) and its hit is light.

**Passive:** to be designed.

**Moves** (names, numbers and cooldowns are placeholders)

| Move      | Target          | Effect |
|-----------|-----------------|--------|
| Trick Blade | one foe       | A direct hit, power 20 ([damage.md](damage.md)), and Silence for the foe's next turn ([effects.md](effects.md#silence)): only its basic attack. Cooldown 2. |
| Puppeteer | a foe's corpse  | Raises the fallen foe as a minion on the Jester's team ([summons.md](summons.md#the-jesters-puppets)), at 33% of its max HP. One puppet at a time; cooldown 5, from when the puppet falls. |

- **Trick Blade disables before the first corpse.** The Jester is Fast, so it
  usually lands before the foe acts that round: a Warlock or Cryomancer
  silenced in round 1 loses its nuke for that turn. It's the Jester's early
  job, until Puppeteer has a corpse to work with. The Silence is a debuff,
  so a cleanse or Panacea answers it. Until v0.0.3 Trick Blade was a plain
  hit, and puppets rose at 25%.

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
| Paladin     | Slow   | tank, protects allies                  |
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
