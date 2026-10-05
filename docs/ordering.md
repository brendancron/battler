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
| 1 | Planner | setup rules: what each move sets up, and what cashes it in |
| 1 | Scholar | what the game logs show about who should go before whom |

Generation 0 keeps today's behaviour, so it's the benchmark. Planner and
Scholar are both generation 1, built side by side as two answers to the same
question, and rated against each other and against Keeper with
`--order Keeper,Planner,Scholar`. Whichever wins is what generation 2 builds on.

Both score all 24 orders of their team and keep the best, so they differ only
in how they score an order.

### Planner: setup rules

An order scores points for each pair of teammates where one sets up and the
other cashes in, with the setter in the lower slot. Only pairs that act in
the same phase count: across tiers, slots don't change who goes first
([speed.md](speed.md)). What counts as a setup and a cash-in is read from
the moves, not from character names, for example:

| Sets up | Cashed in by |
|---|---|
| Area damage (Thunderstorm, Brimstone, Writhing Depths) | moves that do more to wounded foes (Predator, Execution) |
| Chill (Avalanche, Cold Aura) | moves that freeze a chilled foe (Blizzard) |
| Shields, buffs and cleanses on allies | the allies' big attacks |
| Taunts | frail damage dealers, which act once the taunt is up |

The rules and their weights are placeholders, to be tuned by how Planner does
against Keeper.

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

1. A block draws two order AIs, P and Q, as well as the two teams and the two
   move AIs.
2. It plays the usual 4 games with team X ordered by P and team Y by Q, then
   the same 4 with X ordered by Q and Y by P: **8 games**.
3. If P and Q are the same order AI, the second half would repeat the first,
   so the block is the usual 4 games.

Each team is ordered by each order AI equally often, so team strength cancels
out, and order AIs are rated, like move AIs, only in games between two
different ones. Each game asks the order AIs afresh, so an order AI with
some randomness in it is sampled in every game.
