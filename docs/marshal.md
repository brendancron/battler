# Marshal (generation 4)

The fourth AI generation ([balance.md](balance.md#generations)),
`src/agents/marshal.cx`.

**Its one new idea: it looks ahead.** Every earlier AI scores a move by what
it does on its own, by rules (damage, heals, what an effect is worth).
The Marshal tries each move and target on a copy of the battle, plays the
rest of the round out, and keeps the one that leaves its side best placed.

## How it chooses

For each usable move and each target it allows:

1. **Copy the battle** (`copy_battle`): every character, effect and
   cooldown, with links (Spirit Link, a totem's summoner, a bleed's source,
   kept as an effect's `partner`) pointing at the copy's characters. The
   copy records nothing for the screen. Playing on it leaves the real battle
   as it was.
2. **Make the move** on the copy, as the game would: turn started, the move,
   turn ended, cooldowns counted.
3. **Play out the rest of the round**: from the user's phase on, everyone
   still to act, on both sides, makes its Greedy pick (lost turns are lost),
   then the round ends: poison, regen, shield decay, effects wearing off.
   Fatigue isn't applied, since the copy doesn't know the round number.
4. **Score the result** (`standing`): each standing character is worth its
   HP, half its shield and 60 for being in the fight, a minion half its HP
   and 10. The score is the Marshal's side less the foes', with 500 more for
   a win (500 less for a loss).

It takes the best score, with a little noise (up to 4) to break ties.

## Why this and not more rules

Two rule-based attempts came first and tied the Guardian (50% over about
1,000 games each):

- Valuing each character by everything it brings (heals, shields, auras),
  not just its best hit, so it protects and hunts healers.
- Adding focus fire (a hit that sets up an ally's knockout this round) and
  seeing barriers and parries (a direct hit into one does nothing).

Playing the round out covers all of that and more without a rule for each:
a knockout that stops a big hitter acting, a shield that keeps the ally
standing, a hit into a parry that only draws a riposte, Healing Rain before
the burst, all show up in where the round ends.

## Results

`--ai Guardian,Marshal`, 2,000 games: the Marshal won **64.3% of 1,078**
games against the Guardian (z = 5.4). Characters it plays best compared with
the Guardian: Fairy, Barbarian, Vampire, Ranger, Knight; it does a little
worse with Jester, Ninja, Witch and Paladin (each about 330 games, so most
of this is noise).

## Cost

Each choice plays a whole round out, so a turn costs one round of Greedy
picks per option: a game takes about 2 seconds against the Guardian's 0.9.

## Testing

- Playing ahead leaves the battle as it was: no damage, no log lines, no
  cooldowns.
- A copy's links point at its own characters.
- It knocks out the foe that would hit hardest this round, and takes a
  knockout over spreading damage.

## Placeholders

- 60 for a standing character, half a shield, a minion at half its HP and
  10, 500 for a win.
- The rest of the round is played by Greedy, the cheapest AI; a smarter
  continuation (or a second round) is the obvious next step.
