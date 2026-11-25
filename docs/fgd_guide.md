# TrenchBroom + Shothop FGD Guide

> **Making a map or mod?** Skip section 1. It's for people working on the game itself in Godot. Your setup is in the [README](../README.md). Sections 2 to 4 are what you want: how to wire entities together, and what every entity does.
>
> Entity names can change between game versions. `mod entities` lists the real ones for your installed game, and `mod check` catches old names in your maps.

This game ships **one** FGD ("Shothop") built from FuncGodot entity resources living under
[`trenchbroom/fgd/`](../../trenchbroom/fgd/). It currently defines **149 usable entity classes**
(148 gameplay entities across `point/` and `solid/` folders, plus 1 `model_point`), assembled by
[`trenchbroom/fpsdevkit_fgd.tres`](../../trenchbroom/fpsdevkit_fgd.tres) and wrapped into a full
TrenchBroom game config by [`trenchbroom/trenchbroom_config.tres`](../../trenchbroom/trenchbroom_config.tres).

This doc walks through: one-time setup, how to place/wire entities in TrenchBroom, and a full
reference of every shipped entity. If you get lost, cross-reference a real example in
[`trenchbroom/maps/dev_sandbox.map`](../../trenchbroom/maps/dev_sandbox.map) — it's the sandbox/kitchen-sink
map and has a working instance of almost everything below.

## 1. One-time setup

### 1.1 Point func_godot at your TrenchBroom install

TrenchBroom discovers games by scanning its own `games/` folder (per-machine, not part of this repo)
for subfolders that each contain a `GameConfig.cfg` + `icon.png`. That folder is usually:

- Linux: `~/.TrenchBroom/games/`
- Windows: `%APPDATA%/TrenchBroom/games/`
- macOS: `~/Library/Application Support/TrenchBroom/games/`

In the Godot editor:

