# Questions

Design questions still to settle. Answers go into the doc the question came
from, and the question is removed from here.

## Open

- **Move and passive descriptions in the web UI** ([web.md](web.md#the-page)).
  The draft cards and move buttons would show what each move does, but that
  text lives only in `characters.md`; the code has names, cooldowns and
  shapes. Should the page show names only, should each `MoveDef` and passive
  get a short description in the code, or should the page pull the text from
  the docs?
- **More ways to remove corpses** ([fainting.md](fainting.md#removing-corpses)).
  The Cleric's Resurrection (v0.0.3) made bringing allies back common, and
  only the Grim Reaper's Soul Harvest, the Necromancer's Wither and Raise
  Dead, and the Jester's Puppeteer take corpses away. More counters would
  check Resurrection without nerfing it directly: who should get one, and
  how (a passive, a move, an effect of some hits)?


## Designed, not built yet

Agreed designs waiting to be coded, all at once when the user says so. Each
is written up in its doc; remove it from here once it's built and tested.

None: v0.0.9's designs are all built.

## Later

Set aside on purpose; can be added without changing what exists.

- **The Frost Giant's third move** ([characters.md](characters.md#frost-giant)).
  Cryosleep went to the Cryomancer in v0.0.9, leaving it Avalanche and
  Glacial Roar; it plays v0.0.9 with two moves to see where it stands.
  Shatter (a heavy hit, triple on a frozen foe, breaking the freeze) was the
  front-runner.

- **Replay** ([speed.md](speed.md)). An effect that lets a character act a
  second time in a round. When it acts (straight away, or when priority next
  reaches its team) is undecided.
- **Effects that opt out of removal** ([effects.md](effects.md)). Which effects
  besides passives can't be cleansed, dispelled or stolen, and how they say so.
  The Alchemist's Energized (v0.0.7) is the first: immune to all three.
- **Default trigger order** ([events.md](events.md)). After the triggers on the
  character an event is about, what order the rest fire in. It is a strategy,
  so it can be settled when it matters.
- **Barbarian passive values** ([characters.md](characters.md)). The final `c`,
  `a` and `b` in Rage's formula, after playtesting. v0.0.2 has c = 0.77,
  a = 1.3, b = 1.75.
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
- **Shaman passive** ([characters.md](characters.md)). It has Attune, Totem
  and Healing Rain (v0.0.9).
- **Choosing home or away** ([draft.md](draft.md#home-and-away)). For now a coin
  flip decides.
- **Balance checker progress back on stdout** (src/balance.cx). Its progress
  lines use `printerr` because `print` isn't flushed until exit
  (CronyxLang#138). Once that's fixed, change `progress()` back to `print`.
- **Fairy third move** ([characters.md](characters.md#fairy)). It has Fairy
  Ring and Pixie Dust.
- **Necromancer third move** ([characters.md](characters.md#necromancer)).
  It has Wither and Raise Dead, and Death Throes as its passive (v0.0.8).
- **Stormbringer passive and third move** ([characters.md](characters.md#stormbringer)).
  It has Thunderstorm and Chain Lightning, and no passive for now.
- **Ninja second and third moves** ([characters.md](characters.md#ninja)). It
  has Shuriken for now. Ideas: Smoke Bomb, Shadow Strike, Caltrops.
- **Jester passive and third move** ([characters.md](characters.md#jester)). It
  has Trick Blade and Puppeteer for now.
- **Assassin third move** ([characters.md](characters.md#assassin)). It has
  Backstab and Execution for now.
