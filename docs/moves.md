# Moves

Every character has a **basic attack** and its other moves
([characters.md](characters.md)).

## Cooldowns

Every move other than the basic attack has a **cooldown**: after it is used, it
can't be used again for that many turns.

- Every such move has a cooldown of at least 2.
- The bigger the move, the longer the wait, so a high-impact move is worth
  saving for the right moment instead of firing it the moment it's ready:
  - **2** for a plain hit (Cleave, Aimed Shot, Smite, Soul Siphon, Bite, Shuriken, Trick Blade, and the
    Monk's two strikes);
  - **3** for a heavier hit, a single-target heal or buff, or a debuff;
  - **4** for team heals, area attacks, Hex and Execution;
  - **5** for the biggest area moves (Writhing Depths, Healing Rain);
  - more for bringing a character back (Ancestral Call, 7).

Cooldowns went up across the board after balance testing showed big moves
being used every time they were ready; they used to be mostly 1 to 3.

Cooldowns count the character's own turns, not rounds. If the Barbarian uses
Cleave with a cooldown of 2, it can't use Cleave on its next two turns, and
can on the turn after. A turn lost to Hex still counts as one of its turns, as it does
for Inner Peace ([characters.md](characters.md#monk)).

### Cooldowns by move

Placeholders until balancing; the live values are in `src/content/tuning.cx`.

| Character   | Move           | Cooldown |
|-------------|----------------|----------|
| Barbarian   | Cleave         | 2 |
| Ranger      | Aimed Shot     | 2 |
| Ranger      | Hunter's Mark  | 3 |
| Paladin     | Smite          | 2 |
| Paladin     | Guard          | 3 |
| Paladin     | Lay on Hands   | 3 |
| Cleric      | Prayer         | 4 |
| Cleric      | Purify         | 3 |
| Cleric      | Sanctuary      | 3 |
| Witch       | Brimstone      | 4 |
| Witch       | Hex            | 4 |
| Alchemist   | Acid Flask     | 3 |
| Alchemist   | Greater Health Potion | 3 |
| Alchemist   | Energize       | 3 |
| Monk        | Disarming Palm | 2 |
| Monk        | Crippling Blow | 2 |
| Monk        | Flurry of Blows| 3 |
| Grim Reaper | Reap           | 3 |
| Grim Reaper | Soul Siphon    | 2 |
| Warlock     | Writhing Depths | 5 |
| Warlock     | The Final Offering | 5 |
| Shaman      | Healing Rain   | 5 |
| Shaman      | Ancestral Call | 7 |
| Shaman      | Totem          | 3, from when the totem is destroyed ([summons.md](summons.md)) |
| Knight      | Shield Bash    | 3 |
| Knight      | Bulwark        | 3 |
| Assassin    | Backstab       | 3 |
| Assassin    | Execution      | 4 |
| Bard        | Allegro        | 3 |
| Bard        | Largo          | 3 |
| Bard        | Crescendo      | 4 |
| Fairy       | Fairy Ring     | 4 |
| Fairy       | Pixie Dust     | 4 |
| Necromancer | Wither         | 4 |
| Necromancer | Raise Dead     | 5 |
| Vampire     | Bite           | 2 |
| Vampire     | Hemorrhage     | 3 |
| Cryomancer  | Blizzard       | 4 |
| Cryomancer  | Frostbite      | 2 |
| Frost Giant | Avalanche      | 4 |
| Frost Giant | Glacial Roar   | 3 |
| Frost Giant | Cryosleep      | 4 |
| Construct   | Piston Slam    | 2 |
| Construct   | Lockdown       | 3 |
| Stormbringer | Thunderstorm  | 4 |
| Stormbringer | Chain Lightning | 3 |
| Ninja       | Shuriken       | 2 |
| Swashbuckler | Plunder       | 3 |
| Jester      | Trick Blade    | 2 |
| Jester      | Puppeteer      | 5, from when the puppet falls ([summons.md](summons.md#the-jesters-puppets)) |

## Basic attacks

Every character has a basic attack with **no cooldown**, so it always has
something to do.

- Basic attacks vary from character to character.
- They are weaker than a character's other moves.

### Basic attacks by character

Each is a direct hit on one foe ([damage.md](damage.md)), so taunts and Keen
Eye apply as for any single-target move. Names and powers are placeholders;
attackers hit a little harder, tanks and supports a little softer.

| Character   | Basic attack  | Power |
|-------------|---------------|-------|
| Barbarian   | Axe Swing     | 12 |
| Ranger      | Quick Shot    | 10 |
| Monk        | Jab           | 10 |
| Grim Reaper | Scythe Slash  | 10 |
| Witch       | Curse Bolt    | 10 |
| Warlock     | Void Bolt     | 10 |
| Paladin     | Mace Strike   | 8 |
| Knight      | Sword Strike  | 8 |
| Cleric      | Holy Spark    | 8 |
| Alchemist   | Acid Splash   | 8 |
| Shaman      | Spirit Strike | 8 |
| Assassin    | Quick Cut     | 10 |
| Bard        | Lute Strike   | 8 |
| Fairy       | Pixie Bolt    | 8 |
| Necromancer | Bone Spike    | 10 |
| Vampire     | Claw          | 10 |
| Cryomancer  | Ice Shard     | 10 |
| Frost Giant | Frozen Fist   | 8 |
| Construct   | Gear Punch    | 8 |
| Stormbringer | Spark        | 10 |
| Ninja       | Kunai         | 10 |
| Swashbuckler | Cutlass      | 10 |
| Jester      | Juggled Knife | 8 |
| Skeleton (minion) | Bone Claw | 10 |

## Silence

A debuff ([effects.md](effects.md)): a silenced character can only use its
basic attack.

How long it lasts depends on the move that applies it. Typically it covers the
character's next turn, like Hex, then goes away.
