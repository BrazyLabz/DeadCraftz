# DEADCRAFT
Open-world block survival set in 2010, after the outbreak. Infinite seeded worlds with cities, towns and farms, freeways, forests, deserts, beaches, and about 300 blocks of underground to mine.

During the day you scavenge. At night the horde comes out, and if zombies **see light** they rush you and tear through anything in their way.

## Easiest upload: ONE file
`dist/index.html` is the whole game packed into a single file: code, textures, models, sounds and mods.
- **GitHub Pages:** upload only that one `index.html` to your repo, then turn on Settings → Pages.
- **Offline:** just double-click it. It works without the .bat.
- **After you edit textures or code:** run `python tools/build_single.py` to rebuild it, or ask Claude to rebuild it.

## Play
- **iPhone / iPad (and Android):** open your GitHub Pages link in Safari. For full screen, tap **Share → Add to Home Screen** and launch DeadCraft from the home screen. It plays in landscape with touch controls:
  - Left thumb is the joystick. Push it all the way forward to sprint.
  - Drag the right side to look. **Tap** to place or use, **hold** to mine or attack.
  - Buttons: JUMP, HIT/FIRE, USE/AIM/EAT, SNEAK (EXIT in a car), BAG (inventory), TORCH, DROP and pause.
- **Windows:** double-click **`Play DeadCraft.bat`**. It starts a tiny local server and opens your browser. Keep the black window open while you play.
- **Online:** upload this whole folder to a GitHub repo, then go to Settings → Pages → `main` / root. Open `https://<you>.github.io/<repo>/`.
- Use Chrome or Edge with a mouse and keyboard. Double-clicking `index.html` won't work, because browsers block textures from local files.

**Controls:**

| Key | Action |
|---|---|
| WASD | Move |
| Space | Jump / climb ladders |
| R or double-tap W | Sprint (keeps sprinting until you let go of W) |
| Shift | Sneak |
| Left click | Mine / attack |
| Right click | Place / use / open crafting table |
| E | Inventory |
| 1–9 or mouse wheel | Hotbar |
| Q / Shift+Q | Drop item / drop whole stack |
| F | Move a torch to your left hand (and back) |
| Hold right click | Eat food / aim a gun |
| F3 | Debug info |
| F1 | Hide HUD |
| T | Show/hide the clock |
| Right click a car | Get in (with a Gas Can: refuel) |
| W/S · A/D · Space · Shift (in a car) | Drive · steer · brake · get out |
| Esc | Pause |

## Folder map: what to edit

