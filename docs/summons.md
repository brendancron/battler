# Summons

Some moves summon something onto the field: a character-like thing with its
own HP that isn't one of the team's four characters.

## Minions

Every summon is a **minion**: it carries a Minion marker (an effect that can't
be removed) saying it isn't one of the team's characters. Minions don't count
for winning or losing: a team whose characters have all fallen has lost, even
with minions still standing.

Whether a minion takes turns depends on the minion. One that does acts like a
character, in its speed tier's phase ([speed.md](speed.md)); one that doesn't
never comes up in the turn order.

Minions leave no corpse: a destroyed minion is simply gone, and can't be
resurrected ([fainting.md](fainting.md)). (For now; a minion that does could
come later.)

## The Shaman's totem

- Summoned by the Shaman's Totem move ([characters.md](characters.md#shaman)).
  It's a minion, and it doesn't take turns: it just stands there giving its
  bonus.
- Has its own HP pool, and is fairly squishy.
- While it stands, every character on the Shaman's team has **+25% attack and
  +25% defense**. This is its own multiplier (× 1.25), separate from the
  × 1.5 stat ups and downs ([effects.md](effects.md)), and it multiplies with
  them.
- The bonus is an **aura** on the totem, Totem's Blessing
  ([effects.md](effects.md#auras)), not a buff on each ally: it reaches
  everyone on the team while the totem stands, including anyone who joins
  later (a resurrected ally, a skeleton), and nothing done to an ally can
  take it off. It can't be dispelled or stolen from the totem, and a second
  totem doesn't add to it. (Until v0.0.3 each ally had its own copy as a
  buff.)
- When it's destroyed, the bonus ends.
- Foes can target it like a character: single-target moves can pick it
  (targeting tiers apply to it as to anyone), and area moves hit it along with
  everyone else. Killing it is how a foe ends the bonus.
- It's an ally like any other for its team: single-target heals, shields and
  buffs can pick it, and team-wide ones (Prayer, Healing Rain) reach it too.
- One totem at a time, and it stays until it's destroyed. The Totem move can't
  be used while the Shaman's totem stands.
- **Its cooldown starts when the totem is destroyed, not when it's summoned:**
  3 of the Shaman's turns after the totem falls. A totem that stands for many
  turns doesn't use those turns up; the cooldown only begins once it's gone.
- Its HP and defense are placeholders: **20 HP and 32 defense** (40 with its
  own aura, as before v0.0.3, when it was 40 without one), so an average
  hit of power 30 takes most of it.

## The Necromancer's skeletons

- Raised by the Necromancer's Raise Dead from an ally's corpse
  ([characters.md](characters.md#necromancer)). The corpse is removed.
- A minion that **takes turns**: the first one in the game. It acts in its
  tier's phase like a character, after its team's characters in that phase,
  in slot order. (A new skeleton takes the slot of a destroyed one, so it
  may act before an older skeleton.)
- **Only a basic attack**, Bone Claw, power 10 ([moves.md](moves.md#basic-attacks)).
  It has no other moves and no passive.
- Stats are decent, so it's a real threat rather than a speed bump:
  **50 HP, attack 100, defense 80, Normal**. All placeholders.
- Every skeleton is the same, whoever's corpse it came from. (Stats that
  depend on the corpse were considered and set aside for now.)
- It can act in the round it's raised, by the usual phase rules
  ([speed.md](speed.md)): raised in the Slow phase, a Normal skeleton acts in
  that Slow phase.
- Like the totem, it's an ally for its team (heals, shields and buffs can
  pick it) and a target for foes; targeting tiers apply to it as to anyone.
- There's no limit on how many skeletons stand at once; the corpses and the
  cooldown limit it.
- It doesn't count for winning or losing, and when it's destroyed it leaves
  no corpse, like any minion.

## The Jester's puppets

- Raised by the Jester's Puppeteer from a foe's corpse
  ([characters.md](characters.md#jester)). The corpse is removed.
- A minion on the Jester's team, at **33% of its max HP** (placeholder, 25% until v0.0.3; max
  HP lost to Reap or fatigue before it fell stays lost).
- It takes turns in its own speed tier's phase, after its team's characters
  in that phase, like a skeleton.
- **It keeps its full kit**: its basic attack, its moves and its passive.
  Everything it does is for its new team: "ally" and "foe" now mean the
  Jester's team and its old team.
- It comes back as resurrection does ([fainting.md](fainting.md#resurrection)):
  no effects but its passive, and its cooldowns as they were when it fell.
- Its passive works for the Jester's team, so some get strong. A puppeted
  Paladin's Last Rites brings back one of the Jester's fallen when the
  puppet falls; a puppeted Alchemist hands the Jester's team Panaceas; a
  puppeted Cleric's Vigil heals them. That's the point of the move, and why
  the Jester itself is weak.
- Being a minion, it can be targeted, healed and buffed like the totem or a
  skeleton.
- **One at a time.** Puppeteer can't be used while the puppet stands, and
  its cooldown starts when the puppet falls, as the totem's does.
- **When the Jester falls, its puppet falls too,** straight after it. It's a fall, not damage, so shields and Invincible don't stop
  it, and anything that happens on a fall still happens: a puppeted
  Paladin's Last Rites fires, and may bring back the Jester itself if it's
  the first of the team's fallen in slot order. A puppet leaves no corpse, so
  resurrecting the Jester later doesn't bring it back; Puppeteer's cooldown
  has started, so a resurrected Jester waits it out before puppeting again.
