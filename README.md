# ❌⭕ Tic-Tac-Toe

A complete Tic-Tac-Toe game in a single HTML file. Built with vanilla JavaScript and rendered on an HTML5 Canvas, it offers three bot difficulty levels and a local two-player mode. Scores, settings, and the current board are saved in `localStorage`, so you can close the page and continue later.

- **No dependencies** — no frameworks, build step, or external assets.
- **Works offline** — open the file in a modern browser and play.
- **Resumes automatically** — unfinished rounds and pending bot turns survive a reload.

## Features

- Classic 3×3 Tic-Tac-Toe on a Canvas board
- Bot mode with **Easy**, **Medium**, and **Hard** opponents
- Local **2 Players** hot-seat mode
- Color-coded mode buttons with a clear active-state highlight
- Persistent scoreboard, settings, and current round
- Turn indicator that pulses in team colors
- Winning-line highlight and result modal
- Responsive layout for small screens and large displays
- Reduced-motion support
- English interface and source comments

## Quick start

1. Download or clone this repository.
2. Open `index.html` in a modern browser (verified in Chromium-based browsers).

No installation or server is required. If you prefer a local server:

```bash
python -m http.server 8000
```

Then open `http://localhost:8000/index.html`.

> Save data is scoped to the browser and URL/origin. A game opened from `file://` and a game served over HTTP keep separate saves.

## How to play

1. Choose a bot difficulty or **2 Players**.
2. Click or tap an empty cell to place your mark.
3. Get three of your marks in a row — horizontally, vertically, or diagonally — to win.
4. If all nine cells are filled with no winning line, the round is a draw.
5. Use **Play again** to start a new round. The score is kept.

Occupied cells cannot be selected. In bot mode, clicks are ignored while the bot is thinking.

## Game modes

### 🤖 Bot mode

| Property | Value |
| --- | --- |
| Your mark | **X** |
| Bot mark | **O** |
| First move | Random each round |
| Score labels | Player X / Bot |

At the start of every round, the game randomly decides who moves first. If the bot starts, its first move follows the selected difficulty. The bot waits about 300 ms before replying, and the board is locked during its turn.

### 👥 2 Players

| Property | Value |
| --- | --- |
| Players | Two people on the same device |
| Marks | X and O alternate |
| First move | X starts every round |
| Score labels | Player X / Player O |

X always opens a new round. The turn pulse changes between green for X and red for O.

## Difficulty levels

| Level | Strategy | Difficulty |
| --- | --- | --- |
| 😑 **Easy** | Picks a random empty cell. Does not look for wins or blocks. | Easy to beat |
| 🙂 **Medium** | Checks immediate wins and blocks first, then prefers the center, an opposite corner, a free corner, and finally a free side. | Beatable with good play |
| 😎 **Hard** | Full minimax search with alpha-beta pruning. Opens in the center and never loses. | Unbeatable — a draw is the best possible result |

### Easy

The Easy bot chooses uniformly from the available cells. It can miss an immediate win or fail to block yours, which makes it a good introduction for new players.

### Medium

The Medium bot follows a classic priority list:

1. Take an immediate winning move.
2. Block the opponent's immediate win.
3. Take the center.
4. Take the corner opposite an opponent's corner.
5. Take any free corner.
6. Take any remaining cell.

It blocks direct threats and usually punishes careless play, but it is not perfect and can be beaten.

### Hard

The Hard bot evaluates the full game tree. O (the bot) maximizes its score, X minimizes it, faster wins are preferred, and slower losses are preferred. Alpha-beta pruning keeps the search fast.

- On an empty board, Hard opens at the center.
- Among moves with the same optimal result, it chooses randomly. This keeps its play varied without sacrificing strength.
- Exhaustive playouts confirm that the player cannot force a win against Hard in either starting order.

## Scores and persistence

The game stores its state in `localStorage` under the key **`tic-tac-toe-save-v1`** (save version `1`).

Saved data:

- Game mode (`bot` or `2p`)
- Bot difficulty (`easy`, `medium`, or `hard`)
- Score for X, O, and draws
- All nine board cells
- Whose turn it is

Save triggers: every human move, every bot move, every new round, and a full reset.

Behavior after reopening the page:

| Last saved state | What happens |
| --- | --- |
| Unfinished round | The exact board and turn are restored. |
| Human just moved, bot was about to reply | The pending bot turn is resumed automatically. |
| Round already finished | A fresh board starts, and the previous score is kept. |
| No save, corrupt save, or unsupported version | A clean game starts. |

Scores accumulate across rounds and **Play again** does not clear them. Changing the mode or the bot difficulty starts a fresh game and resets the score; selecting the already active difficulty does not reset anything.

Storage is optional. If `localStorage` is unavailable or blocked, the game still runs normally, but progress will not persist.

## Turn indicator and colors

The board wrapper glows in the color of the active side:

- **Player X** — green `#15803d` marks, green pulse
- **Player O / Bot** — red `#e63946` marks, red pulse
- **Winning line** — gold `#ffd166`

In bot mode, only the human's turn pulses, since the bot does not need a visual prompt. The pulse stops on a win, a draw, when the result modal is open, and while the bot is thinking. With `prefers-reduced-motion: reduce`, the animation is replaced by a static glow.

## Responsive behavior

- The canvas scales with the viewport while keeping its aspect ratio.
- Internal board resolution is **460 × 460** (`20 px` padding + `3 × 140 px` cells).
- The scoreboard and controls wrap on narrow screens.
- The result modal stays inside the viewport.
- Layout verified at width **280, 320, 360, 375, 460, and 768 px** with no horizontal overflow.

Keyboard support covers the buttons. The Canvas board itself is pointer/touch operated.

## Tech stack

- HTML5 + Canvas 2D
- CSS3 (flexbox, animations, media queries)
- Vanilla JavaScript (ES6)
- `localStorage` for persistence

The whole game — markup, styles, rendering, AI, and storage — lives in `index.html`.

## Project structure

```text
.
├── index.html     # The complete game
└── README.md      # This file
```

### Implementation notes

- `minimax()` — alpha-beta search with depth-aware scoring
- `bestMinimaxMove()` — optimal move selection with random tie-breaking
- `heuristicMove()` — Medium difficulty priority rules
- `handleCellClick()` — canvas coordinate mapping with edge tolerance
- `saveState()` / `loadSavedState()` / `initGame()` — persistence and resume logic
- `updateTurnHint()` — turn-pulse state management

## Verification

The final build was tested in a Chromium-based desktop browser.

| Area | Result |
| --- | --- |
| Game rules | All 8 winning lines, draws, occupied-cell blocking, win highlight, result modal, and replay verified |
| Mode controls | Color coding, active-mode highlight, `aria-pressed` state, and score-reset behavior verified |
| AI correctness | 250 reference minimax positions, 120 optimal Hard moves, and 60 legal AI moves checked |
| Hard difficulty | 0 human wins across exhaustive playouts in both starting orders; center opening confirmed |
| Medium / Easy | Both beatable; Medium follows win → block → center → corner → side priorities |
| Input mapping | Exact boundaries, cell centers, padding/outside clicks, and 2P mapping verified at 1× and 0.75× scales |
| Persistence | Unfinished round, pending bot turn, finished round, setting changes, malformed JSON, unsupported version, and blocked storage verified |
| Responsive layout | 280–768 px widths, modal fit, controls, scoreboard, and click mapping verified |
| Console | 0 errors, 0 warnings |
| Assets / network | No external requests, iframes, or assets |
| Static checks | Valid JavaScript, English-only UI, no duplicate IDs, `lang="en"`, reduced-motion rules present |