| I want to change… | Edit this |
|---|---|
| **Block textures** | `textures/blocks/*.png` (16×16, or 32×32 for HD, as long as they're all the same size) |
| **Item / tool icons** | `textures/items/*.png` |
| **Your player skin** | `textures/entity/playerskin.png`: any normal Minecraft skin works (64×64, or old 64×32). Just name it `playerskin.png` |
| **Zombie skin** | `textures/entity/zombie.png` (64×64 Minecraft-style skin layout). Any **bright pure-red pixel** (like #FF0000) glows in the dark, and red pixels on the face get a glowing halo |
| **Player model** | `models/player.geo.json` (Blockbench, Bedrock format) |
| **Sound effects** | Replace any file in `sounds/` (.wav/.mp3/.ogg). The list and volumes are in `sounds/sounds.js` |
| **Guns** (damage, fire rate, auto, recoil, noise) | `js/data/items.js` (bottom) |
| **Gun models & arm positions** | `js/viewmodel.js` (`makePistol`, `makeRifle`, POSES) |
| **Food values** | `js/data/items.js` (`food:` points, 20 = full) |
| **Zombie model** | `models/zombie.geo.json` (open in **Blockbench** as a Bedrock model) |
| **UI colours / font / slot size / roundness** | `css/theme.css` |
| **Zombies-hunting-you icon** (top left) | `textures/gui/zombie_icon.png` (any size, square looks best) |
| **UI layout** | `css/ui.css`, `index.html` |
| **Blocks** (hardness, drops, which tool…) | `js/data/blocks.js` |
| **Items & tools** (damage, durability) | `js/data/items.js` |
| **Crafting recipes** | `js/data/recipes.js` |
| **Supply crate loot** | `js/data/loot.js` |
| **Day length, zombie counts & speed, sea level, building on/off** | `DC.config` at the top of `js/core.js` |
| **World generation** (cities, roads, biomes, caves, ores) | `js/worldgen.js` |
| **Zombie behaviour** | `js/entities.js` |
| **Cars** (speed, fuel, HP, and the box models) | `js/data/vehicles.js`. Add your own with `DC.registerVehicle` |
| **Driving / refuel / car breaking** | `js/vehicles.js` |

A missing texture shows as a purple/black checkerboard, so you can see what still needs painting.
If you add new blocks, add them at the **end** of `blocks.js` so old saves stay correct.

### Editing the zombie in Blockbench
1. Open Blockbench, go to File → Open Model, and pick `models/zombie.geo.json`. Load `textures/entity/zombie.png` as its texture.
2. Change cubes and paint the skin.
3. Export with File → Export → **Export Bedrock Geometry**, overwriting `models/zombie.geo.json`. Save the texture over `zombie.png`.
4. Bones named `head`, `body`, `rightArm`, `leftArm`, `rightLeg` and `leftLeg` animate automatically.

## Making mods
1. Copy `mods/metal_barricade` to `mods/my_mod`.
2. Add `'my_mod'` to the list in `mods/mods.js`.
3. Inside `mod.js`:
```js
DC.registerMod({ name: 'My Mod', version: '1.0', description: 'What it does' });
DC.registerBlock({ name: 'spike_trap', textures: { all: DC.modPath('spikes.png') }, hardness: 2, tool: 'pickaxe', placeable: true });
DC.registerItem({ name: 'canned_beans', desc: 'Heals 2 hearts', use: 'heal', heal: 4, texture: DC.modPath('beans.png') });
DC.registerRecipe({ pattern: ['I I', ' I '], key: { I: 'iron_ingot' }, result: 'spike_trap', needsTable: true });
DC.on('nightStart', day => DC.ui.notify('Night ' + day + ' begins!'));
```
**Events you can hook:** `init`, `worldStart`, `tick`, `nightStart`, `dayStart`, `blockBreak`, `blockPlace`, `blockChanged`, `zombieSpawn`, `zombieAttack`, `zombieBreak`, `zombieKilled`, `playerDeath`, `craft`, `generateChunk` (add your own structures!).

**Console cheats for testing** (press F12 → Console):
- `DC.give('iron_machete')` gives you an item.
- `DC.game.time = 590` skips to just before nightfall.

## v0.2 notes
- **Hunger:** the bar is on the right above the hotbar. Sprinting and jumping make you hungry faster. You only heal when hunger is 7 drumsticks or more, and you can't sprint at 3 or less. Hold right click to eat. Wheat makes bread (3 in a row); supply crates have cans, chips and chocolate.
- **Items drop on the floor:** they glow red and float. Walk over them to pick them up.
- **Zombies:**
  - Once a zombie starts hunting you, it keeps coming. If **none** of them can see you for 10 seconds (the white bar under the zombie counter), they lose track of you. Change it with `loseTrackTime` in `js/core.js`.
  - Zombies push each other apart, so they swarm around you instead of merging into one.
  - Hunters growl, and every zombie within 10 blocks of a growl joins the hunt.
  - Gunshots alert every zombie within 48–64 blocks.
  - Hunting zombies break blocks day and night. In daylight they break them more slowly.
- **Guns:** infinite ammo for testing. Pistol = iron ingots in a sideways L (`III` / `I..`); rifle = `III` / `PPI` (P = planks). Both need a crafting table. Headshots do double damage.

## v0.5 notes: shaders, iPhone, cartoon grass
- **Shaders (Options → Shaders):**
  - Warm sunlight with cool blue shadows on the sides of blocks.
  - Grass, wheat and leaves sway in the wind, and water ripples with sun glints.
  - Gradient sky with sunsets, a sun halo, stars and blocky clouds.
  - Fog that glows toward the sun, plus film-style colour grading and a vignette.
  - Turn shaders **OFF (fast)** on older phones. **Resolution** lowers the pixel count; the game also lowers it automatically if the frame rate drops.
- **Cartoon grass:** grass, dirt, leaves and tall grass are repainted in a bolder, Minecraft Dungeons-like style. Regenerate them with `tools/make_cartoon.py`, or just paint over the PNGs.
- **Shader code** lives in `js/render.js`, which explains each effect.

## v0.4 notes
- **You wake up in a hospital.** Ambulances and a cop car are parked out front.
- **Buildings** have staircases (walk straight up them), a corridor and separate rooms. Light now comes in through windows and doors, so rooms near windows are lit and deep inside is darker.
- **Cars:**
  - There are four kinds: sedans, pickups, abandoned police cars and ambulances. They sit on roads with a little fuel left.
  - Stop next to a **gas pump** (gas stations are in cities and towns) to refuel for free, or use a **Gas Can**.
  - Cars climb 1-block bumps, run zombies over, and their engines attract nearby zombies.
  - Hit a car with anything and it wobbles. Keep going and it breaks into **20 iron ingots**.

## Survival tips
- Punch trees to get logs. Make planks, then a crafting table, then wooden tools.
- Mine down for cobblestone, coal and iron. You need a stone pickaxe or better for iron.
- Forge iron at a crafting table: raw iron + coal makes an iron ingot.
- Coal + stick makes 4 torches. A torch in your hand lights the way, but at night it tells every zombie where you are.
- Break supply crates inside buildings for loot.
- Before dark, get inside and board up doors and windows with planks. Ladders take you up through apartments and offices to the roof.
