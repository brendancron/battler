# Web UI

Play in a browser instead of the terminal. The browser is an **agent**: the
game runs in Cronyx on the server, exactly as `cx run` does. It asks the
browser what to do the way it asks `ConsoleAgent`, and checks the answer
before using it. The page only draws what it's sent and sends back choices
from the options it was given. No game rules live in JavaScript.

This needs `std/net/WebSocket` (CronyxLang#157), so it waits for the `cx`
release that includes it.

## Running it

```
cx run src/web.cx -- --port 8080 --ai Marshal
```

Then open `http://localhost:8080`. The server answers on one port:

- `/` serves the page, `src/web/page.html`. It's read again on each request,
  so an edit shows on reload.
- `/play` is the WebSocket. Each connection is **one game**: coin flip,
  draft, then the match against the `--ai` agent (Marshal by default). When
  the game ends, the page offers a new one by opening a new connection.

Two tabs are two independent games. `--port` and `--ai` are the only options
to start with.

## How it fits

```
         browser tab                         src/web.cx
   ┌──────────────────────┐   WebSocket   ┌───────────────────────────────┐
   │ page.html            │ ◄───────────► │ session (one task per game)   │
   │  draws frames        │   JSON text   │   run_draft / play_match      │
   │  answers asks        │               │   WebAgent ──┐   AI agent     │
   └──────────────────────┘               │   WebViewer ─┴─ the socket    │
                                          └───────────────────────────────┘
```

`std/net/WebSocket`'s `serve(listener, respond, session)` hands each
handshake to `session` as a task of its own. The session builds two seats,
a `WebAgent` for the player and the AI for the foe, and calls `run_draft`
and `play_match` as `main.cx` does. All of a game's sends and receives
happen in that one task, so there's no channel and no second task to
coordinate.

### WebAgent

`src/agents/web.cx`, next to `console.cx`. It holds the socket.

- **`choose_pick`:** sends a `pick` ask and waits for the answer.
- **`choose_move`:** sends a `move` ask, then a `target` ask for each target
  the move's shape needs. This mirrors `ConsoleAgent`'s `pick_action` and
  `pick_choice`, with `ask(...)` replaced by a question over the socket. The
  shape decides the questions, as at the console:
  - `ChainPicks` asks one bolt at a time from `next_in_chain`.
  - `FoePicks` may pick the same foe again.
  - Shapes with nothing to pick don't ask.
- **A closed socket counts as leaving.** If `receive` returns `None`, the
  agent returns `None` (or `-1` in the draft) and the game stops.

The `Agent` trait already declares `async` and `Throw<IoError>`, which is all
the socket calls need (`send` and `receive` don't need `Net`). So the trait
and the other agents don't change.

### Viewer: the display as a trait

The display is a concrete `Screen` today. `game.cx` calls `play`,
`begin_round`, `end_round` and `lose_turn` in `ui/display.cx`, which run the
engine and then print the steps. `run_draft` prints each pick itself. The
web needs the same moments sent over the socket, so the display becomes a
trait, as agents are:

```
trait Viewer {
    /** A draft pick was made. */
    fn picked(self, pick: int, side: int, name: string): <...> unit;
    /** What happened since `start`: the battle's frames from `from` on, titled, with `user` acting (side -1 for none). */
    fn steps(self, battle: Battle, title: string, user: Slot, start: List<List<Look>>, from: int): <...> unit;
    /** The match is over. */
    fn finished(self, outcome: Outcome): <...> unit;
}
```

- **`Screen` implements it** for the terminal, with what `steps` and the pick
  line already do. A silent `Screen` still draws nothing, so the balance
  checker is unaffected.
- **`WebViewer` implements it** by sending `picked`, `steps` and `over`
  messages.
- **The engine-driving half moves to `game.cx`:** `play`, `begin_round`,
  `end_round` and `lose_turn` (announce, `use_move`, `turn_over`, fatigue).
  They end with `viewer.steps(...)`. `play_match` and `run_draft` take a
  `Viewer` instead of a `Screen`.
- **Effects are declared on the trait, as with `Agent`:** every method lists
  everything any viewer needs (`Console` for the terminal; `async` and
  `Throw<IoError>` for the socket).

The player's `WebAgent` and the game's `WebViewer` share one socket, so the
page sees every turn, the foe's included.

## Messages

Everything is a JSON text message with a `type`. Slots are
`{"side": s, "index": i}`; side 0 is home.

### Server to page

| `type` | When | Fields |
|---|---|---|
| `hello` | First, once | `you` (your side), `home` (bool), `ai` (its name), `version` (`balance_version()`) |
| `ask` | The game needs a decision | `id`, `kind` (`pick`, `move` or `target`), and that kind's fields, below |
| `picked` | After each draft pick | `pick` (1–8), `side`, `name` |
| `steps` | After each turn, round start or round end that logged anything | `title`, `actor` (slot or `null`), `start` (looks before), `frames` (a list of `{line, looks}`) |
| `error` | An answer wasn't one of the options | `text`; the same ask is sent again |
| `over` | The match ended | `winner` (side, or -1), `rounds` |

`looks` is `Battle.looks()` as JSON, a list per side of
`{name, tier, hp, max, base, acted, corpse, effects}`. These are the same
snapshots the terminal steps through, so the page draws exactly what the
terminal shows. `effects` is the terminal's effect text for now.

**What each kind of ask carries:**

- **`pick`:** `pick` (1–8), `available`, `mine`, `theirs`. These are lists of
  fighters: `{name, archetype, tier, hp, attack, defense, passives, moves}`,
  where each move is `{name, cooldown, basic, shape}`.
- **`move`:** `title`, `actor`, `looks` (the field now), and `moves`. Each move
  is `{name, cooldown, basic, shape, why}`, where `why` is `why_not`'s reason
  it can't be used, or `""`.
- **`target`:** `prompt` (e.g. "Chain Lightning, bolt 2 of 3: which foe?"),
  `move`, and `options` (the slots that may be picked).

### Page to server

| `type` | Fields |
|---|---|
| `answer` | `id` (the ask's), `index` (into the ask's `available`, `moves` or `options`) |
| `back` | `id`. From a `target` ask, go back to the `move` ask, to choose another move |

An answer whose `id` isn't the open ask's is ignored. An index out of range,
or a move with a non-empty `why`, gets an `error` and the ask again. The
server never trusts the page; the game's own legality check still runs after.

## Pacing

The server never waits for the page. AI turns produce `steps` messages as
fast as Marshal decides. The page keeps a queue:

- It plays each `steps` message a frame at a time. Each frame is one log line
  with the field as it stood after that line, as in the terminal.
- It auto-advances every 700 ms (placeholder). A click skips to the next
  frame, and a "skip" button skips to the end of the queue.
- It shows an `ask` only once everything queued before it has played. You
  always choose from the field as it stands, never from one that's still
  animating.

## The page

`src/web/page.html` is one file with inline CSS and plain JavaScript, so
there's no build step, as with `balance/report.html`.

- **Draft:** a grid of the available fighters, each card showing archetype,
  speed tier, HP/Atk/Def, passive and moves. Both teams so far sit in
  columns at the sides. Click a card to pick.
- **Battle:**
  - The foe's four cards sit on top and yours below, as `bottom` puts you in
    the terminal.
  - Each card shows name, tier badge, HP bar and number, and the effects line.
    The bar marks max HP lost to fatigue, as the terminal's `╳` does.
  - Each card also shows its state: acting ▶, acted ✓, FNT for a corpse,
    GONE.
  - Damage and heals float up from the card (`-23`, `+15`) on the frame they
    happen.
- **Log:** the turn's lines so far beside the field, with the round and phase
  title above it.
- **Your turn:** the move buttons sit under the acting character. A move that
  can't be used is greyed out with its `why`. Picking a move highlights its
  `options` on the field; click one to target it, or press Esc for `back`.
- **End:** the winner, the round count, and a "play again" button.

Whether cards and buttons also say what each move does is an open question
(`questions.md`).

It follows the system light/dark setting and works at phone width; four
cards a side fit in two rows of two.

## Tests

`tests/web.cx`, mechanics not values, as everywhere else:

- **Messages:**
  - A `move` ask lists the actor's moves, with `why` set exactly where
    `why_not` says.
  - A `target` ask's options are the shape's legal slots.
  - `looks` round-trips through JSON.
- **The agent, over a real socket:** start `serve` in a task on a free port
  and `connect` a client that always answers index 0. Check that:
  - The game reaches `over`.
  - Every `ask` is answered before the next arrives.
  - Closing the client mid-game ends the session (`choose_move` returns
    `None`).
  - A bad index gets `error` and the same ask again.
  - `back` from a `target` ask returns to the `move` ask.

## Risks

- **CronyxLang#151 (memory before a suspending call is kept).** Every socket
  wait is a suspending call, so a server that plays many games may grow.
  Check this on the release with #157. If it's still true, restart the
  server now and then; one person playing locally won't notice.
- **No `wss`.** It's local play only, `http://localhost`. Hosting it publicly
  would need TLS in front, such as a reverse proxy.

## Later

- **Two players.** Two `WebAgent`s on two sockets in one game. This needs
  pairing (a lobby, or a game code in the URL).
- **Watching AI vs AI.** A session with two AI seats and a `WebViewer`.
- **Replays.** The balance checker's game log played back in the page.
- **Structured effects.** Effects as `{kind, rounds, stacks, good}` instead of
  the terminal's text, for icons and tooltips.
- **Reconnecting.** At the moment a closed tab abandons the game.
