# Metro Station Portfolio

A single-file interactive portfolio presented as a small metro-station game. Visitors explore the station, read portfolio information from the announcement board, collect a ticket, and use it to open a destination after a short train ride.

## Run It

Open [`index.html`](index.html) in a modern browser. The page is self-contained and does not require a build step, package installation, or server. An internet connection is used to load the Patrick Hand font; a cursive fallback is provided if it is unavailable.

## Personalize It

Edit the `CONFIG` object near the top of the inline script in `index.html`. Replace the sample name, station, welcome message, board entries, resume path, and ticket destinations with your own information. The `board` content accepts HTML, so use only trusted content.

The detailed field reference, interaction flow, architecture, and browser notes are in [`DOCUMENTATION.md`](DOCUMENTATION.md).

## Visitor Controls

- Move with the arrow keys or `W`, `A`, `S`, `D`.
- Interact with `E`, `Space`, or `Enter` when near an object.
- Press `M` or use the Sound button to toggle audio.
- Use **Plain version** to view the portfolio content without navigating the game; use **Back to the game** to return.
- On touch devices, use the on-screen direction pad and Interact button.

## Project Files

- `index.html` contains the markup, styles, portfolio configuration, canvas scene, controls, dialogue, and audio implementation.
- `DOCUMENTATION.md` is the implementation and customization guide.

There are no automated tests or build scripts in this project.