1. Open your personal local-config resource — `trenchbroom/stuyk_trenchbroom_local_config.tres` or
   `trenchbroom/bh_trenchbroom_local_config.tres` (or make your own copy, one per developer, since the
   real path is never committed to git — it's saved to `user://func_godot_config.json` on your machine).
2. Double-click it in the FileSystem dock so it opens in the **Inspector**.
3. Set **Trenchbroom Game Config Folder** to `<your TrenchBroom games dir>/Shothop`
   (create the `Shothop` subfolder if it doesn't exist — e.g. `~/.TrenchBroom/games/Shothop` on Linux).
4. Click the **Export func_godot settings** button at the top of the Inspector.

### 1.2 Export the game config + FGD

1. Open `trenchbroom/trenchbroom_config.tres` in the Inspector (double-click it in FileSystem dock).
2. Click the **Export GameConfig** button at the top of the Inspector.
3. This writes three files into the folder from step 1.3:
   - `icon.png` — the game's TrenchBroom icon
   - `GameConfig.cfg` — tells TrenchBroom the game exists, its supported map formats, texture root, etc.
   - `Shothop.fgd` — the actual entity definitions (all 149 classes below), generated from every
	 `.tres` file under `trenchbroom/fgd/`
4. Re-run this any time you add/edit an entity `.tres` file — TrenchBroom does not see your Godot
   resources directly, only this exported `.fgd`/`.cfg` snapshot.

### 1.3 Point TrenchBroom at the project

1. Launch TrenchBroom. "Shothop" should now appear in the game list (from the config folder above).
   If not, restart TrenchBroom — it only scans `games/` on launch.
2. **File → New Map** (or **Open** an existing one under `trenchbroom/maps/`), choose game **Shothop**.
3. The first time you open/create a map for this game, TrenchBroom asks for a **Game Path** — point it
   at the repo's `trenchbroom/` folder (this is where `textures/` and `models/` live, which TrenchBroom
   needs for texture browsing and model previews).
4. Map format is **Valve 220** (`// Format: Valve` in the map header, `"mapversion" "220"` on worldspawn)
   — this is set automatically once you pick the Shothop game, but if TrenchBroom asks, pick Valve.

### 1.4 Getting your map into the game

TrenchBroom only edits `.map` files — Godot never reads them directly. FuncGodot's map importer
converts a saved `.map` into a Godot scene. Save your map under `trenchbroom/maps/` (matching the
existing zone folders like `alpha/`, `bravo/`, `delta/`, or the shared `trenchbroom/maps/` root for
dev/test maps), then use the FuncGodot import flow in Godot to turn it into a `.tscn`. The
`fpsdevkit_settings.tres` (`FuncGodotMapSettings`) resource is what drives that import — it already
points at `trenchbroom/textures` and the shared FGD, so you shouldn't need to touch it.

## 2. Placing and wiring entities in TrenchBroom

### Entity names: `<category>_<name>`

Every Shothop entity is named `<category>_<name>`. TrenchBroom's entity browser groups entities by
the word before the first underscore, so each category gets its own folder. Categories:

| Category | What lives there |
|---|---|
| `area` | Brush volumes that react when something enters (`area_trigger`, `area_killbox`, `area_weather`) |
| `audio` | Sound emitters and ambient areas |
| `decor` | Plants, trees, props, graffiti, security cameras |
| `destructable` | Things that break |
| `display` | Screens, posters, signs |
| `effect` | Explosions and particles |
| `enemy` | Enemy spawners. Enemies only enter a map through these, fired by a trigger |
| `info` | Map setup: player spawn, environment, map data, cinematic cameras |
| `interact` | Switches, keycard switches, panels, press-E triggers |
| `light` | Lights and `light_group_switch` |
| `logic` | Invoke plumbing: counter, delay, gate, random, repeater, sequence, relays |
| `lounge` | Hub-only terminals, radio and screens |
| `marker` | Path, target and teleport markers |
| `mover` | Moving brushes: elevators, loops, rotators, crushers |
| `pickup` / `powerup` | Collectibles and weapon upgrades |
| `portal` | Level portals |
| `trap` | Hazards and traps, including fixed turrets (`trap_turret`) |
| `tutorial` | Tutorial markers, screens, control prompts and the training dummy |
| `water` | Water brushes |
| `world` | Special brushes: ice, crystal, windows, glass, player clip, footstep surfaces |

`func_group`, `worldspawn`, `func_geo`, `func_detail`, `func_detail_illusionary` and
`func_illusionary` are TrenchBroom / FuncGodot built-ins and keep their names.



### 2.1 Point entities vs. solid (brush) entities vs. model-point entities

The FGD has three kinds of classes, and TrenchBroom treats each differently:

- **Point entities** (`point/...`, e.g. `pickup_medkit`, `light_large`, `audio_sound`) — a single
  origin with no geometry of their own. Place with the **Entity Browser** (View → Entity Browser, or
  the icon in the top toolbar): drag the class onto the 3D/2D view, or select it and press Enter.
  Some point classes render a preview model in the editor (anything backed by a `scene_file`, e.g.
  `pickup_medkit`, most `enemy_*`, most `decor_*`); others show as a plain bounding-box gizmo
  (anything without a scene, e.g. `marker_target`, `audio_sound`).
- **Solid entities** (`solid/...`, e.g. `area_trigger`, `func_group`, `destructable_dynamic`) — you build
  one or more brushes first (Brush Tool, or drag from a primitive), select them, then right-click →
  **Move Brushes to Entity → <classname>** (or press the assigned key after selecting the class in the
  Entity Browser while brushes are selected). The brush geometry itself becomes the entity's shape/trigger
  volume/collision.
- **Model-point entities** (`model_point/...`, currently just `trap_train`) — like point entities, but
  are explicitly modeled around a moving 3D model rather than a static prop.

### 2.2 The Entity Property editor

Select any placed entity, open the **Entity Inspector** panel (bottom of the TrenchBroom window by
default). Every property documented per-entity below shows up there as an editable field — text,
number, checkbox, or color swatch, depending on the type declared in the FGD. Hovering a property name
shows the same description text that's reproduced in this doc.

### 2.3 The `uid` / `uid_to_invoke` / `uid_to_move_to` linking pattern

> **Not the same "uid" as Godot's `uid://...` resource identifiers.** Godot auto-generates
> `uid://...` values for its own resources (`.uid` sidecar files, `ext_resource` entries) — those
> are engine-internal and must never be hand-written. The `uid` properties described below are a
> completely separate, unrelated concept: plain string identifiers used by FuncGodot entities to
> reference each other at the game-logic level.

Everything in this FGD communicates through three string properties, each with a distinct meaning:

- **`uid`** — an entity's own identifier, published so other entities can find it. Set this on the
  thing being invoked or moved to (a door, an elevator, a waypoint marker).
- **`uid_to_invoke`** — on the entity that *fires* (a switch, trigger, panel) — the `uid` of the
  entity to invoke.
- **`uid_to_move_to`** — on an entity that moves (an elevator, crusher, train) or teleports the
  player — the `uid` of the waypoint/marker entity to move toward.

Each concept also has a matching Godot group that invokable/waypoint entities self-register into,
and that `Utility.find_target`/`find_targets` searches: entities holding a `uid` join either the
`"uid_to_invoke"` group (if they can be triggered) or the `"uid_to_move_to"` group (if they're a
movement waypoint, e.g. `marker_target`).

Example, straight from `trenchbroom/maps/dev_sandbox.map`:

```
// A door switch that invokes something with uid "switch_test" when used
"classname" "interact_switch"
"origin" "1256 -56 88"
"angle" "180"
"uid_to_invoke" "switch_test"
```

Give the door/light/trigger you want opened a matching `uid` of `switch_test`, and the switch will
fire it. This same pattern powers `interact_keycard_switch`, `area_trigger`, `mover_elevator`,
`mover_brush`, and most `trap_*`, `area_*`, `interact_*` and `logic_*` entities — look for `uid_to_invoke` on the
"cause" and `uid` on the "effect."

Event hooks use the same pattern with a suffix: `uid_to_invoke_on_<event>` fires the target each
time that event happens. `enemy_spawner` has `uid_to_invoke_on_spawn` (per enemy spawned)
and `uid_to_invoke_on_death` (per spawned enemy killed). Chain a hook into `logic_counter` to fire
something once after N events:

```
// Spawner: 5 enemies total, each death bumps the counter
"classname" "enemy_spawner"
"max_lifetime_units" "5"
"uid_to_invoke_on_death" "arena_kills"

// Counter: fires "arena_door" once, on the 5th death
"classname" "logic_counter"
"uid" "arena_kills"
"required_count" "5"
"uid_to_invoke" "arena_door"
```

Difficulty adds bonus units to `max_lifetime_units`, so a fixed `required_count` can be reached
before the spawner finishes on Hardcore. Set `required_count` for the lowest difficulty, or gate on
something else when every spawned enemy must die.

Logic blocks (point entities, all invoked by `uid`, all chainable):

| Entity | Does | Key properties |
|---|---|---|
| `logic_counter` | Fires once after N invokes | `required_count`, `uid_to_invoke_on_step`, `reset_after_fire` |
| `logic_delay` | Fires N seconds after invoke | `delay`, `cancel_on_reinvoke` |
| `logic_sequence` | Fires the next uid in a list per invoke | `uids_to_invoke` (comma list), `loop` |
| `logic_random` | Fires one random uid from a list | `uids_to_invoke`, `no_repeat` |
| `logic_repeater` | Fires every N seconds; invoke toggles | `interval`, `enabled`, `fire_on_enable`, `max_fires` |
| `logic_gate` | Passes invokes through only while open | `uid_open`, `uid_close`, `uid_toggle`, `start_open`, `close_after_pass` |
| `light_group_switch` | Switches lights sharing a `light_group` | `light_group`, `mode` (0 toggle, 1 on, 2 off, 3 flicker), `fade_time` |

Lights (`light_tiny` ... `light_xlarge`) take `light_group` and `start_off`. A light switched fully
off is hidden, so it costs no shadow pass.

### 2.4 Worked example: a keycard-gated door (from the sandbox map)

```
"classname" "interact_keycard_switch"
"origin" "1256 424 104"
"angle" "180"
"keycard" "BLUE"
"uid_to_invoke" "bluedoor"
```

1. Place an `interact_keycard_switch` point entity next to the door, set `keycard` to `BLUE` (must match a
   `pickup_keycard` with `type = BLUE` somewhere earlier in the level) and `uid_to_invoke` to a name
   of your choosing, e.g. `bluedoor`.
2. Build the door as a brush, convert it to a `mover_brush` (or `mover_elevator`, etc.),
   and set its `uid` to `bluedoor`.
3. Player picks up the blue keycard → interacts with the switch → the door entity with uid `bluedoor` moves.

### 2.5 Worked example: an elevator

```
"classname" "mover_elevator"
"elevator_name" "test_elevator"
"uid_to_move_to" "test_elevator"
"speed" "2"
"pause_at_top" "3000"
```

1. Build the elevator platform as a brush and convert to `mover_elevator`.
2. Place a `marker_target` at the top stop, and give it a `uid` matching the elevator's
   `uid_to_move_to` value.
3. Set `speed` (units/sec) and `pause_at_top` (ms) to taste. The elevator moves to the marker when the
   player steps on, and returns when they leave — unless `is_remote_triggered` is enabled, in which case
   it only moves when something else (a switch, area_trigger) fires its `uid`.

### 2.6 Enemy type numbers

Several trigger entities (`enemy_spawn`, `enemy_spawner`, `enemy_arena_spawner`)
take a numeric `enemy_type` / `rng_enemy_pool` instead of a string. The mapping is:

| # | Enemy | # | Enemy |
|---|-------|---|-------|
| 1 | Melee | 7 | Spider Mine |
| 2 | Dynamite | 8 | Flying Drone |
| 3 | Arcane | 9 | Lootbot |
| 4 | Shooter | 10 | Flamethrower |
| 5 | Shield | 11 | Gasbot |
| 6 | Achilles | 12 | Spinner |
| 13 | Turret (standing) | 14 | Mine Bot |
| 15 | Minigun | 16 | Knife |
| 17 | Rocket Bot | 18 | Hammer |

13 (turret) only works with `enemy_spawn`. `enemy_spawner` and `enemy_arena_spawner` skip it; place a
`trap_turret` instead.

## 3. Category walkthroughs

Below is one worked example per category, followed by the full reference table in section 4.

### Info / markers (`point/info`)
Non-visual helpers: spawn points, path markers, teleport targets, level config. Every level needs at
least one `info_player_spawn` and one `info_map_data` (controls hub status, disabled weapons, flashlight
default, kill depth, etc. — one per map).

### Lights (`point/lights`)
`light_tiny` / `light_small` / `light_medium` / `light_large` / `light_xlarge` — same properties
(`color`, `energy`, `attenuation`, `fade_begin`, `flicker`, `shadows`), differing only in default
radius/brightness scale. Pick the size closest to what you need, then fine-tune with `energy`/`attenuation`.

### Pickups & powerups (`point/pickup`, `point/powerup`)
Drop-in point entities, no brush needed. Most have zero required properties (`pickup_medkit`,
`pickup_revolver`) — just place and go. A few need one property set: `pickup_keycard` needs `type`
(`RED`/`BLUE`), several powerups take an optional `quad` bool for a 4x variant.

### Enemies (spawned by triggers only)
Enemies are never placed directly; there are no placeable enemy entities. Every fight starts from a
trigger:

- `enemy_spawn`: spawns one `enemy_type` each time it's invoked.
- `enemy_spawner`: spawns `enemy_type` on a timer with concurrent and lifetime caps. Usually placed
  with `enabled` 0 and woken by a trigger. Has `uid_to_invoke_on_spawn` / `uid_to_invoke_on_death`.
- `enemy_arena_spawner`: random or scripted waves; fires `uid_to_invoke` when cleared.

Wire them from a skip-textured `area_trigger` (`is_player_only` 1, `trigger_once` 1) whose
`uid_to_invoke` matches their `uid`. Fixed turrets are traps (`trap_turret`, needs `type`, e.g.
`MINIGUN`), and the training target is `tutorial_dummy`.

### Traps & panels (`point/traps`, `point/panels`)
Environmental hazards/interactables. `trap_bullet` / `trap_rocket`: leave `uid` empty to fire forever
automatically, or set a `uid` so a trigger can fire it on demand. `interact_panel_electrical` specifically
requires the player's revolver alt-fire (electric shot) to activate — don't wire it to a generic trigger
expecting normal damage.

### Switches (`point/switches`)
`interact_switch` (wall-mounted), `interact_floor_switch` (floor plate), `interact_keycard_switch` (needs matching
`pickup_keycard`). All fire via `uid_to_invoke`.

### Triggers, point flavor (`point/triggers`)
Fire-and-forget logic nodes with no brush volume — sounds, explosions, scene loads, enemy spawns, menu
screens. Reference by `uid`, fired by an area/switch pointing `uid_to_invoke` at that uid.

### Destructables (`point/destructables`, `solid/destructable`)
Two flavors: prefab point entities (`destructable_vent` — just
place them) and brush-based (`destructable_light`,
`destructable_dynamic`, `destructable_vending_machine` — build the brush shape
first, then convert). `destructable_dynamic` additionally takes a `destruction_mode` enum controlling
*how* it can be destroyed (default/charged-melee-only/ground-slam-only/destroy-on-bump).

### Decor (`point/decor`)
Pure set dressing — plants, trees, alarm lights, security cameras, fireflies. Almost none need
properties; place and forget.

### Audio (`point/audio`, `solid/audio`)
`audio_ambient_sound` is a point entity that plays positional 3D looping background audio under the `ambience` bus, automatically searching the audio file tree by file name (`sound_name`). `audio_loop` for a fixed-position ambient loop (`path` must be a full `res://assets/audio/...ogg` path). `audio_attached` attaches 3D audio to a moving brush or target node. `audio_slider` follows a `marker_path` sequence — good for moving-water sound sources. `audio_ambient_area` is brush-based, playing from a comma-separated list of ambient sound names.

### Areas & volumes (`solid/info`, `solid/triggers`)
Build a brush, convert to class. Covers water/fluid hazards (`water_transparent`,
`water_flowing`, `area_dangerous_fluid`), hurtboxes, achievement/secret unlocks, weather zones, and the generic
`area_trigger` used everywhere for gameplay logic (see 2.3–2.4 above).

### Moving brushes & platforms (`solid/triggers`, `solid/platforms`)
`mover_brush` (one-shot or toggleable move), `mover_loop` /
`mover_loop_smooth` (repeat forever), `mover_elevator` (player-riding platform),
`mover_rotate_x` / `mover_rotate_y` / `mover_rotate_z` (continuous rotation, no trigger needed — `speed`, `direction`),
`mover_crusher` (configurable lethal crushing block, no trigger needed).

### Model-point (`model_point`)
`trap_train` — a point entity backed by a moving model rather than a static prop; give it a `uid_to_move_to`
pointing at a `marker_path` or `marker_target` sequence to define its route.

## 4. Full entity reference (163 classes)

Grouped by category, the same folders TrenchBroom shows. Open the entity's `.tres` under
`trenchbroom/fgd/` (or check TrenchBroom's Entity Inspector) for every property and default.

### area

| Classname | Type | Purpose |
|---|---|---|
| `area_achievement_unlock` | brush | Unlocks a Steam/in-game achievement (`achievement_name`) on entry. |
| `area_arena` | brush | Requires all enemies inside to die before firing its `uid_to_invoke` on exit. |
| `area_dangerous_fluid` | brush | Acid/lava/arctic hazard (`fluid_type` 0=acid/1=lava/2=arctic); `instant_kill` toggle, `enable_sound` toggle, optional `teleport_name` respawn. |
| `area_electric_panel` | brush | Like `area_trigger`, but only activates on the revolver's electric special-fire. |
| `area_hurtbox` | brush | Generic damage/effect volume for hazards and traps. |
| `area_jump_pad` | brush | Launches entities that touch it (`jump_force`). |
| `area_killbox` | brush | Lethal trigger volume that instantly kills the player and removes all their speed and momentum upon contact. |
| `area_remove_ammo` | brush | Strips ammo on entry (pairs with `logic_remove_ammo` logic). |
| `area_remove_weapons` | brush | Removes both weapons and all ammo when the player enters, then deletes itself. |
| `area_secret_unlock` | brush | Unlocks a secret by `secret_int` id on entry. |
| `area_toggle_bool` | brush | Sets a named save-state boolean to true on entry (auto-saves). |
| `area_trigger` | brush | General-purpose trigger volume; `is_button`/`is_player_only`/`keycard`/`ms_timeout`/`trigger_once`/`uid_to_invoke`/`emit_bus`. See 2.3–2.4. |
| `area_weather` | brush | Sets local weather (`weather_type`: 0 none / 1 rain / 2 snow / 3 ash / 4 sandstorm). |

### audio

| Classname | Type | Purpose |
|---|---|---|
| `audio_ambient_area` | brush | Plays one of a comma-separated `ambient_names` list to anything inside the brush volume. |
| `audio_ambient_sound` | point | Positional 3D ambient audio point player with recursive filename search under the ambience bus. |
| `audio_attached` | point | 3D audio player that attaches and follows a named target node at runtime. |
| `audio_loop` | point | Positional 3D audio loop that plays continuously on map load, with distance falloff. |
| `audio_room_tone` | brush | Fades in looping ambient audio (`audio_name`) on the ambience bus when the player enters the brush volume, and fades out on exit (`fade_time`, `volume_db`, `pitch_scale`). |
| `audio_slider` | point | Audio node that follows a named path and slides along it (e.g. rivers). |
| `audio_sound` | point | Plays `audio_path` (2D or 3D) when its `uid` is fired; supports `ms_delay`. |

### decor

| Classname | Type | Purpose |
|---|---|---|
| `decor_cat` | point | A decorative cat model |
| `decor_fireflies` | point | Ambient flying bug swarm. |
| `decor_graffiti` | brush | Wall graffiti projection brush (`flip_x`). Automatically selects graffiti texture (0-17) based on brush world position; non-destructible; avoids decal limits (32 max). |
| `decor_plant_bush_1` | point | Decorative bush used for ground cover and scene composition. |
| `decor_plant_bush_2` | point | Decorative bush used for ground cover and scene composition. |
| `decor_plant_tropical_1` | point | Decorative tropical plant instance used for level decoration and atmosphere. |
| `decor_plant_tropical_2` | point | Decorative tropical plant instance used for level decoration and atmosphere. |
| `decor_plant_tropical_3` | point | Decorative tropical plant instance used for level decoration and atmosphere. |
| `decor_plant_tropical_4` | point | Decorative tropical plant instance used for level decoration and atmosphere. |
| `decor_plant_tropical_5` | point | Decorative tropical plant instance used for level decoration and atmosphere. |
| `decor_plant_tropical_6` | point | Decorative tropical plant instance used for level decoration and atmosphere. |
| `decor_plant_tropical_7` | point | Decorative tropical plant instance used for level decoration and atmosphere. |
| `decor_plant_tropical_8` | point | Decorative tropical plant instance used for level decoration and atmosphere. |
| `decor_security_camera` | point | Decorative security camera. |
| `decor_small_tree_rt_1` | point | Small rotating decorative tree. |
| `decor_tree_rt_1` | point | Rotating tree instance used as decorative foliage and light cover in levels. |
| `decor_tree_rt_2` | point | Rotating tree instance used as decorative foliage and light cover in levels. |
| `decor_tree_rt_2_1` | point | Rotating tree decorative instance used for foliage and occlusion. |
| `decor_tree_rt_3` | point | Rotating tree instance used as decorative foliage and light cover in levels. |
| `decor_tree_rt_4` | point | Rotating tree instance used as decorative foliage and light cover in levels. |
| `decor_vegetation_bush` | point | Vegetation bush. Automatically selects one of 8 bush models (tree_pack/bushXX) based on world position, so placement is deterministic across builds. |
| `decor_vegetation_tree` | point | Vegetation tree. Automatically selects one of 36 tree models (tree_pack/treeXX) based on world position, so placement is deterministic across builds. |
| `decor_waving_guy` | point | A tall, slender humanoid waving guy silhouette |

### destructable

| Classname | Type | Purpose |
|---|---|---|
| `destructable_barrel` | brush | Explosive/breakable barrel (`godmode` makes it indestructible but still explodes when hit). |
| `destructable_control_panel` | brush | Destructible control panel; fires `uid_to_invoke` when broken. |
| `destructable_dynamic` | brush | Generic destructible; `destruction_mode` = 0 default / 1 charged-melee-only / 2 ground-slam-only / 3 destroy-on-bump. Pieces/audio follow the texture prefix — glass windows are `destructable_dynamic` with a `G*` texture (glass impact FX + shatter overlay) and `freeze 1`. |
| `destructable_fan` | point | Destructible spinning ceiling fan (`spin_speed`, `uid_to_invoke`). |
| `destructable_light` | brush | Breakable light fixture. |
| `destructable_vending_machine` | brush | Destructible vending machine that spits out health/armor pickups. |
| `destructable_vent` | point | Breakable vent prefab. |

### display

| Classname | Type | Purpose |
|---|---|---|
| `display_poster` | brush | CRT-styled digital poster display (`poster_dir`, `poster_index`, `deterministic`, `destroyable`, `flip_x`, `flip_y`, `glow_intensity`, `enable_light`). |
| `display_screen` | brush | CRT-styled brush display (heading/subtext, font sizes, `destroyable`, `flip_text`). |
| `display_screen_tips` | point | Display screen showing general tutorial tips. |
| `display_sign` | point | Sign with custom `text`. |

### effect

| Classname | Type | Purpose |
|---|---|---|
| `effect_explosion` | point | Spawns one or more explosions at this point (`explosion_type`, `count`, `ms_delay`). |
| `effect_no_rain` | brush | Suppresses the rain particle effect inside (sheltered zones). |
| `effect_particle_collision` | brush | Volume that handles particle-system collision interactions. |
| `effect_particle_loop` | point | Continuously plays a particle effect/scene at its location (`path`). |

### enemy

| Classname | Type | Purpose |
|---|---|---|
| `enemy_arena_spawner` | point | Wave-based arena spawner (`rng_*` or `static_spawn` waves); fires `uid_to_invoke` when cleared. |
| `enemy_spawn` | point | Spawns a named `enemy_type` when triggered. |
| `enemy_spawner` | point | Periodically spawns one `enemy_type` up to a concurrent/lifetime cap. |

### info

| Classname | Type | Purpose |
|---|---|---|
| `info_cinecam` | point | Cinematic camera viewpoint used during round complete when End Map Replay is disabled. Cycles through placed cinecams to showcase scenic areas of the map. |
| `info_environment` | point | Sets environment/skybox/sun preset (`environment` index 1/5/6/7/8/9) plus sun color/direction/energy, with optional `random_earthquakes` toggle (20s–45s interval). |
| `info_map_data` | point | Per-level config: hub flag, disabled weapons, default flashlight, disabled music, kill depth, start VO. One per map. |
| `info_player_spawn` | point | Player spawn/respawn point. One (or more) required per map. |

### interact

| Classname | Type | Purpose |
|---|---|---|
| `interact_floor_switch` | point | Floor-plate variant of the above. |
| `interact_keycard_switch` | point | Requires matching `keycard` type to fire `uid_to_invoke`. |
| `interact_panel_destructive` | point | Destroy this panel to fire `uid_to_invoke`. |
| `interact_panel_electrical` | point | Must be shot with the revolver's electric special-fire to fire `uid_to_invoke`. |
| `interact_panel_fuse` | point | Fuse-collectible panel that activates portals; give it a `uid_to_invoke`. |
| `interact_switch` | point | Wall switch; fires `uid_to_invoke` (optional `oneshot`). |
| `interact_trigger` | point | generic press-E interactable with no model or state of its own; fires target(s) by name |

### light

| Classname | Type | Purpose |
|---|---|---|
| `light_alarm` | point | Rotating industrial sweep alarm beacon, toggleable. |
| `light_blinking` | brush | Blinking computer / indicator light brush with zero-emission unshaded texture. Overlays a white texture, recolors to specified color with 2x HDR overclock when lit, and goes black when off. |
| `light_group_switch` | point | Switches every light (light_tiny ... light_xlarge) whose light_group matches. |
| `light_large` | point | Adjustable light. Takes `color`, `energy`, `flicker`, `shadows`, `light_group`, `start_off`. |
| `light_medium` | point | Adjustable light. Takes `color`, `energy`, `flicker`, `shadows`, `light_group`, `start_off`. |
| `light_small` | point | Adjustable light. Takes `color`, `energy`, `flicker`, `shadows`, `light_group`, `start_off`. |
| `light_tiny` | point | Adjustable light. Takes `color`, `energy`, `flicker`, `shadows`, `light_group`, `start_off`. |
| `light_xlarge` | point | Adjustable light. Takes `color`, `energy`, `flicker`, `shadows`, `light_group`, `start_off`. |

### logic

| Classname | Type | Purpose |
|---|---|---|
| `logic_achievement_unlock` | point | Unlocks an achievement when invoked by another trigger or destructible. |
| `logic_bool_relay` | point | Reads a save-state boolean; if true, immediately fires the named trigger on ready. |
| `logic_counter` | point | Counts incoming invoke() calls and invokes uid_to_invoke once required_count is reached. |
| `logic_delay` | point | Invokes uid_to_invoke a set number of seconds after being invoked. Pauses with the game. |
| `logic_gate` | point | Passes invokes through to uid_to_invoke only while open. Other entities open/close it through its control uids. Combine with logic_counter for AND/OR logic. |
| `logic_load_scene` | point | Transitions to a new scene when triggered by an adjacent area. |
| `logic_menu` | point | Opens a specific menu screen by `menu_type` index (see `.tres` for the full enum). |
| `logic_new_scene` | point | Instantiates a scene at `scene_path` when triggered. |
| `logic_random` | point | Each invoke fires one uid picked at random from a comma-separated list. |
| `logic_remove_ammo` | point | Strips revolver/shotgun ammo when triggered. |
| `logic_remove_guns` | point | Strips revolver/shotgun weapons when triggered. |
| `logic_repeater` | point | Invokes uid_to_invoke on a fixed interval while enabled. Invoking the repeater toggles it on/off. Pauses with the game. |
| `logic_sequence` | point | Each invoke fires the next uid in a comma-separated list, in order. |
| `logic_terminal_email` | point | Adds a persisted email to the lounge terminal inbox; usually fired via an `area_trigger`'s `uid_to_invoke`. |
| `logic_toggle_bool` | point | Sets a named save-state boolean to true when invoked (auto-saves). |

### lounge

| Classname | Type | Purpose |
|---|---|---|
| `lounge_area_unlock_screen` | point | Display screen showing area-unlock details (`area_name`). |
| `lounge_cpu_cluster` | point | Press-[E] trigger for the CPU Cluster Screen. Prompts 'Verify CPU Cluster' to launch the scrolling per-level CPU verification display, and prompts 'Open Door' to invoke uid_to_invoke once all 15 alpha-charlie CPUs are verified. |
| `lounge_cpu_cluster_screen` | point | Interactive display screen that tracks all 15 alpha-charlie sector CPUs, plays a scrolling per-level verification visualization when pressing [E] ('Verify CPU Cluster'), and triggers 'Open Door' when all 15 alpha-charlie CPUs are collected. |
| `lounge_level_details_screen` | point | Display screen showing level details (`level_name`). |
| `lounge_radio` | point | Destructible lounge radio that plays music. |
| `lounge_sector_terminal` | point | Interactive sector terminal that displays level details and cycles through a sector's maps to tune a linked interactive portal. |
| `lounge_starfield` | brush | Solid brush entity that renders an opaque cosmic starfield shader with infinite parallax depth on its surfaces. |
| `lounge_terminal` | point | Interactable terminal for stats/email/progress. |

### marker

| Classname | Type | Purpose |
|---|---|---|
| `marker_path` | point | Named ordered path point for moving platforms/entities. |
| `marker_path_simulator` | point | Debug helper that visually follows a `marker_path` sequence. |
| `marker_target` | point | Named reference marker (elevators, some triggers). |
| `marker_teleport` | point | Named safe teleport destination (e.g. for water hazards). |

### mover

| Classname | Type | Purpose |
|---|---|---|
| `mover_brush` | brush | Moves a brush `move_distance` along `direction` at `move_speed` when triggered; `toggleable` for back-and-forth. |
| `mover_crusher` | brush | Configurable crushing block: extends to a `uid_to_move_to` marker, becomes lethal at max extension, retracts back to start, repeats; supports `fake_crush_count` fake-out jabs plus delay/pause tuning. No trigger required. |
| `mover_elevator` | brush | Rides the player to a `marker_target` and back; see 2.5. |
| `mover_explosion_force` | brush | Frozen brush that gets a physics impulse (`direction`, `direction_spread`, `force`, `force_variation`, `torque_scale`) when triggered. |
| `mover_loop` | brush | Loops back to start instantly on reaching `move_distance`. |
| `mover_loop_smooth` | brush | Same, but loops back smoothly rather than teleporting. |
| `mover_rotate_x` | brush | Continuously rotates the brush group on the X axis (`speed`, `direction`), no trigger required. |
| `mover_rotate_y` | brush | Continuously rotates the brush group on the Y axis (`speed`, `direction`), no trigger required. |
| `mover_rotate_z` | brush | Continuously rotates the brush group on the Z axis (`speed`, `direction`), no trigger required. |

### pickup

| Classname | Type | Purpose |
|---|---|---|
| `pickup_ammo_permanent_revolver` | point | Persistent ammo pickup for revolver |
| `pickup_ammo_permanent_shotgun` | point | Persistent ammo pickup for shotgun. |
| `pickup_ammo_revolver` | point | Revolver ammo pickup that replenishes the player's revolver ammunition by a configured amount. |
| `pickup_ammo_shotgun` | point | Shotgun ammo pickup that replenishes shotgun shells for the player. |
| `pickup_computer_chip` | point | Collectible for puzzles/objectives. |
| `pickup_fuse` | point | Fuse collectible for portal activation. |
| `pickup_keycard` | point | Keycard collectible; set `type` (`RED`/`BLUE`) to match gated doors/switches. |
| `pickup_medkit` | point | Standard medkit pickup that restores player health when collected. |
| `pickup_medkit_permanent` | point | Persistent medkit that heals the player. |
| `pickup_revolver` | point | Revolver weapon pickup that grants the player the revolver and its starting ammunition when collected. |
| `pickup_scrap` | point | Scrap collectible picked up by the player. |
| `pickup_shotgun` | point | Shotgun weapon pickup that gives the player a shotgun and initial shells upon collection. |

### portal

| Classname | Type | Purpose |
|---|---|---|
| `portal_full_scene` | point | Framed portal that loads another `.map` by name (`map_name`, `enable_fuses`, `use_round_complete_screen`). |
| `portal_interactive` | point | Interactive sector portal that can be tuned remotely by a terminal, turning off and re-igniting with SFX and particles when its destination changes. |
| `portal_scene` | point | Unframed portal loading another `.map` by name; same properties as above. |

### powerup

| Classname | Type | Purpose |
|---|---|---|
| `powerup_adrenaline` | point | Temporary speed + fire-rate boost. |
| `powerup_arcane_chainsaw` | point | Temporary melee weapon/enhanced melee damage. |
| `powerup_armor` | point | Temporary damage reduction/armor points. |
| `powerup_bullet_bounce` | point | Temporary projectile ricochet (`quad` variant available). |
| `powerup_bullet_choke` | point | Temporary spread/damage modifier (`quad` variant available). |
| `powerup_revolver_head_burster` | point | Revolver Head Burster mod. |
| `powerup_revolver_storm` | point | Temporary rapid/multi-shot revolver fire. |
| `powerup_revolver_vampire` | point | Revolver Vampire Shots mod. |
| `powerup_shotgun_dragonfire` | point | Shotgun shells that ignite enemies on hit. |
| `powerup_shotgun_kinetic_battery` | point | Next ground slam: 10x damage, 2x AoE. |

### trap

| Classname | Type | Purpose |
|---|---|---|
| `trap_bullet` | point | Fires bullets on a loop, or on-demand if given a `uid`. |
| `trap_landmine` | point | Explodes when the player steps on it. |
| `trap_laser` | point | Continuous damaging laser. |
| `trap_rocket` | point | Fires rockets (homing disabled) on a loop, or on-demand if given a `uid`. |
| `trap_steam` | point | Steam hazard. |
| `trap_steam_valve` | point | Destructible valve that spawns a `trap_steam`. |
| `trap_train` | model | Moving train hazard that follows a target path or marker. |
| `trap_turret` | point | Fixed turret hazard (`type`, e.g. `MINIGUN`). Placed directly like other traps. |

### tutorial

| Classname | Type | Purpose |
|---|---|---|
| `tutorial_control_prompt` | brush | Shows an on-screen control prompt while the player is inside (`action_display`, `action_name`, `context`). |
| `tutorial_dummy` | point | Training target dummy for tutorial and hub areas. |
| `tutorial_marker` | point | Pulsing holographic floor ring highlighting tutorial areas. |
| `tutorial_screen` | point | Display screen showing the combat tutorial. |
| `tutorial_screen_companion` | point | Display screen showing dynamic keybind instructions during the combat tutorial. |

### water

| Classname | Type | Purpose |
|---|---|---|
| `water_endless` | point | Point-placed water. The point's position is exactly where the water surface sits — the water spawns as one giant plane centered on that point and fades into the distance with a fake horizon curvature, giving the feeling of an endless body of water. Uses the same enter/exit submersion behavior (screen tint, splash sound, is-in-water state) as regular water. |
| `water_flowing` | brush | Moving water hazard (`direction`, `fog_color`, `instant_kill`, `teleport_name`). |
| `water_transparent` | brush | Standard water hazard (`color`, `subdivisions`, `instant_kill`, `teleport_name`). |

### world

| Classname | Type | Purpose |
|---|---|---|
| `world_crystal` | brush | Glowing crystal structure brush. Quake-style procedural crystal shader at 32 texels/m (pixel-snapped base-to-tip gradient over the group height, no dither, per-face shade, brush texture screen-blended at 0.9, subtle animated rising light band + facet glint on top). Semi-translucent, no light (`color` default cyan, `glow`, `opacity` default 0.85). |
| `world_footstep_surface` | brush | Marks a surface as queryable for footstep sound selection. |
| `world_ice` | brush | Static solid that reduces player traction (slippery). |
| `world_player_clip` | brush | Invisible barrier that blocks the player from entering an area, but allows projectiles and enemies to pass through. |
| `world_refraction_glass` | brush | Standard refraction glass brush. Frosted, ribbed glass that blurs what is behind it. The look is fixed so all glass in the game matches. |
| `world_window` | brush | Marks a brush as an indestructible window. |

## 5. Troubleshooting

- **New entity class doesn't show up in TrenchBroom.** You edited/added a `.tres` under
  `trenchbroom/fgd/` but forgot to re-export. Re-run 1.2 (Export GameConfig), then restart TrenchBroom
  or reopen the map so it reloads the `.fgd`.
- **Entity class exists in the `.tres` folder but you can't find it in TrenchBroom at all.** Check
  it's actually listed in `entity_definitions` inside `trenchbroom/fpsdevkit_fgd.tres` — a `.tres` file
  sitting in `trenchbroom/fgd/` is not automatically included; it has to be added as an `ext_resource`
  and appended to that array. (`portal_teleport` and `pickup_arc` are examples of `.tres` files
  that currently exist but are *not* wired into the shipped FGD — they won't appear in TrenchBroom
  until someone adds them to the array.)
- **Trigger/switch does nothing in-game.** Double-check the `uid` on the receiving entity exactly
  matches the `uid_to_invoke` string on the firing entity — this is plain string matching,
  case-sensitive, no fuzzy lookup.
- **Textures/models missing in TrenchBroom.** Re-check the Game Path from step 1.3 — it must point at
  `trenchbroom/` (not the repo root), since that's where `textures/` and `models/` live.
