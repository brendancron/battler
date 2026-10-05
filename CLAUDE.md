# Battler

A 4-v-4 turn-based battler written in [Cronyx](../CronyxLang), with its own
characters, speed tiers, targeting, effects and draft. The design lives in `docs/`; the code follows it.

## Working here

- **Never change CronyxLang (`../CronyxLang`) from this repo.** When a Cronyx
  compiler or runtime bug gets in the way, work around it in this project and
  tell the user. If the bug is clear, reproduce it in a scratch directory and
  file an issue at https://github.com/brendancron/CronyxLang/issues (check for
  an existing one first). Compiler fixes are handled separately.
- **Ask one question per message.** Keep open design questions in
  `docs/questions.md` ("Open", or "Later" for ones set aside) and ask them one
  at a time. Once answered, write the answer into the doc it came from and
  remove the question.
- **Pick starting numbers yourself; balance changes need the user's OK.**
  When designing something new, its move power, heal amounts, durations,
  cooldowns and stats are placeholders: choose something reasonable, mark it
  as a placeholder in the docs, and ask about mechanics and intent instead.
  Changing a number that's already in the game (a nerf, a buff, a retune) is
  a balance change: propose it, with the evidence (balance checker results,
  the maths), and wait for the user to explicitly OK it before editing
  `tuning.cx` or `roster.cx`. The aim is strong strengths and big weaknesses
  ([characters.md](docs/characters.md#strong-strengths-big-weaknesses)), so a
  proposal sharpens what a character is missing rather than evening it out.
  Every move and passive number lives in `src/content/tuning.cx` (characters'
  stats and basic attacks in `roster.cx`); code reads it from there, never as
  a literal.
- **Test mechanics, not values.** A test must survive retuning: it checks what
  a move or effect does in terms of its numbers, never the numbers
  themselves. Read amounts from `tuning.cx` and work out the expected result
  (`damage(...)` for hits, `smaller(100, hp + heal)` for heals), or build the
  effect with a number the test chooses (`poison(5)`, `shield(30)`). Set up
  HP so caps don't depend on tuning: start low before a heal, use `sturdy()`
  (1000 HP) before big hits. For the AI, test scoring rules ("a heal is worth
  the HP it restores", "worth more nearer the line") rather than which of two
  tuned moves wins. To check a change, multiply or shift every number in
  `tuning.cx` and run `cx test`; nothing should fail.
- **New AI ideas make a new AI.** Don't make an existing agent smarter: write
  the next generation in its own file in `src/agents/`, building on the last
  one, and check with the balance checker that it beats it
  (`docs/balance.md#generations`).
- **Design in `docs/` before building.** When the user describes a mechanic or
  character, write it into the matching doc (`characters.md`, `effects.md`,
  `speed.md`, `targeting.md`, `events.md`, `moves.md`, `draft.md`, `fatigue.md`,
  `balance.md`, ...). Build it when asked, with tests.

## Commands

```
cx run                                       # play: coin flip, draft, battle vs the AI
cx test                                      # the test suite (tests/*.cx)
cx run src/balance.cx -- --games 200 --ai Greedy,Tactician --jobs 4   # balance checker
cx run src/balance.cx -- --games forever --ai Greedy,Tactician --jobs 8 quiet   # until Ctrl+C
cx run src/balance.cx -- --compare v0.0.1 v0.0.2   # what a balance patch changed
```

The balance checker keeps everything it writes in `balance-data/`
(gitignored), one folder per balance version: it adds to
`balance-data/<version>/balance.json` on every run, the version being
`balance_version()` in `tuning.cx`. **Bump the version with every balance
change** and tag the commit (`git tag v0.0.2`); `--compare` and
`balance-data/compare.html` show what changed (`docs/balance.md#versions`).
Each run writes `balance.html`, a sortable report to open
in a browser, and keeps `balance.data.js` (its data) and two logs
(`balance.games.jsonl`, `balance.timeline.jsonl`) up to date; `--games 0`
just writes the page. Options are in `docs/balance.md`.

`cx` is the Homebrew release (0.0.27+). `cxdev` builds `../CronyxLang` from
source; it's only needed for a fix that isn't released yet.

At a terminal the game waits for a key between steps of a turn; with piped
input it prints a plain transcript, which is handy for scripted runs.

## Layout

Entry points sit at the top of `src/`; everything else is in a folder.
Imports are relative to the importing file (`"../core/battle"`); tests use
`"battler/core/battle"`.

- `src/main.cx`: play a game. `src/balance.cx`: the balance checker.
- `src/core/`: the rules engine.
  - `creature.cx`: characters, effects, events, actions (the core types).
  - `battle.cx`: sides and slots.
  - `engine.cx`: the trigger engine (depth-first resolution, provenance,
    intercepts, shields).
  - `move.cx`, `target.cx`, `order.cx`: moves, targeting, speed-tier turn
    order.
  - `maths.cx`.
- `src/content/`: `effects.cx`, `moves.cx`, `roster.cx`. These are the
  effect types, the moves, and each character's stats, passive and moves.
  `tuning.cx` has every move and passive number, and the fatigue numbers.
- `src/game/`:
  - `game.cx`: `run_draft` and `play_match`.
  - `draft.cx`: draft rules.
  - `fatigue.cx`: max HP lost each round once fatigue sets in (`docs/fatigue.md`).
- `src/agents/`: one file per agent.
  - `agent.cx`: the `Agent` trait, `Turn`/`Plan`, and `ai_named`.
  - `console.cx`, and the AIs by generation: `random.cx`, `greedy.cx`,
    `tactician.cx`, `guardian.cx`.
  - `scoring.cx`: move scoring shared by Greedy, Tactician and Guardian.
  - A new AI gets its own file and an entry in `ai_names`/`ai_named`.
- `src/ui/`: `display.cx` and `term.cx` (the battle screen), `prompt.cx`
  (numbered console questions).
- `src/balance/`:
  - `ratings.cx`: Elo ratings.
  - `jobs.cx`: the result lines the `--jobs` workers send back.
  - `report.html`, `page.cx`: the report page and its data script.
  - `logs.cx`: the game log and the timeline (JSON lines).
  - `versions.cx`, `compare.html`: balance versions and the compare page.

## Cronyx notes

- **Methods go in `impl` blocks** (`impl Battle { fn at(self, slot: Slot) ... }`).
  They come with their type, so don't import them by name. (Before 0.0.25,
  CronyxLang#134 forced free functions with an explicit receiver.)
- **Trait objects can be built anywhere** since 0.0.25 (CronyxLang#135), so
  `agent.cx` and `game.cx` are covered by tests (`tests/agents.cx`).
- **A function can't read a top-level variable of an imported module**
  (CronyxLang#143); one in the entry file works. Pass values in, or use a
  function like `fn width(): int { return 60; }`.
- **Memory before a suspending call is kept** (CronyxLang#151): a function
  that allocates and then waits on real I/O (a file write) keeps what it
  allocated. A long-lived process that writes files grows; restart it now and
  then, as `--games forever` does with batches.
- **A file write can wait on another task's pipe read** (CronyxLang#150), so
  saves lag while the balance checker reads its workers.
- **`effect`, `run` and `code` are keywords**; name variables `fx` and `exit_code`, functions
  `play_all`, etc.
- **`print` is buffered.** Call `flush()` (from `std/io/Console`) after a line
  that has to show up straight away, as `progress()` in `src/balance.cx` and
  the `--jobs` workers do (CronyxLang#138).
