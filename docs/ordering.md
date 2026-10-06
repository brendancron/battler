# Slot ordering

Once both teams are known, each team is arranged into slots 0 to 3 by an
**order AI**, a separate kind of AI from the ones that choose moves. The
order it picks is fixed for the whole battle.

## Why order matters

Slots matter in a few places:

- **Turn order within a tier.** Within one team and one tier, characters act
  in slot order, slot 0 first ([speed.md](speed.md)). Combos that need one
  character to act before another only work if the first one has the lower
  slot.
- **Picking among the fallen.** Raise Dead and Puppeteer take the first
  fallen character in slot order ([summons.md](summons.md)).

Example: the Stormbringer and the Assassin are both Fast. The Stormbringer's
area hits bring the foes down, and the Assassin's Predator then deals 50%
more to anyone below half HP. That only works in the same round if the
Stormbringer is in the lower slot. With teams in a random order, the pair is
set up the wrong way round half the time, and its win rate averages that away.

## How a battle is set up

1. The two teams are made: random teams in the balance checker, a draft in
   `cx run` ([draft.md](draft.md)).
2. Each team's order AI arranges its own team into slots.
3. The battle is played by the move AIs, with the slots fixed.

An order AI sees its own team, the foe team, and whether it is home or away.
It doesn't see the foe's order: both teams are ordered at the same time,
blind, so neither can arrange itself to counter the other's slots.

Ordering is a separate AI from move choice on purpose, so the two can get
better independently. A move AI never reorders a team, and an order AI never
chooses a move. Each kind has its own generations, in the same way as the
move AIs ([balance.md](balance.md#generations)).

## Order AIs

Names are placeholders.

| Generation | Name | Orders by |
|---|---|---|
| 0 | Keeper | the order the team arrived in (pick order, or the random team's order), which is how slots work today |
| 0 | Random | any order, picked at random afresh every game |
| 1 | Planner | setup rules: what each move sets up, and what cashes it in |
| 1 | Scholar | what the game logs show about who should go before whom |

Generation 0 keeps today's behaviour, so it's the benchmark. Random is a
second benchmark: no thought at all. In the balance checker, teams are random
already, so the Keeper's order is a random one too, kept for the whole block;
Random reshuffles every game, and the two should rate about even. In `cx run`
they differ, as the Keeper keeps the pick order. Planner and
Scholar are both generation 1, built side by side as two answers to the same
question, and rated against each other and against Keeper with
`--order Keeper,Random,Planner,Scholar`. Whichever wins is what generation 2 builds on.

Both score all 24 orders of their team and keep the best, so they differ only
in how they score an order.

### Planner: setup rules

An order scores points for each pair of teammates where one sets up the
other, with the setter in the lower slot. Only pairs in the same tier count:
across tiers, slots don't change who goes first ([speed.md](speed.md)).

What sets up what isn't written down anywhere, by name or by move. The
Planner finds it out by trying the moves in a **sandbox**: a copy of the
battle with fresh characters, its team home against the real foe team, each
foe given 1000 max HP so no probe knocks one out. A move is measured by what
it does to the foes: the HP they lose, plus 25 for each foe it makes skip its
turn. Two rules come out of it:

| Rule | Sets up | Cashed in by | Worth |
|---|---|---|---|
| **Combo** | a move of A, used first on the first foe (or on B, for a move on an ally) | a move of B that then does at least 10% (and 3) more than B's best on its own | 2 |
| **Finish** | A has a move that takes HP off two foes or more | B has a move that does at least 20% more to a foe at 40% HP than at full | 2 |

The combo rule finds a second chill freezing a chilled foe (the Frost Giant
before the Cryomancer, when they share a tier), a buff before the ally it
boosts (the Bard), and a debuff that makes a foe take more (the Monk, the
Ranger's Hunter's Mark). The finish rule is there because one area hit
rarely takes a foe below the Predator's line on its own: the Stormbringer
before the Assassin.

The Planner scores all 24 orders and keeps the best; on a tie the order the
team came in wins, so a team with nothing to set up is left as it is.
Probing takes a few hundred milliseconds a team, so the Planner remembers its
last 8 answers (by both teams and the side), which covers a balance block.

Not seen yet: the Execution's chain (it moves on after a knockout, and
sandbox foes don't get knocked out), and anything that's worth acting early
rather than before a teammate (a taunt or a shield going up before the foes
hit). Those are ideas for a later generation.

Every number here (the worths, 25 per skipped turn, 10%, 3, 20%, 40%) is a
placeholder, in `src/agents/planner.cx`, to be tuned by how the Planner does
against the Keeper.

### Scholar: learned from the logs

In the balance checker, Keeper's order is the random team's order, so every
game it plays is a random experiment in slot order. `balance.games.jsonl`
lists each team in slot order, so it already holds the data:

1. For each game and each pair of teammates A and B, A was either before B
   or after it. Credit the pair (A before B) with the team's actual result
   minus its expected result, as the pair score does
   ([balance.md](balance.md#character-pairs)).
2. A pair's **before score** is the average credit when A was before B,
   minus the average when B was before A.
3. Scholar scores an order as the sum of the before scores of every pair it
   puts that way round, and keeps the best.

Scholar learns from Keeper's games only (so the orders it learns from are
random), and the table is rebuilt from the logs for each balance version,
since a balance change can change which order is best.

## In a game

In `cx run`, after the draft, the player arranges their own team: the
console asks which character goes in each slot, one numbered question per
slot, as in the draft, with the foe's team on screen. The
console is the player's order AI, as it is their move AI. The foe's team is
arranged by an order AI at the same time, blind, so the player doesn't see
the foe's order until the battle starts.

## In the balance checker

Order AIs have their own list, `--order LIST`, beside `--ai LIST`, and their
own ratings ([balance.md](balance.md#keeping-the-numbers-fair)):

1. With more than one order AI in the list, a drawing draws two different
   ones, P and Q, as well as the two teams and the two move AIs.
2. Its block is the usual 4 games with team X ordered by P and team Y by Q,
   then the same 4 with X ordered by Q and Y by P: **8 games**.
3. With one order AI in the list (the default, `Keeper`), both teams use it
   and the block is the usual 4 games.
4. A drawing plays `--repeat` games of its block, shuffled (default 1;
   [balance.md](balance.md#repeats-how-many-games-a-drawing-plays)). To
   compare order AIs, play whole blocks: `--repeat 8`.

With whole blocks, each team is ordered by each order AI equally often, so
team strength cancels out, and order AIs are rated, like move AIs, only in
games between two different ones. Each game asks the order AIs afresh, so an order AI with
some randomness in it is sampled in every game.

Each game in the log (`balance.games.jsonl`) names its order AIs
(`home_order`, `away_order`) and lists each team in slot order, as arranged.
A worker's game line carries them too. A game with a Planner team shows as
`Guardian/Planner` in the progress lines.
