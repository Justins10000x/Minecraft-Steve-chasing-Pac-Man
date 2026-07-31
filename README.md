# Minecraft Steve chasing Pac-Man (Block Hunter)

A blocky miner hunts a pellet-munching chomper through a procedurally generated maze. The chomper is trying to eat the level clean and escape. You're trying to corner it first. Grab a gold pellet and the hunt flips: for a few seconds, it comes after you.

The whole game is one HTML file. No dependencies, no build step, no bundler. Open it and it runs.

---

## Play

Open `index.html` in a browser. That's  it.

If you want it over HTTP:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

### Controls

| Input | Action |
| --- | --- |
| Arrow keys / `W` `A` `S` `D` | Move the miner |
| `Space` or `P` | Pause |
| Swipe | Move (touch devices) |

### Rules

- **You win a level** by touching the chomper while it's yellow.
- **You lose a life** if the chomper eats every pellet in the maze, or if it touches you while it's red.
- **Gold pellets** are the chomper's power-up, not yours. When it eats one it turns red and hunts you for about six seconds. Keep your distance and wait it out.
- Each level generates a brand new maze and speeds the chomper up — but never past the point where you can catch it.

---

## How the chomper thinks

The chomper isn't following a scripted path or a canned "ghost personality." Every time it arrives at a tile, it runs two breadth-first searches over the maze:

1. One flooding outward from **you**, giving the step-distance from the player to every tile.
2. One flooding outward from **every remaining pellet at once**, giving the distance to the nearest food from every tile.

Then it scores each available exit and takes the best one:

```
score = panic × distanceFromPlayer  −  1.1 × distanceToNearestPellet
```

The interesting part is `panic`, which scales with how close you are:

| Your distance | panic | Behaviour |
| --- | --- | --- |
| 9+ tiles | 0.35 | Grazes calmly, beelines for pellets |
| 4–8 tiles | 2.4 | Nervous — eats, but keeps an escape route |
| under 4 tiles | 6 | Pure flight, ignores food entirely |

When it's powered up the sign flips to `−4 × distanceFromPlayer` and it simply closes on you.

A small random jitter is added to every score so it doesn't move like a solved equation. It also refuses to reverse direction unless it's cornered — which is exactly the mistake you're trying to force it into.

**The maze matters more than the AI.** A "perfect" maze (one path between any two points) makes for a terrible chase — every corridor is a dead end and catching the chomper stops being a skill. So the generator carves a standard randomised depth-first maze and then *braids* it: dead ends get an extra wall knocked through, turning the maze into a web of loops. That's what makes the hunt feel like a hunt.

Tuned so a near-optimal player wins about three rounds in four, with a median catch time around 22 seconds.

---

## Making it yours

Everything lives in `index.html` — styles in the `<style>` block, game code in the `<script>` block at the bottom.

The tuning constants sit at the top of the script:

```js
const COLS = 27;                  // maze width in tiles (must be odd)
const ROWS = 21;                  // maze height in tiles (must be odd)
const PLAYER_SPEED = 6.6;         // tiles per second
const CHOMPER_BASE_SPEED = 5.5;
const BRAID_CHANCE = 0.88;        // 0 = twisty dead ends, 1 = wide open loops
const START_LIVES = 3;
```

**The miner is a text file.** There's no sprite sheet — the character is drawn from a grid of letters mapped to a colour palette. Edit the strings, and the character changes:

```js
const MINER_FRAMES = [[
  '.HHHHHH.',
  'HHHHHHHH',
  'HSSSSSSH',
  '.SESSES.',
  '.SSMMSS.',
  'ATTTTTTA',
  'ATTTTTTA',
  '.LL..LL.'
]];
```

Swap `T: '#35b7a6'` in `PALETTE` and you've changed his shirt. Add rows and columns for a bigger character (bump the `px` value in `drawMiner` to match).

**The on-screen title** says `BLOCK HUNTER`. It appears twice in the `<h1 class="marquee__title">` line near the top of the body — change both and it's renamed.

**Poke at it live.** The game exposes itself on `window.BlockHunter`, so you can open devtools and try:

```js
BlockHunter.state.lives = 99;
BlockHunter.state.level = 20;      // watch how fast it gets
BlockHunter.config;
```

---

## Publishing it

**Settings → Pages → Source: Deploy from a branch → `main` / `(root)`.**

It's a static site, so it'll be live at `https://<your-username>.github.io/Minecraft-Steve-chasing-Pac-Man/` in a minute or two. Worth pasting that link up at the top of this README once it's up.

---

## A note on the sprites

The miner and the chomper are original pixel art, drawn from the palette and character grid in the script rather than ripped from anywhere. Nothing here needs licensing or attribution, and the game is a homage rather than a copy — the mechanics are inverted, the maze is procedural, and the art is its own thing.

---

This is the file for V1.1.1 of the game, this won't be updated frequently, I've got too much going on in life. If i ever do go clear, perhaps I'll launch it someday. Love 

--- 

## License

MIT — see [LICENSE](LICENSE).
