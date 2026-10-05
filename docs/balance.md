# Balance checker

A tool that plays many games between AIs and reports how strong characters,
archetypes, team compositions and the AIs themselves are. It is built on
`play_match` (src/game/game.cx), which plays any two teams with any two agents.

## Ratings

Everything rated gets an **Elo** rating, starting at 1000, alongside its plain
win rate and game count (a rating from 5 games means little). Each pool is held
at an **average of 1000**: a game moves both sides by the same total, so the
average stays put on its own, and after every game the pool is re-centred in
case something else pulled it off (a character added to a pool whose ratings
have already spread out, say). A rating above 1000 is stronger than average,
below is weaker, and the gaps between ratings are what matter.

Battles are 4 against 4, so Elo is applied team-style:

1. A side's rating is the average of the ratings of what is being rated on it
   (its 4 characters, say).
2. The usual Elo expected score is worked out from the two sides' ratings.
3. Every member of a side moves by the same amount: K × (result − expected),
   with K = 4. K is small because a member's own rating is only a quarter
   of its side's, so the pull back towards its true rating is weak: at K = 32
   every character's rating swung about ±160 around its average over a 400k-game
   run (Cleric read 631 at a 49.4% win rate); swings scale with √K. The flip
   side is that a fresh file takes a few thousand games to spread out.

## What gets rated

| Pool | Rated thing | Notes |
|------|-------------|-------|
| Characters | each one in the roster | the main balance number |
| Archetypes | Warrior, Rogue, Tank, Mage, Support | a team of 2 Mages counts Mage twice |
| Compositions | a team's archetype mix, e.g. "Mage, Support, Tank, Warrior" | win rate is more useful than Elo here, since there are many mixes |
| Pairs | two characters on the same team, e.g. "Fairy + Warlock" | a score like the compositions', for synergy ([below](#character-pairs)) |
| Agents | Random, Greedy, Tactician, and every AI added later | shows whether a new AI beats the old ones, and by how much |
| Sides | home and away | how much Fast/Slow-phase priority is worth |

## Keeping the numbers fair

Two things can make a number lie: the AIs playing it, and the side it was on.
Games are played in **blocks of 4** that cancel both out:

1. Two random teams, X and Y, and two AIs, A and B, drawn from the AIs allowed
   for the run (they may be the same one).
2. A plays X against B with Y, and A plays Y against B with X, each twice with
   home and away swapped.

Each team is played by each AI equally often, and each AI plays each team from
each side. So one block rates the characters, archetypes, mixes and sides (AI
skill cancels out) and the AIs (team strength and home priority cancel out)
at the same time. AIs are only rated in games between two different AIs.

Allowing only the strongest AI gives the cleanest character numbers; allowing
several also rates the AIs against each other.

## Running it

```
cx run src/balance.cx -- [--games N] [--ai Greedy,Random] [--version V] [--file PATH] [--jobs N] [quiet]
cx run src/balance.cx -- --compare A B
```

