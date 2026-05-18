# ♟ ChessAnalyzer

A fully self-contained, single-file chess analysis and play application that runs entirely in the browser — no server, no install, no accounts required.

![License](https://img.shields.io/badge/license-MIT-blue) ![HTML](https://img.shields.io/badge/built%20with-HTML%20%2F%20CSS%20%2F%20JS-orange) ![Engine](https://img.shields.io/badge/engine-Stockfish%2016-green) ![chess.js](https://img.shields.io/badge/chess.js-0.10.3-purple)

---

## Features

### 📊 Analyze Mode
- Load and replay any PGN game with full move-by-move navigation (← → keys, click, autoplay)
- **Stockfish 16** engine analysis at configurable search depth (8–22 half-moves)
- Per-move classification: Excellent `!!`, Good `!`, OK `=`, Inaccuracy `?!`, Mistake `?`, Blunder `??`
- Classification badge appears directly on the destination square after each move
- Eval bar and live eval chart across all moves
- Accuracy percentage for both players
- Move breakdown statistics with visual bars
- **What-if mode** — branch off any position and explore alternative lines; engine evaluates the hypothetical against the actual move
- Best-move arrow overlay (green SVG arrow showing Stockfish's recommendation)
- Board flip, coordinate labels, check highlighting, last-move highlighting
- PGN import via paste or file upload (`.pgn`, `.txt`); supports multi-game files
- PGN export of any loaded game
- Two classic games preloaded: *The Immortal Game* (Anderssen–Kieseritzky, 1851) and *The Opera Game* (Morphy–Duke of Brunswick, 1858)

### 🎮 Manual Play Mode
- Free-play board with full chess rule enforcement (chess.js)
- Click-to-move with legal move hints (dots on valid squares, ring highlight on capturable pieces)
- Check highlight on the king in check
- Promotion piece selector (Queen / Rook / Bishop / Knight)
- Captured pieces display for both sides
- Move history with vertical scroll; click any move to jump back
- Board flip, undo/redo navigation controls
- Live Stockfish eval bar as you play
- **Send to Analyzer** — exports the played game into the Analyze tab and auto-triggers engine analysis
- PGN export with correct result tag (`1-0`, `0-1`, `1/2-1/2`, or `*`)

### 🤖 vs Computer Mode
- Play against Stockfish at 5 configurable depth levels (1, 3, 5, 8, 12)
- Choose your colour: White, Black, or Random
- Enter your name — used in the PGN headers and analyzer
- Engine thinking indicator with depth and eval feedback
- Captured pieces display, eval bar, move list
- Undo (rolls back your move + the engine's response)
- **Send to Analyzer** — one click to analyze the full game with Stockfish
- PGN export with your name vs "Stockfish" in the headers

### 👥 Player vs Player (Roadmap)
- Detailed phase-by-phase implementation plan built into the UI
- Phase 1–2 complete (engine, interactive board)
- Phase 3: Local hot-seat with clocks and resign/draw (next)
- Phase 4: Online play via room codes (planned)
- Phase 5: Player profiles and game archive (planned)
- Phase 6: Swiss/round-robin tournament mode (planned)

---

## Getting Started

No build step, no dependencies to install.

```bash
git clone https://github.com/your-username/chess-analyzer.git
cd chess-analyzer
open chess_analyzer.html   # macOS
# or just double-click the file in your file manager
```

Or serve it over HTTP (required for Stockfish Web Worker in some browsers):

```bash
python3 -m http.server 8080
# then open http://localhost:8080/chess_analyzer.html
```

> **Note:** Stockfish is loaded as a Blob Worker to avoid the `SharedArrayBuffer` requirement, so the file works with `file://` in most modern browsers. If you see engine errors, use the local HTTP server above.

---

## Usage

### Loading a game for analysis
1. Click **+ Load PGN** in the header
2. Paste PGN text or click **📂 Load File** to open a `.pgn` file
3. Click **Analyze** — Stockfish runs automatically at the selected depth
4. Use ← → arrow keys (or the on-screen buttons) to step through moves

### What-if mode
1. Navigate to any move in the Analyze tab
2. Press **W** (or click the What-if button) to enter What-if mode
3. Click a piece and make an alternative move
4. The right panel shows a side-by-side eval comparison of the actual vs. your move

### Playing vs the computer
1. Switch to the **🤖 vs Computer** tab
2. Enter your name, choose a colour, and set the engine depth
3. Click **▶ Start Game**
4. After the game, click **📊 Analyze Game** to review it with full engine analysis

### Keyboard shortcuts (Analyze tab)

| Key | Action |
|-----|--------|
| `←` | Previous move |
| `→` | Next move |
| `Space` | Toggle autoplay |
| `F` | Flip board |
| `W` | Toggle What-if mode |

---

## Architecture

Everything is in a single HTML file — no bundler, no framework, no network requests at runtime (Stockfish is fetched once on first analysis and cached as a Blob URL).

```
chess_analyzer.html
├── <style>          — CSS variables, layout, board, UI components
├── <body>
│   ├── Header       — game selector, depth slider, PGN import
│   ├── Mode bar     — Analyze / Manual Play / vs Computer / PvP
│   ├── panel-analyze
│   │   ├── Board panel   — board, eval bar, SVG arrows, badge
│   │   ├── Center panel  — Moves / Stats / PGN tabs
│   │   └── Right panel   — move detail, eval chart, accuracy, breakdown
│   ├── panel-manual — interactive free-play board
│   ├── panel-computer — vs Stockfish board + setup
│   └── panel-pvp    — implementation roadmap
└── <script>
    ├── Embedded game data (GAMES array)
    ├── Board renderer (parseFen, renderBoard, drawArrow)
    ├── Move list builder (buildMoveList, scrollMoveIntoView)
    ├── Engine (initStockfish, evalFen, runEngineAnalysis)
    ├── What-if mode (enterWhatif, exitWhatif, handleSquareClick)
    ├── Mode switcher (switchMode)
    ├── Manual Play (manualChess, manualSquareClick, …)
    ├── vs Computer (compChess, compEngineMove, …)
    └── PGN utilities (parsePgnText, buildPGNFromHistory, getGameResult)
```

### Dependencies (CDN, no local files needed)

| Library | Version | Purpose |
|---------|---------|---------|
| [chess.js](https://github.com/jhlywa/chess.js) | 0.10.3 | Move validation, FEN/PGN parsing, game state |
| [Stockfish](https://stockfishchess.org) | 16 (WASM) | Engine evaluation and best-move search |

Both are loaded from CDN at runtime. The app works fully offline after the first load if you pre-cache the CDN assets.

---

## Browser Compatibility

| Browser | Status |
|---------|--------|
| Chrome 90+ | ✅ Full support |
| Firefox 88+ | ✅ Full support |
| Safari 15+ | ✅ Full support |
| Edge 90+ | ✅ Full support |

WebAssembly and Web Workers are required for Stockfish. All modern browsers support both.

---

## Roadmap

See the **👥 Player vs Player** tab inside the app for the detailed plan. Summary:

- [x] Core chess engine and board renderer
- [x] PGN import/export and multi-game support
- [x] Stockfish analysis with move classification
- [x] What-if / alternative line explorer
- [x] Manual free-play mode
- [x] Play vs Stockfish at configurable difficulty
- [ ] Local hot-seat PvP with chess clocks
- [ ] Online play via room codes (WebSocket/WebRTC)
- [ ] Player profiles and game history (IndexedDB)
- [ ] Swiss/round-robin tournament mode

---

## Contributing

Pull requests are welcome. Since the entire app is one HTML file, keep changes self-contained — no build tooling is intentionally required.

1. Fork the repo
2. Make your changes in `chess_analyzer.html`
3. Test in at least one Chromium-based and one non-Chromium browser
4. Open a PR with a clear description of what changed and why

---

## License

MIT — do whatever you want, attribution appreciated.

---

*Built with chess.js and Stockfish. No frameworks were harmed in the making of this file.*
