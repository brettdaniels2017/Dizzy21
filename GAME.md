# Rock Clicker (Dizzy21)

Single-file idle clicker. Main code: `index.html`. Live site: https://brettdaniels2017.github.io/Dizzy21/  
GitHub: https://github.com/brettdaniels2017/Dizzy21.git  
Local play: open `index.html` or serve the folder (Ruby `httpd` on port 8000 was used when Python CLI tools were missing).

## How it plays

- Click the rock for rocks. Shop buys upgrades. Progress saves in `localStorage` key `rock-clicker-v1`.
- **Click tools (global, kept on every land):** Stone pick, Sledge line, Beam cutter.
- **Land tools (per rock, reset on a newly unlocked land; old land keeps its own):** Chip crew, Mine carts, Bore drill, Excavator, Orbital quarry.
- **Lands (paid unlock, not auto from total mined):** Coal cavern (free; save id `pebble`) → Granite → Quartz → Diamond mine (save id `gold`) → Obsidian → Meteorite. Unlocking a land does **not** switch you onto it; tap the land card to change rocks.
- Unlocked lands keep **idle income** even when not selected. Clicks only apply to the active rock. Header/stat **All lands / sec** is the combined idle rate. Land cards show that land’s `/s`.

## Visuals (tied to the active land only)

- **Chip crew:** wooden mill on the **right**, small walking miners with picks. Shirt color uses crew visual tiers. Up to 5 walkers.
- **Mine carts:** metal depot on the **left**, rail, one cart that upgrades in color with cart level. Not the same building as the mill.
- **Bore drill / Excavator:** first buy goes to the gear rack. Drag near the rock to place; drop position is kept. Drag a placed tool to move it. Extra buys upgrade the same tool. Save ids stay `drill` / `blast`. Excavator truck still hauls to the factory from wherever you park it.
- **Active rock face:** each land paints ore on the boulder (coal chunks, granite speckles, quartz crystals, diamonds, glassy obsidian, pitted meteorite). Land cards use matching swatches. Save ids stay `pebble` / `gold`.

## Economy (current intent)

- Shop costs use `costOf`: early copies cheaper (`starter`, slow `pow`), then exponential. Idle tools also × `autoCostMult()`.
- Idle tool **base prices** are +50% vs the old pebble bases. Each later land multiplies idle-tool prices by a **flat 1.5** (`Math.pow(1.5, landIndex)`). Works for future lands. Click tools ignore this.
- Land **unlock** prices were raised **75%** (Granite 1400, Quartz 15750, Diamond mine 148750, Obsidian 1.3125M, Meteorite 11.375M).
- **Rewards:** idle copies still use `copyPower` at 14% compounding; every 5th is a 5× reward (later 5ths bigger). **Click tools** (pick / sledge / laser) use a milder curve: 6% compounding and 2× milestones that grow slowly, so per-click stays progressive without the old exponential spike. Shop shows next copy vs a first copy. Late prices were eased (`n/16`, lower extra exponent) so cost doesn’t outrun payout as hard.
- Idle of a land = (that land’s auto tools + placed drill/excavator) × land.mult.

## Code map

- `LANDS`, `UPGRADES`, `PLACEABLES`, `state.landStats[landId] = { owned, placed }`
- `ownedOf(id, land?)` — click upgrades from `state.owned`; others from the land bag
- `costOf`, `autoCostMult`, `scaledStat` / `copyPower`, `landIncome`, `perSec`, `perClick`
- `syncMiners` — cart factory/rail/cart + crew mill/walkers + excavator/road/truck
- `syncPlaceables` — gear rack + placed drill/excavator at saved spots

## GitHub Pages

- Site is `index.html` at repo root plus `.nojekyll`.
- Pushing `main` updates Pages; wait ~1 minute and hard-refresh. Edits here do **not** publish until push.
- Push needs GitHub auth in this environment (`git push origin main`).
