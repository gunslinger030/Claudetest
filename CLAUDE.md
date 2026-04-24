# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the projects

No build system or dependencies. Open either HTML file directly in a browser:

```
start chronicles.html
start tictactoe.html
```

Or use a local server (e.g. `npx serve .`) to avoid any browser file-protocol restrictions.

## Git workflow

After completing any meaningful unit of work, commit and push to GitHub:

```
git add <files>
git commit -m "short description of what changed and why"
git push
```

Commit frequently — after each feature, fix, or notable change — so there is always a clean checkpoint to revert to. Never batch unrelated changes into one commit.

## Architecture

Both projects are **single-file HTML applications** — all CSS, HTML markup, and JavaScript live in one `.html` file each. There are no external dependencies, no bundlers, and no frameworks.

### chronicles.html — MUD-style RPG

The file is structured in three sections:

1. **Data tables** (top of `<script>`) — `SPELLS`, `ITEMS`, `MONSTERS`, `WORLD`, `SHOPS`, `QUESTS` are plain JS objects that define all game content. All gameplay tuning happens here.

2. **Game state** — a single `player` object holds the character's stats (`power`, `agility`, `toughness`, `knowledge`, `cunning`, `luck`, `hp`, `mp`, `gold`, `level`, `exp`), inventory, equipment, active quests, and current room ID. Combat state lives in a separate `combat` object.

3. **Engine functions** — stateless functions that read/mutate `player` and re-render the UI panels. Key entry points:
   - `startNewGame()` / `loadGame()` — initializes or restores state from `localStorage`
   - `movePlayer(dir)` — room transitions and encounter rolls
   - `startCombat(monsterId)` / `playerAction(action)` — turn-based combat loop
   - `processCommand()` — parses the text input box
   - `updateUI()` — syncs all three panels to current `player` state

   Save/load uses `localStorage` key `chronicles_save`.

**Three-panel layout**: left panel (character stats + quests), center panel (scrolling output log + action buttons), right panel (inventory + area info).

**Classes**: Fighter (high HP/power) and Mage (high MP/knowledge, spell scaling). Class choice affects starting stats, available equipment (`classes` filter on ITEMS), and spell access.

**World graph**: `WORLD` is a flat object of room IDs → room descriptors with `exits` (direction → room ID), `encounter` (array of monster IDs), `encounterRate`, and flags like `safe`, `isInn`, `isShop`.

### tictactoe.html — Tic-Tac-Toe

Single `<script>` block. State is four variables: `board` (9-element array), `current` (active player), `gameOver`, `vsCPU`. The CPU uses an unmodified minimax search (`minimax()`) — it plays perfectly and cannot be beaten, only drawn.
