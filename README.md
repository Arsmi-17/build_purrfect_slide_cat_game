# Purrrfect Slide

A 2D sliding puzzle game. A cat slides across a tiled garden and cannot stop until something
stops it — and the puzzle is working out an order of pushes that covers every tile before it runs
out of room.

Built in Unity 6000.0.73f1 (URP 2D), released as a WebGL build on the Arsmi Games platform.

---

## How it plays

Swipe, or press an arrow key. The cat launches in that direction and **keeps going until it hits
something** — a wall, the edge, a trap. It never stops halfway, so you are not choosing where the
cat lands; you are choosing a direction and living with the consequence.

Every tile the cat crosses is painted behind it. **Clear the level by covering every path tile.**

Two ways to fail:

- **The cat runs out of moves.** Every direction is blocked, or leads somewhere it cannot go.
- **The performance meter empties.** It drains as you take moves, spend time, and bump into
  things.

The trap in the design is that the easy tile is the one you regret. Sliding all the way across an
open row is free progress, but it parks the cat against the far wall, and the tiles you skipped may
now be unreachable. Good levels are the ones where the greedy move loses.

### Controls

| Input | Action |
|---|---|
| Swipe, or arrow keys / WASD | Slide the cat |
| `Z` or `Backspace` | Undo the last move |
| `H` | Hint — shows the next move of a solved route |
| `P` | Reset all local progress (development shortcut) |

Undo unwinds a full snapshot, so cracked tiles, used junctions and the performance meter all roll
back with the cat. It is a puzzle, not a reflex test — you are meant to experiment.

## The tiles

| Tile | Behaviour |
|---|---|
| **Path** | Ordinary ground. Counts toward completion when covered. |
| **Wall** | Stops the slide. The cat parks against it. |
| **Blocked** | Cannot be entered at all. |
| **Empty** | A hole in the grid. Nothing there. |
| **Home** | Where the cat starts. |
| **Wall trap** | Looks like a wall, ends the run when the cat slams into it. Reading the board before committing is the whole point. |
| **Junction** | Passable **once per axis**. Cross it horizontally and the horizontal lane closes, but the vertical one is still open — so the order you cross matters. |
| **Breakable** | Cracks on the first crossing and collapses on the next. A route you used once may not be there when you come back. |

Junctions and breakables are what turn the level from a maze into a scheduling problem: not
*can I reach that tile*, but *can I reach it while the route I need later is still standing*.

## Stars

Every level is scored out of three stars against three independent measures:

- **Moves** — how many slides you took
- **Time** — how long you took
- **Mistakes** — how many blocked moves you attempted

The final score is **the worst of the three**, not the average. Three stars means the run was
tidy on all counts; being fast does not buy back a sloppy route. Thresholds are per level
(`threeStarMoveLimit`, `twoStarTimeLimit`, and so on), so a cramped puzzle can demand precision a
generous one does not.

Your **best** stars are kept per level, and replaying can only improve them. Your **most recent**
result is tracked separately, so the level card can show how the last attempt went without
overwriting a better one.

After clearing a level you can watch a **perfect replay** — the game solves the level itself
(`CatSlideLevelSolver`) and plays the route back.

## Progression

Levels unlock on two conditions at once:

1. **Sequence** — you have cleared the level before it.
2. **Stars** — you hold at least the level's `requiredStars` total.

So falling behind on stars gates you before the sequence does, and a level you rushed through with
one star may need revisiting.

Levels come in four flavours — `Easy`, `Normal`, `SemiBoss` and `Boss` — and are grouped into
themes of 18. Three themes ship: **Nature**, **Dessert** and **Lava**, each with its own palette,
background and wall art, paged through on the level select screen.

Progress is saved locally and mirrored to the player's Arsmi Games account, so it follows them to
another device. Merging is highest-wins per level, and progress never goes backwards on either
side.

## Project layout

```
Assets/
  Scenes/
    Start.unity            Title screen and settings
    Levels.unity           Level select, themed and paged
    LevelGenerator.unity   The level being played
  Scripts/
    Core/                  Level data, tile types, themes, the solver
    Gameplay/              Slide resolution, scoring, undo, win and lose
    Tiles/                 Junction, breakable, wall trap, star behaviours
    Progression/           Stars and unlocks, on PlayerPrefs
    UI/                    Screens, level cards, responsive grid
    Audio/                 Music and sound, with the platform mute layer
    GameHub/               Arsmi Games platform integration
  Levels/                  Level assets (CatSlideLevelData)
  Themes/                  Nature, Dessert, Lava
```

Levels are `ScriptableObject`s — **Create → Cat Slide/Level Data** — holding a grid of
`CatSlideTileType`, a start position, and the star thresholds. `CatSlideLevelSolver` can verify a
level is solvable before you ship it, and drives both the hint and the perfect replay.

## Running it

Open the project in Unity **6000.0.73f1** and press Play from `Assets/Scenes/Start.unity`. Any
scene works — nothing depends on entering through the title screen.

The game targets landscape (1920×1080 reference) and adapts to the viewport, with a separate
portrait grid layout for narrow screens.

## Publishing

See **[PUBLISHING.md](PUBLISHING.md)** for building the WebGL player and uploading it to Arsmi
Games, including what the platform checks and how to read a rejection.
