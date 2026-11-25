# Maps

You build Shothop levels in TrenchBroom (set up in the [README](../README.md)). A level is one `.map` file. The game builds it when you load it, so there's no compile or export step.

## Where maps go

| Put it in | Use it for |
|---|---|
| `mods/<your_mod>/maps/` | Anything you plan to share, or more than one level |
| `maps/` (next to `mods/`) | Quick one-off maps |

Both show up in **CUSTOM MAPS**. Mod maps say which mod they came from.

Subfolders are fine. `maps/arena/arena.map` works the same as `maps/arena.map`.

## The basics every map needs

1. **One `info_player_spawn`.** That's where you start.
2. **One `info_environment` right next to the spawn.** Sky, fog and the sun. Skip it and your level has no proper sky or sunlight.
3. **One `info_map_data` next to it too.** Level settings and trophy times.
4. **Lights.** `light_small` through `light_xlarge` for anything the sun doesn't reach.
5. **A way to finish.** An `info_portal_scene` with `map_name` set to the next level and `use_round_complete_screen` set to `1`.
6. **Valve 220 format.** TrenchBroom does this for you when you pick the Shothop game.

Every campaign level keeps the spawn, `info_environment` and `info_map_data` in one little cluster. Do the same and you always know where to find them.

**Skip `use_round_complete_screen` and the run never counts.** No time, no ghost, no badges. The portal just loads the next map.

`mod new` gives you a starter map with all of that already in it.

## World environment and sunlight

`info_environment` picks the sky and fog, and sets the sun:

| Property | What it does |
|---|---|
| `environment` | The look: `1` Alpha (desert night), `2` Bravo (volcanic), `3` Charlie (fractal space), `4` Delta (icy), `5` Lounge (hub) |
| `light_direction` | Sun angle as `x y z` degrees, like `-63 0 -87` |
| `light_color` | Sun colour as `r g b` from 0 to 255, like `220 230 255` |
| `light_energy` | Sun brightness. Campaign levels use `1` to `1.6` |
| `clouds` | Cloud layer, on by default |
| `celestial_body` | Sun or moon in the sky, on by default |
| `sky_rotation` | Slowly turns the sky, on by default |
| `shooting_stars` | Shooting stars across the sky |
| `falling_stars` | Star specks drifting around the player |
| `floating_rocks` | Huge rock islands drifting in the distance |
| `volcanic_ridge` | A ring of volcanic mountains around the map |
| `random_earthquakes` | Rolling earthquakes with camera shake |

Starting points the examples use:

| Look | Settings |
|---|---|
| Alpha | `environment 1`, `light_direction -63 0 -87`, `light_color 220 230 255`, `light_energy 1` |
| Bravo | `environment 2`, `light_direction -55 120 0`, `light_color 170 185 235`, `light_energy 1.35`, `volcanic_ridge 1`, `clouds 0` |
| Charlie | `environment 3`, `light_direction -42 120 0`, `light_color 165 200 255`, `light_energy 1.6`, `clouds 0`, `celestial_body 0`, `sky_rotation 0`, `shooting_stars 1`, `floating_rocks 1` |

## Title, description and screenshot

Put a `<map>.json` next to the map:

```json
{
  "title": "Neon District 01",
  "description": "Vertical rooftops. Shotgun only.",
  "author": "you",
  "version": "1.0.0",
  "screenshot": "screenshot.png"
}
```

Every field is optional.

| Field | Shows as |
|---|---|
| `title` | The name in the list. Defaults to the file name, tidied up |
| `description` | The text under the preview |
| `author` | `BY <AUTHOR>` |
| `version` | `v1.0.0` |
| `screenshot` | The preview image, relative to the map's folder |

No `screenshot` field? The game looks for `<map>.png` (or `.jpg`, `.jpeg`, `.webp`) next to the map. Inside a subfolder it also tries `screenshot.png` and `preview.png`.

Map in its own subfolder? You can name the JSON `map.json` instead of `<map>.json`.

## Enemies: use triggers

**Don't place enemies straight into the map.** Put down spawners and wake them with a trigger when the player walks in. That's how every campaign level does it: fights start when the player gets there, not the second the map loads.

