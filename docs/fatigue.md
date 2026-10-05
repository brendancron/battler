# Fatigue and the end of a game

Long games are discouraged by **fatigue**: after a while, everyone on the
field starts wasting away, so a game that drags on is decided by whoever has
the most left.

## Fatigue

- **When:** at the end of every round after round 10, so at the end of rounds
  11, 12, 13, and on.
- **Who:** every standing character on both sides, minions included.
- **What:** each loses **10 max HP** (placeholder), the way Reap takes max HP
  ([characters.md](characters.md)). Max HP lost this way is gone for good: heals
  stop at the new max, and HP above it drops to it.
- It isn't damage, so shields don't stop it, and it isn't an effect, so nothing
  can cleanse or block it.
- Everyone loses it at the same moment, once the end-of-round triggers (poison,
  regen, timed effects running out) are done.

A character whose max HP reaches 0 faints, and like any character at 0 max HP
its corpse is removed at once and it can't come back
([fainting.md](fainting.md)). Every character starts at 100 max HP, so no game
lasts past round 20; the totem, at 20, lasts until round 12 at most.

## How a game ends

- A team loses once none of its characters are standing. Minions don't count
  ([summons.md](summons.md)).
- If both teams' last characters fall at the same moment, to fatigue or
  anything else, the game is a **draw**.
- A game still going after 100 rounds is also a draw. Fatigue makes this
  unreachable; it stays as a safety net.
