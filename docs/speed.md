# Speed and turn order

Speed isn't a number that is compared. Each character is in one of three speed
tiers, and the tiers set the order everyone acts in each round.

## Tiers

| Tier   | Goes   | Team that acts first in the tier |
|--------|--------|----------------------------------|
| Fast   | first  | home                             |
| Normal | second | away                             |
| Slow   | last   | home                             |

One team is **home** and the other is **away** for the whole battle.

## Ordering a round

1. Every character in the Fast tier acts before any Normal character, and every
   Normal character acts before any Slow one.
2. Within a tier, the two teams take turns: a character from the team with
   priority in that tier, then one from the other team, and so on.
3. When one team has no characters left in the tier, the other team's
   remaining characters in it act back to back.
4. Within one team and one tier, characters go in slot order: slot 0 first.
5. Each character acts once a round.

## Choosing actions

A character chooses its action when its turn comes, not at the start of the
round. It sees everything that has happened in the round so far.

## Phases

A round runs as three phases, Fast, then Normal, then Slow. Each phase passes
priority back and forth between the teams:

1. Priority starts with the team that has it in this phase (home in Fast and
   Slow, away in Normal).
2. If the team with priority has a character that can act in this phase, its
   lowest-slot one takes an action, whatever that character's tier.
3. Priority passes to the other team, whether or not anyone acted.
4. Repeat from 2 until neither team has a character that can act in this
   phase. Then the next phase starts.
5. After the Slow phase the round ends.

A character **can act in a phase** when it is still standing, its current tier
is the phase's tier or faster, and it hasn't acted this round. So a Fast
character can also act in the Normal and Slow phases, and a Normal one in the
Slow phase.

Who can act is checked fresh at every step, because tiers can change
mid-round. A character slowed from Fast to Slow before it acts sits out the
Fast and Normal phases and acts in the Slow phase. A character sped up from Slow
to Fast during the Normal phase can act straight away in the Normal phase.

## Examples

Home has H1, H2 and away has A1, A2, each in slots 0 and 1.

**Everyone is Fast:** it alternates the whole way.

```
Fast:   H1 A1 H2 A2
```

**Home is Fast and away is Slow:** home acts twice in a row, then away does.

```
Fast:   H1 H2
Slow:   A1 A2
```

**H1 Fast, H2 Normal, A1 Normal, A2 Normal:** away has priority in Normal, so A1
acts before H2.

```
Fast:   H1
Normal: A1 H2 A2
```

**Everyone is Normal, and A1's move slows H1 to Slow:** H1 hasn't acted yet,
so it drops to the Slow tier, and H2 takes home's turn.

```
Normal: A1 H2 A2
Slow:   H1
```
