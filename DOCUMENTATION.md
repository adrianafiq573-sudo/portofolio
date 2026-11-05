# Implementation Guide

This guide describes the current implementation in [`index.html`](index.html). The page is intentionally delivered as one HTML file: its CSS and JavaScript are inline, and it has no build system or server-side component.

## Visitor Experience

1. The opening screen introduces the station and portfolio. Selecting **Start game** starts the audio context and displays the welcome dialogue.
2. The visitor moves around a small station and interacts with nearby objects. The announcement board contains the portfolio sections; the ticket machine contains destination links.
3. After choosing a destination, the visitor takes the ticket to the gate. The game plays a short train sequence and offers **Continue** or **Go back**. Continuing opens the destination URL; `mailto:` destinations use the current tab.
4. The **Plain version** control bypasses the game and displays the configured portfolio sections and links in a scrollable page.

The station and train are drawn procedurally on a 320 by 180 canvas and scaled to a responsive 16:9 frame. There are no external image or audio assets.

## Configuration Reference

The `CONFIG` object near the start of the script is the primary place to personalize the portfolio:

| Field | Purpose |
| --- | --- |
| `name` | Name shown in the opening subtitle and plain version. |
| `station` | Station name shown on the opening screen and wall sign. |
| `welcome` | Dialogue shown after the game starts. |
| `resumeUrl` | URL or relative path for the resume download button on the board. |
| `board` | Ordered list of board tabs. Each item has a `title` and `body`; `body` may contain HTML. Set `resume: true` on an item to add a download button using `resumeUrl`. |
| `tickets` | List of destinations offered by the machine. Each item has a `label`, `dest`, and `url`. The sample also includes an `id`, which is not used by the current interaction flow. |

Update both `resumeUrl` and the resume ticket's `url` when the resume should be available from both the board and ticket machine. Replace every sample name, email, and profile URL before publishing. A `#` URL is a placeholder and will not navigate anywhere.

Board bodies are inserted as HTML. Only use content you trust; do not put visitor-supplied or otherwise untrusted strings in these fields without sanitizing them.

## Controls

| Input | Action |
| --- | --- |
| Arrow keys or `W`, `A`, `S`, `D` | Move around the station. |
| `E`, `Space`, or `Enter` | Interact with the nearest object in range. |
| `Escape` | Close a dialogue or panel while the game is in its normal UI state. |
| `M` or the Sound button | Toggle sound. |
| Touch direction pad and Interact button | Move and interact on coarse-pointer devices. |

An interaction prompt appears when the player is close enough to an object. Movement is blocked by the station boundaries and configured solid objects.

## Code Layout

Everything below lives in the inline `<script>` in `index.html`:

- **Configuration**: `CONFIG` contains portfolio copy and destinations.
- **State and world**: `state`, room geometry constants, `SOLIDS`, and `INTERACTABLES` define player state and the station's interactive area.
- **Queue NPCs**: `QUEUE` describes the waiting passengers. Each queue entry automatically adds a solid collision rectangle and a chat interaction. `drawQueue()` paints them.
- **Drawing**: `drawWall()`, `drawRoom()`, `drawBoard()`, `drawMachine()`, `drawGate()`, `drawPlayer()`, `drawGuard()`, and `drawTrain()` draw the scene with canvas primitives. `box()` adds the outlined, slightly irregular look.
- **Audio**: `initAudio()`, `setMusic()`, `sfx()`, and `trainRumble()` synthesize music and effects with the Web Audio API. The visitor may select a local audio file through the guard's music dialogue.
- **UI and flow**: `say()` handles dialogue, `openPanel()` handles panels, and `openBoard()`, `openMachine()`, `useGate()`, and `boardTrain()` implement the main station journey.
- **Input and game loop**: keyboard and pointer handlers update movement and interactions. `frame()` updates state and redraws the canvas on each animation frame.
- **Plain version**: an immediately invoked function builds the alternate view from `CONFIG.board` and `CONFIG.tickets`.

### Extending the Scene

For new portfolio text or destinations, update `CONFIG`; no drawing changes are needed. To add a queue passenger, add an entry to `QUEUE` with position, colors, and dialogue lines. To add a different kind of object, define its rectangle, register it in `SOLIDS` if it should block movement, add an `INTERACTABLES` entry with a prompt and `onUse` callback, and call its draw function from the station rendering branch in `frame()`.

To change the station artwork, edit the drawing functions. To replace procedural art with sprites, preserve the coordinate system and dimensions expected by the world logic. The CSS variables and `.grain` rule control much of the paper-and-pencil presentation.

## Runtime and Browser Notes

- Open `index.html` directly in a modern browser. No installation, build, or local server is required.
- The page uses the Canvas 2D API, modern JavaScript, and Web Audio. Audio is initialized from the Start button to comply with browser autoplay restrictions; if audio is unavailable, the visual experience remains usable.
- Patrick Hand is requested from Google Fonts. An offline or blocked request falls back to local cursive fonts.
- The custom audio file is selected locally, decoded in the browser, and is not uploaded or saved for later visits. Supported formats depend on the browser's audio decoder.
- The plain version exposes the configured text and destination links without requiring game navigation. It does not reproduce the station scene.
- The page responds to `prefers-reduced-motion` for the hint fade only; other canvas and scene animations continue.
- No automated tests or build scripts are included. After changing configuration, manually check the board tabs, plain version, ticket links, resume links, and game flow in a browser.