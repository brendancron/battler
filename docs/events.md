# Events and triggers

## Definitions

- **State.** Everything about the battle at one instant.
- **Action.** Changes the state from one state to another. As well as
  changing the state, an action emits a list of **events**.
- **Event.** A record of something that happened, such as "damage taken" or
  "character fainted".
- **Trigger.** Listens for a kind of event and, when one happens, causes
  another action. For example: "when I get hit directly, deal 10 damage to the
  attacker".
- **Trigger engine.** Every event is passed through it, and it fires each
  trigger that matches.

## How they connect

```
action ──▶ new state + events ──▶ trigger engine ──▶ triggered actions ──▶ …
```

A triggered action is an action like any other, so it emits events of its own,
which go through the trigger engine again. This can recurse.

## Events from the game itself

Not every event comes from an action. The game's phases emit events too, such
as:

- start of a character's turn
- end of a character's turn
- end of the round

These go through the trigger engine the same way, so anything that happens
"at the end of the round" or "at the start of my turn" is a trigger on one of
these events. Effects that tick at the end of the round
([effects.md](effects.md)) are triggers on the end-of-round event.

## Order of resolution

Events resolve **depth first**: when an event sets off a trigger, the triggered
action and everything it sets off are resolved before the next event in the
original list.

An action emits events A and B, and A sets off a trigger whose action emits C:

```
A → C → B
```

The order should be swappable (a strategy), so a queue, where B would go before
C, can be tried later without rewriting the engine.

## Order between triggers

When several triggers match one event, the order they fire in is decided by an
ordering strategy passed to the trigger engine, not built into it. Different
events may want different orders.

A likely default: the triggers on the character the event is about fire first.
The order among the rest is undecided.

## Direct and indirect damage

Damage is either **direct** or **indirect**. A move hitting its target is
direct; damage from a trigger or an effect such as poison is indirect.
Triggers can listen for one or the other, so "when I get hit directly" doesn't
fire on a counter-attack's damage.

## Provenance

Every event carries its **provenance**: the chain of actions and triggers that
led to it, back to the original action or game-phase event. When a trigger
fires, the events its action emits carry the chain so far with that trigger
added on the end.

Every trigger has a **should trigger** check that decides whether it fires on
an event, and that check always looks at the provenance chain. The usual check
is that the trigger isn't already in the chain, which keeps two triggers from
setting each other off forever. A trigger can check for more than that, such
as only firing on direct hits.