| Option | Default | Meaning |
|--------|---------|---------|
| `--games N` | 100 | games to play this run, rounded up to whole blocks of 4; `0` just prints the report from the file; `forever` plays until cancelled (see [Running overnight](#running-overnight)) |
| `--ai LIST` | Greedy | the AIs that may be drawn for a block |
| `--version V` | `balance_version()` in tuning.cx | the [balance version](#versions) whose totals to add to or report |
| `--file PATH` | balance-data/*version*/balance.json | where the running totals live; the folder is made if it isn't there |
| `--compare A B` | | print how versions A and B differ and write the compare page ([Versions](#versions)) |
| `--jobs N` | 1 | play in N worker processes at once; about 4 per 4 cores is a good start |
| `quiet` | off | leave out the per-game lines |

The AIs, one file each in `src/agents/` (shared scoring in `scoring.cx`):

| AI | How it plays |
|----|--------------|
| Random | any usable move on any legal target |
| Greedy | the move that scores best right now: damage, knockouts, healing, a flat value for each buff or debuff |
| Tactician | like Greedy, but a buff or debuff that changes attack or defense is worth how much it changes the damage each side could deal next turn, for as long as it lasts. It knows every character's moves, so it energizes the hitter still to act this round, and gives nothing for boosting an ally that can't attack |
| Guardian | like Tactician, but it forecasts the foes: each foe still to act makes its Greedy pick, and protection (Invincible, shields, barriers, taunts, potions, speed changes) is worth the danger it takes off its side. So Fairy Ring goes on the ally about to be knocked out, a taunt goes up when a frail ally is about to be focused, and a heal that lifts an ally out of reach gets a bonus. A knockout is worth the damage that foe would go on to deal ([guardian.md](guardian.md)) |

All of them draft at random, all but Random without repeating an archetype.

### Generations

Each AI is a generation: it plays like the one before it plus one new idea,
and lives in its own file. A better way to play goes into a **new** AI, not
into an old one, so the old ones stay as fixed benchmarks and the balance
checker can show whether each generation really beats the last
(`--ai Tactician,Guardian`). Random (0), Greedy (1), Tactician (2),
Guardian (3). Guardian replaced the Strategist, which counted what a
cooldown costs (waste × cooldown ÷ 4) and didn't beat the Tactician by
enough to keep.

The totals are read from the file at the start and saved after every 4 games,
so runs add up (100 games, then another 100) and an interrupted run keeps what
it played (all but the last few games). Each save writes a temporary file
and renames it over the real one, so cancelling mid-save can't leave a
half-written file. A balance change starts a new file of its own by bumping
the [version](#versions); delete a file only to throw a version's games away.
The files are listed in `.gitignore`.

Progress (a line per game and standings every 20 games) is printed as it
happens, then the final report of the totals.

### Report page

Everything the checker writes goes in `balance-data/<version>/` (gitignored),
next to the totals file. Each run writes a **report page** there (`balance.json`
gives `balance.html`), and every save writes the totals beside it as
`balance.data.js`, which the page loads (all in `.gitignore`). Open the page
in a browser to look at the totals without running anything. During a run a
refresh shows the latest; the header says when the totals were last saved,
and **Auto-refresh** reloads every 10 seconds (keeping your place on the
page) to follow a run live. `--games 0` writes both for the file as it is,
without playing.

- One table per thing rated (characters, archetypes, AIs, home and away,
  character pairs), each with a bar showing how far above or below the
  middle (1000 Elo, 50%, a score of 0) each row is.
- **Game length** and **Short and long games** show how long games last and
  how each character's games end, short or long ([below](#game-length)).
- **Counters** shows how each character does against each one on the other
  team ([below](#counters)).
- **Characters by AI** shows each character's win rate under each AI that
  played it ([below](#characters-by-ai)).
- The characters table has **Off 50**, how far each win rate is from 50%
  either way, to sort from most to least balanced, and each character's
  **Archetype** and **Speed** tier: sorting by either groups the characters,
  best win rate first within each, to see whether a class fills its niche.
- The characters table also shows each character's base attack, defense and
  attack × defense, to sort by and see how stats line up with win rates.
  They're the stats the version was played with: the data script carries
  them, with the archetype and tier, as `window.BALANCE_STATS`.
- Click a heading to sort by that column, again to reverse. A filter by name
  and a minimum number of games apply to every table; tabs show one table
  or all of them. The page remembers these in the browser.

The page is a copy of `src/balance/report.html`; the data script
(`src/balance/page.cx`) is `window.BALANCE_DATA = {...}`, loaded with a
`<script src>`, which works from `file://`, so the page needs no server or
internet. The data is a file of its own because splicing it into the page was
slow in Cronyx (about 5 s a save for a 46K-character ledger). Each table and
column is one entry in the page's `TABLES` list, so new views are added there.

### Versions

Each balance is a **version**, `balance_version()` at the top of
`src/content/tuning.cx` (v0.0.1 is the first). Bump it in the same edit as
any balance change, in `tuning.cx` or `roster.cx`, and tag the commit with it
(`git tag v0.0.2`), so `git diff v0.0.1 v0.0.2 -- src/content` shows what
changed in the numbers.

The checker keeps each version's games apart: the totals, page and logs for
v0.0.1 are in `balance-data/v0.0.1/`, and a run plays and saves under the
version in `tuning.cx`. So a run after a patch can't add to the old
version's totals by mistake. `--version V` reports on (or adds to) another
version, e.g. `--games 0 --version v0.0.1`.

**Comparing.** `--compare v0.0.1 v0.0.2` prints each character's, archetype's
and AI's win rate in both versions, biggest change first, each marked:

| Mark | Meaning |
|------|---------|
| real | the change is 3+ standard errors: almost surely not luck |
| likely | 2+ standard errors |
| noise | less: could be luck |

Every run also writes **`balance-data/compare.html`**, the same comparison
for every table on the report page (pairs compare scores),
with menus for the two versions (the two newest by default, or
`compare.html?a=v0.0.1&b=v0.0.2`) and a button to hide the noise. Each
version's report page links to it. It reads each version's data script, so
during a run a refresh shows the latest.

The standard error is widened for the blocks: games come in blocks of 4 with
the same two teams, so their results move together, and over v0.0.1's 409k
games a character's win rate varied 2.5-2.8 times as much as independent
games would make it. The checker counts it as 3 (`block_spread` in
`src/balance/versions.cx`). Two runs of the same tuning should show about 1
row in 20 as likely and almost none as real.

How many games a new version needs: each game has 8 of the 23 characters, so
a version with N games has about 0.35 N per character, and against v0.0.1's
140k per character a change is real once it's 3 × √(3 × 0.25 / 0.35 N), in
win rate. About 23,000 games show a 3-point change as real, 110,000 a
1.5-point one; a change of 5+ points shows up within 8,000.

### Logs

Every run also appends to two logs next to the totals file, as JSON lines (one
object per line), for looking at a long run afterwards (`src/balance/logs.cx`):

- **`balance.games.jsonl`**: every game, as recorded: `time` (Unix seconds),
  `home_ai`, `away_ai`, `home` and `away` (character names), `winner` (0 home,
  1 away, -1 draw), `rounds`, `secs` (how long the game took to play).
- **`balance.timeline.jsonl`**: a `start` line when a run (or a batch of a
  `forever` run) begins, with its arguments and the games in the totals; then
  a `snapshot` every 100 games and at the end of each run, with:
  - `games` (in the totals), `this_run`, `elapsed_s`;
  - `interval`: the games since the last snapshot, `wall_s`,
    `games_per_min`, `avg_game_s`, `avg_rounds`, `draws`, `home_win_pct`;
  - `main_rss_mb` (the main process's memory), `last_save_ms`,
    `workers_started`;
  - `characters`, `archetypes`, `agents`: every rating as `[elo, games, wins]`.

Game lines are appended at each save, so after a cancel they match the
totals. The logs only grow; delete them with the totals to start over.

### Running overnight

```
cx run src/balance.cx -- --games forever --ai Greedy,Tactician --jobs 8 quiet
```

plays until you press Ctrl+C. It runs the checker again and again, 1000 games
at a time (`batch_games` in `src/balance.cx`), with the same options; each
batch is a run of its own that loads the totals, adds to them, saves, and
exits, printing one line instead of the full report. `quiet` keeps the
terminal to the standings every 20 games and a line per batch; the page and
the logs have the rest.

It runs in batches because a long-lived Cronyx process keeps the memory it
had before each file write it waits on (CronyxLang#151): one endless run's
main process grew by about 150 KB a game. A batch frees it all when it exits
(about 100 MB at most), and the process running the batches only waits.

Saves can lag a few seconds behind the games (CronyxLang#150: a file write
waits for a worker's next line), so cancelling loses at most the last few
seconds of games. Everything saved is consistent: the totals, the page data
and the game log are each written whole or not at all.

### Jobs

The games are played by **worker processes**, each the checker itself run as
`cx run src/balance.cx -- --worker --games K --ai LIST`, while the main
process only records them. A worker plays its blocks and prints one line per
game (`src/balance/jobs.cx`); it never reads or writes the totals file. The
main process reads every worker at once and records each game as it arrives,
so the totals still live in one place and Elo is still applied one game at a
time; only the order games are recorded in changes. Run it from the project's
root, with `cx` on `PATH`.

`--jobs N` runs N **lanes** at once, sharing the games out in whole blocks.
Each lane runs workers one after another, each playing at most 1000 games
(`worker_games` in `src/balance.cx`), so a normal run is one worker per lane.

Before cx 0.0.27, a Cronyx process kept every finished battle in memory
(about 2 MB a game; CronyxLang#144 and #147), and the growing heap slowed it
down: one process playing 300 games ran each game 5× slower by the end. The
cap was 24 games then, so no worker got big. With 0.0.27 a worker's memory
stays flat (about 30 MB over 400 games), and one process playing 300 games
was no slower than fifteen 20-game workers.

## Team composition

Teams are picked fully at random from the roster, with any mix of archetypes
allowed, four Supports included. The composition numbers can only show that a
mix is bad if that mix gets played.

A composition's raw win rate mixes up two things: how strong its characters
are, and how well they work together. The **composition score** separates
them:

1. Before each game, the character ratings give each team an expected score:
   the usual Elo expectation from the average rating of its 4 characters.
2. After the game, each team's composition is credited with
   actual − expected (actual is 1 for a win, 0 for a loss).
3. A composition's score is the average of those credits.

| Score | Meaning |
|-------|---------|
| about 0 | the mix does as well as its characters predict |
| positive | synergy: it wins more than its characters explain |
| negative | a bad fit: it loses more than its characters explain |

The score is reported in percentage points ("+6% above expected") and can be
converted to Elo points.

A mix being bad, like four Supports, is a design goal rather than part of the
formula: the checker shows whether the goal is met. Four Supports should come
out clearly negative; if it doesn't, Supports are too strong together.

There are 70 possible archetype mixes, so most take many games to settle. A
coarser view fills up faster: win rate and score by how many of each
archetype a team has (0 to 4 Supports, 0 to 4 Mages, and so on).

The checker still counts both (`mixes` and `counts` in the totals file),
but neither the report page, the compare page nor the printed report shows
them any more: they turned out to matter less for balancing than the
character tables. Showing them again is an entry in each page's `TABLES`.

## Character pairs

Teams are random, so a character built for a combo is mostly measured with
partners that can't use it. The Fairy is worth far more protecting a Warlock
so it gets its nuke off than on a random team, and the Cryomancer and Frost
Giant chill for each other; their win rates average that away.

The **pair score** shows it, the same way the composition score does for
archetype mixes:

1. Before each game, each team's expected score comes from its characters'
   ratings, as for compositions.
2. After it, every pair of characters on a team (6 per team) is credited
   with actual − expected.
3. A pair's score is the average of its credits: positive means the two win
   more together than their ratings explain.

With 23 characters there are 253 pairs, and each game feeds 12 of them, so a
pair needs many games to settle: each turns up in about 1 team in 42, and
telling a real +8% from noise takes 100+ games together, so 4,000-5,000 games
in all. The printed report lists the best and worst 10 pairs with 30+ games
(`pair_min_games`, `pair_ends` in src/balance.cx); the report page has every
pair, with Min games to hide the thin ones. Pairs are kept in the totals file
under `pairs`; a file from before pairs existed starts with none.

Balancing combo characters means two numbers: the character's own
rating (how it does on any team) and its best pair scores (how it does on the
team it was built for). A character that's weak alone but has strong pairs
is working as designed (see
[characters.md](characters.md#strong-strengths-big-weaknesses)).

## Characters by AI

Some characters are only as good as the AI playing them: the Fairy's Ring
and the Bard's tempo need an AI that sees who is about to be hit
([guardian.md](guardian.md)). So the checker also counts each character
under each AI that played it, as `Fairy · Guardian`, kept in the totals file
under `by_ai` (a file from before starts with none).

The report page's **Characters by AI** table has a row per character: its
win % under each AI, and its **edge** there, that win % less the AI's own
win % over every character it played. The edge takes out how good the AI is
overall, so a character with a much higher edge under one AI is one that AI
plays better. **Gap** is the best edge less the worst, and **Suits** names
the AI with the best.

A character under one AI turns up in about 0.35 × (that AI's share of
sides) of games, and like any win rate it needs thousands of games to
settle: with two AIs, 10,000 games give each character about 1,700 under
each, enough to show a gap of about 9 points as real; a 5-point gap takes
about 30,000 games.

## Game length

Some characters are built for one end of a game: the Bard snowballs early
and should fade, the Cleric and the Grim Reaper grind. So the checker counts
games by how many rounds they lasted, kept in the totals file under
`lengths` (a file from before starts with none):

- `All @ 7` counts every game of 7 rounds (its wins are the home side's,
  kept for later).
- `Fairy @ 7` counts the Fairy's games of 7 rounds, wins and losses.

The report page shows two tables from them:

- **Game length**: the number of games, the mean, median, shortest and
  longest, the middle half (25th to 75th percentile) and the middle 80%,
  over a chart of how many games lasted each number of rounds with the
  median, the mean and the Short ≤ line marked.
- **Short and long games**: each character's games split four ways, as
  shares of all its games that add up to 100: **won short**, **lost short**,
  **won long**, **lost long**. A character built to protect early (the
  Fairy) should rarely lose short; one built to snowball (the Bard) should
  win short. **Short ≤** in the toolbar sets where short games end; empty,
  it's the median length. The lengths are kept round by round, so the line
  can move without rerunning anything.

## Counters

Pairs show synergy between teammates; **counters** show the same thing
across the table: how a character does against a particular foe. Before
each game the character ratings give each team an expected score, as for
pairs; after it, every character on one team is credited against every
character on the other (16 matchups a game) with actual − expected from its
side. A matchup's score is the average: positive means the first character
does better against the second than the ratings explain, so it counters it.

Each matchup is kept once, in alphabetical order and from the first
character's side, under `counters` in the totals file ("Assassin vs Fairy";
the Fairy's view is the same numbers turned round). A character against
itself, when both teams drafted it, isn't counted. The report page's
**Counters** table shows every matchup both ways round, and its filter
looks at the first name, so filtering for "Fairy" lists the Fairy's
matchups from its side. The printed report lists the best and worst 10 with
30+ games, and the compare page compares their scores.

With 253 matchups and 16 credited a game, a matchup turns up in about 1 game
in 16, so 100+ games in a matchup takes 1,600+ games, and telling a real
+8% from noise takes several thousand.
