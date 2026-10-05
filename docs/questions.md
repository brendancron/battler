# Questions

Design questions still to settle. Answers go into the doc the question came
from, and the question is removed from here.

## Open

None right now.

## Later

Set aside on purpose; can be added without changing what exists.

- **Replay** ([speed.md](speed.md)). An effect that lets a character act a
  second time in a round. When it acts (straight away, or when priority next
  reaches its team) is undecided.
- **Effects that opt out of removal** ([effects.md](effects.md)). Which effects
  besides passives can't be cleansed, dispelled or stolen, and how they say so.
- **Default trigger order** ([events.md](events.md)). After the triggers on the
  character an event is about, what order the rest fire in. It is a strategy,
  so it can be settled when it matters.
- **Barbarian passive values** ([characters.md](characters.md)). The final `a`
  and `b` in its formula, after playtesting. Placeholders are a = 2, b = 2.
- **Power, heal and cooldown numbers** ([characters.md](characters.md),
  [moves.md](moves.md)). Every move's power, heal amount and cooldown is a
  placeholder until balancing.
- **Paladin numbers** ([characters.md](characters.md)). Smite power 20, Lay on
  Hands 15 + 15, Guard for 2 rounds, and Last
  Rites' 50% are all placeholders to tune.
- **Witch passive and third move** ([characters.md](characters.md)). The Witch
  has Brimstone and Hex for now.
- **Grim Reaper third move** ([characters.md](characters.md)). It has Reap and
  Soul Siphon for now.
- **Warlock passive and third move** ([characters.md](characters.md#warlock)).
  It has Writhing Depths (it was the Pyromancer's Inferno) and The Final
  Offering.
- **Shaman passive and third move** ([characters.md](characters.md)). It has
  Healing Rain and Ancestral Call for now.
- **Arranging slots after the draft** ([draft.md](draft.md#slots)). For now a
  team's slots follow its pick order.
- **Choosing home or away** ([draft.md](draft.md#home-and-away)). For now a coin
  flip decides.
- **Balance checker progress back on stdout** (src/balance.cx). Its progress
  lines use `printerr` because `print` isn't flushed until exit
  (CronyxLang#138). Once that's fixed, change `progress()` back to `print`.
- **Bard passive** ([characters.md](characters.md#bard)). It has Allegro,
  Largo and Crescendo.
- **Fairy third move** ([characters.md](characters.md#fairy)). It has Fairy
  Ring and Pixie Dust.
- **Necromancer passive and third move** ([characters.md](characters.md#necromancer)).
  It has Wither and Raise Dead for now.
- **Vampire third move** ([characters.md](characters.md#vampire)). It has Bite
  and Hemorrhage for now; Crimson Veil was rejected.
- **Cryomancer passive and third move** ([characters.md](characters.md#cryomancer)).
  It has Blizzard and Frostbite for now.
- **Construct third move** ([characters.md](characters.md#construct)). It has
  Piston Slam and Lockdown for now.
- **Stormbringer passive and third move** ([characters.md](characters.md#stormbringer)).
  It has Thunderstorm and Chain Lightning, and no passive for now.
- **Ninja second and third moves** ([characters.md](characters.md#ninja)). It
  has Shuriken for now. Ideas: Smoke Bomb, Shadow Strike, Caltrops.
- **Swashbuckler passive, second and third moves** ([characters.md](characters.md#swashbuckler)).
  It has Plunder for now.
- **Jester passive and third move** ([characters.md](characters.md#jester)). It
  has Trick Blade and Puppeteer for now.
- **Assassin third move** ([characters.md](characters.md#assassin)). It has
  Backstab and Execution for now.