1. Draw a brush where the fight should start, textured `skip` (invisible). Turn it into a `trigger_area`:
   - `uid_to_invoke`: a name for this fight, like `room1_wave`
   - `is_player_only`: `1`
   - `trigger_once`: `1`
2. Place a `trigger_enemy_spawner` where each enemy should appear:
   - `uid`: the same name, `room1_wave`
   - `enabled`: `0` (the trigger turns it on)
   - `enemy_type`: which enemy, from the table below
   - `max_lifetime_units`: how many it spawns in total (`0` = forever)
   - `spawn_maximum`: how many can be alive at once
   - `spawn_frequency`: seconds between spawns

Every spawner with that `uid` wakes up together. Want a second fight later on? New trigger, new name.

| `enemy_type` | Enemy |
|---|---|
| 1 | Melee |
| 2 | Dynamite |
| 3 | Arcane |
| 4 | Shooter |
| 5 | Shield |
| 6 | Achilles |
| 7 | Spider mine |
| 8 | Flying drone |
| 9 | Loot bot |
| 10 | Flamethrower |
| 11 | Gas bot |
| 12 | Spinner |
| 13 | Standing turret |
| 14 | Mine bot |
| 15 | Minigun |
| 16 | Knife |
| 17 | Rocket bot |
| 18 | Hammer |

All three combat examples (`example_single_map`, `example_pack_01`, `example_pack_02`) do exactly this. Open one in TrenchBroom and look.

Hardcore difficulty adds one extra enemy per spawner and spawns a second faster. You don't need to do anything for that.

## Linking levels

Portals load the next map by its file name without `.map`:

| Entity | What it does |
|---|---|
| `info_portal_scene` | A portal you walk into |
| `info_portal_full_scene` | Same, with a visible frame around it |
| `info_portal_interactive` | A portal a terminal can retune (see below) |

Set `map_name` to the target, like `neon_02`. Run `mod check` and it warns you if a portal points at a map that isn't installed.

## Sector terminals

Put your levels in a sector in `mod.json` ([how](mods.md#sectors)), then drop these two in a hub map:

1. An `info_portal_interactive` with `uid` set to something like `portal_neon`.
2. An `info_terminal_sector` with `sector` set to your sector id and `uid_to_invoke` set to `portal_neon`.

Players use the terminal to cycle through the sector's levels, and the portal follows. The first level is always open. The rest open once a player has visited them.

| Property | What it does |
|---|---|
| `sector` | Your sector id from `mod.json` |
| `uid_to_invoke` | The portal's `uid` |
| `custom_levels` | Comma separated map names to use instead of the sector's list |
| `allow_locked_levels` | `1` opens every level. Handy while testing |

## Level settings

One `info_map_data` per map sets level-wide options:

| Property | Default | What it does |
|---|---|---|
| `kill_depth` | `-200` | Fall below this height and you die |
| `disable_music` | `0` | `1` turns music off for this level |
| `disable_shotgun` | `0` | `1` takes the shotgun away |
| `disable_revolver` | `0` | `1` takes the revolver away |
| `default_flashlight_on` | `0` | `1` starts with the flashlight on |
| `vo_on_start` | empty | A sound to play when the level starts, by name (`neon_intro` for `audio/neon_intro.ogg`) |
| `is_hub_level` | `0` | `1` treats the level as a hub, like the lounge |

### Trophy times

The same `info_map_data` sets the times (in seconds) for the silver, gold and platinum trophies on the results screen, for Any% and 100% runs.

TrenchBroom doesn't list these, so add them yourself: select the `info_map_data`, click **+** in the entity properties, type the key, set the value.

| Property | Default |
|---|---|
| `trophy_silver_any_time` | `60` |
| `trophy_gold_any_time` | `45` |
| `trophy_platinum_any_time` | `30` |
| `trophy_silver_100p_time` | `80` |
| `trophy_gold_100p_time` | `65` |
| `trophy_platinum_100p_time` | `50` |

Play your level a few times, then set platinum just under your best run.

## Everything else

- What each entity does and its properties: [Entity reference](fgd_guide.md).
- Real entity names for your version of the game: `mod entities`.
- Your own textures and sounds: [Sounds, music and textures](assets.md).
