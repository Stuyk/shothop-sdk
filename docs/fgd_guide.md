# TrenchBroom + Shothop FGD Guide

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

### 2.1 Point entities vs. solid (brush) entities vs. model-point entities

The FGD has three kinds of classes, and TrenchBroom treats each differently:

- **Point entities** (`point/...`, e.g. `pickup_medkit`, `light_large`, `trigger_sound`) — a single
  origin with no geometry of their own. Place with the **Entity Browser** (View → Entity Browser, or
  the icon in the top toolbar): drag the class onto the 3D/2D view, or select it and press Enter.
  Some point classes render a preview model in the editor (anything backed by a `scene_file`, e.g.
  `pickup_medkit`, most `enemy_*`, most `decor_*`); others show as a plain bounding-box gizmo
  (anything without a scene, e.g. `info_marker_target`, `trigger_sound`).
- **Solid entities** (`solid/...`, e.g. `trigger_area`, `func_group`, `destructable_dynamic`) — you build
  one or more brushes first (Brush Tool, or drag from a primitive), select them, then right-click →
  **Move Brushes to Entity → <classname>** (or press the assigned key after selecting the class in the
  Entity Browser while brushes are selected). The brush geometry itself becomes the entity's shape/trigger
  volume/collision.
- **Model-point entities** (`model_point/...`, currently just `hazard_train`) — like point entities, but
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
movement waypoint, e.g. `info_marker_target`).

Example, straight from `trenchbroom/maps/dev_sandbox.map`:

```
// A door switch that invokes something with uid "switch_test" when used
"classname" "door_switch"
"origin" "1256 -56 88"
"angle" "180"
"uid_to_invoke" "switch_test"
```

Give the door/light/trigger you want opened a matching `uid` of `switch_test`, and the switch will
fire it. This same pattern powers `keycard_switch`, `trigger_area`, `trigger_elevator`,
`trigger_move_brush`, and most `trap_*` / `trigger_*` entities — look for `uid_to_invoke` on the
"cause" and `uid` on the "effect."

### 2.4 Worked example: a keycard-gated door (from the sandbox map)

```
"classname" "keycard_switch"
"origin" "1256 424 104"
"angle" "180"
"keycard" "BLUE"
"uid_to_invoke" "bluedoor"
```

1. Place a `keycard_switch` point entity next to the door, set `keycard` to `BLUE` (must match a
   `pickup_keycard` with `type = BLUE` somewhere earlier in the level) and `uid_to_invoke` to a name
   of your choosing, e.g. `bluedoor`.
2. Build the door as a brush, convert it to a `trigger_move_brush` (or `trigger_elevator`, etc.),
   and set its `uid` to `bluedoor`.
3. Player picks up the blue keycard → interacts with the switch → the door entity with uid `bluedoor` moves.

### 2.5 Worked example: an elevator

```
"classname" "trigger_elevator"
"elevator_name" "test_elevator"
"uid_to_move_to" "test_elevator"
"speed" "2"
"pause_at_top" "3000"
```

1. Build the elevator platform as a brush and convert to `trigger_elevator`.
2. Place an `info_marker_target` at the top stop, and give it a `uid` matching the elevator's
   `uid_to_move_to` value.
3. Set `speed` (units/sec) and `pause_at_top` (ms) to taste. The elevator moves to the marker when the
   player steps on, and returns when they leave — unless `is_remote_triggered` is enabled, in which case
   it only moves when something else (a switch, trigger_area) fires its `uid`.

### 2.6 Enemy type numbers

Several trigger entities (`trigger_new_enemy`, `trigger_enemy_spawner`, `trigger_enemy_arena_spawner`)
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

### Enemies (`point/enemy`)
Place directly; most have no required properties. `enemy_turret` needs `type` (e.g. `MINIGUN`,
`SHOTGUN`). Pair with `trigger_area` or `trigger_new_enemy` if you want spawn-on-trigger rather than
spawn-on-load.

### Traps & panels (`point/traps`, `point/panels`)
Environmental hazards/interactables. `trap_bullet` / `trap_rocket`: leave `uid` empty to fire forever
automatically, or set a `uid` so a trigger can fire it on demand. `panel_electrical` specifically
requires the player's revolver alt-fire (electric shot) to activate — don't wire it to a generic trigger
expecting normal damage.

### Switches (`point/switches`)
`door_switch` (wall-mounted), `door_switch_floor` (floor plate), `keycard_switch` (needs matching
`pickup_keycard`). All fire via `uid_to_invoke`.

### Triggers, point flavor (`point/triggers`)
Fire-and-forget logic nodes with no brush volume — sounds, explosions, scene loads, enemy spawns, menu
screens. Reference by `uid`, fired by an area/switch pointing `uid_to_invoke` at that uid.

### Destructables (`point/destructables`, `solid/destructable`)
Two flavors: prefab point entities (`destructable_prefab_vent` — just
place them) and brush-based (`destructable_light`,
`destructable_dynamic`, `destructable_vending_machine` — build the brush shape
first, then convert). `destructable_dynamic` additionally takes a `destruction_mode` enum controlling
*how* it can be destroyed (default/charged-melee-only/ground-slam-only/destroy-on-bump).

### Decor (`point/decor`)
Pure set dressing — plants, trees, alarm lights, security cameras, fireflies. Almost none need
properties; place and forget.

### Audio (`point/audio`, `solid/audio`)
`audio_ambient_sound` is a point entity that plays positional 3D looping background audio under the `ambience` bus, automatically searching the audio file tree by file name (`sound_name`). `audio_loop` for a fixed-position ambient loop (`path` must be a full `res://assets/audio/...ogg` path). `audio_attached` attaches 3D audio to a moving brush or target node. `audio_slider` follows an `info_marker_path` sequence — good for moving-water sound sources. `audio_ambient_area` is brush-based, playing from a comma-separated list of ambient sound names.

### Areas & volumes (`solid/info`, `solid/triggers`)
Build a brush, convert to class. Covers water/fluid hazards (`info_water_transparent`,
`info_water_flowing`, `info_dangerous_fluid`), fog volumes (`info_area_fog_volume` — exposed in the FGD
as `info_fog_volume`), hurtboxes, achievement/secret unlocks, weather zones, and the generic
`trigger_area` used everywhere for gameplay logic (see 2.3–2.4 above).

### Moving brushes & platforms (`solid/triggers`, `solid/platforms`)
`trigger_move_brush` (one-shot or toggleable move), `trigger_looping_moveable_brush` /
`trigger_looping_moveable_smooth_brush` (repeat forever), `trigger_elevator` (player-riding platform),
`platform_rotate_x` / `platform_rotate_y` / `platform_rotate_z` (continuous rotation, no trigger needed — `speed`, `direction`),
`platform_crusher` (configurable lethal crushing block, no trigger needed).

### Model-point (`model_point`)
`hazard_train` — a point entity backed by a moving model rather than a static prop; give it a `uid_to_move_to`
pointing at an `info_marker_path` or `info_marker_target` sequence to define its route.

## 4. Full entity reference (149 classes)

Grouped by folder to match `trenchbroom/fgd/`. Click through to a `.tres` file for the exact property
list and defaults (also visible live in TrenchBroom's Entity Inspector).

### model_point

| Classname | Purpose |
|---|---|
| `hazard_train` | Moving train hazard that follows a target path or marker. |

### point/audio

| Classname | Purpose |
|---|---|
| `audio_ambient_sound` | Positional 3D ambient audio point player with recursive filename search under the ambience bus. |
| `audio_attached` | 3D audio player that attaches and follows a named target node at runtime. |
| `audio_loop` | Positional 3D audio loop that plays continuously on map load, with distance falloff. |
| `audio_slider` | Audio node that follows a named path and slides along it (e.g. rivers). |

### point/decor

| Classname | Purpose |
|---|---|
| `SecurityCamera` | Decorative security camera. |
| `alarm_light` | Rotating industrial sweep alarm beacon, toggleable. |
| `decor_fireflies` | Ambient flying bug swarm. |
| `decor_plant_bush_1` / `decor_plant_bush_2` | Decorative ground-cover bush. |
| `decor_plant_tropical_1`–`8` | Decorative tropical plant instances. |
| `decor_small_tree_rt_1` | Small rotating decorative tree. |
| `decor_tree_rt_1` / `_2` / `_2_1` / `_3` / `_4` | Rotating decorative trees (foliage/light cover). |
| `destructable_fan` | Destructible spinning ceiling fan (`spin_speed`, `uid_to_invoke`). |

### point/destructables

| Classname | Purpose |
|---|---|
| `destructable_prefab_vent` | Breakable vent prefab. |

### point/enemy

| Classname | Purpose |
|---|---|
| `enemy_achilles` | Light melee enemy that patrols and collides with the player. |
| `enemy_arcane` | Special enemy using magical projectiles/unique behaviors. |
| `enemy_drone` | Flying enemy that maneuvers to pursue/attack. |
| `enemy_dynamite` | Explosive enemy, detonates after delay or on contact. |
| `enemy_flamethrower` | Flamethrower robot. |
| `enemy_gas` | Emits poisonous gas to damage/slow nearby players. |
| `enemy_hammer` | Heavy sledgehammer robot that sprints in and lands one big swing up close; damage stuns it. |
| `enemy_knife` | Agile hovering knife robot that circles the player and performs high-speed dash attacks. |
| `enemy_loot` | Drops items when defeated. |
| `enemy_melee` | Close-range melee attacker. |
| `enemy_mine_bot` | Mine bot enemy that wanders, drops landmines, and becomes a proximity bomb. |
| `enemy_minigun` | Heavy robot with twin miniguns that sprays a continuous stream of bullets. |
| `enemy_rocket_bot` | Treaded robot that kites the player (retreats when close, strafes at range) and fires tracking rockets from its right-arm launcher. |
| `enemy_shield` | Has a shield reducing/blocking damage until disabled. |
| `enemy_shooter` | Ranged enemy that aims and fires projectiles. |
| `enemy_spider_mine` | Proximity mine, detonates near the player. |
| `enemy_spinner` | Patrols, sprays 360° low bullets. |
| `enemy_turret` | Stationary turret; `type` selects firing behavior (e.g. `MINIGUN`, `SHOTGUN`). |
| `enemy_tutorial_dummy` | Target-practice dummy that reports weapon hit types. |

### point/info

| Classname | Purpose |
|---|---|
| `info_display_screen_tips` | Display screen showing general tutorial tips. |
| `info_display_screen_tutorial` | Display screen showing the combat tutorial. |
| `info_display_screen_tutorial_companion` | Display screen showing dynamic keybind instructions during the combat tutorial. |
| `info_environment` | Sets environment/skybox/sun preset (`environment` index 1/5/6/7/8/9) plus sun color/direction/energy, with optional `random_earthquakes` toggle (20s–45s interval). |
| `info_lounge_radio` | Destructible lounge radio that plays music. |
| `info_lounge_terminal` | Interactable terminal for stats/email/progress. |
| `info_map_data` | Per-level config: hub flag, disabled weapons, default flashlight, disabled music, kill depth, start VO. One per map. |
| `info_marker_path` | Named ordered path point for moving platforms/entities. |
| `info_marker_target` | Named reference marker (elevators, some triggers). |
| `info_marker_teleport` | Named safe teleport destination (e.g. for water hazards). |
| `info_particle_loop` | Continuously plays a particle effect/scene at its location (`path`). |
| `info_path_simulator` | Debug helper that visually follows an `info_marker_path` sequence. |
| `info_player_spawn` | Player spawn/respawn point. One (or more) required per map. |
| `info_portal_full_scene` | Framed portal that loads another `.map` by name (`map_name`, `enable_fuses`, `use_round_complete_screen`). |
| `info_portal_scene` | Unframed portal loading another `.map` by name; same properties as above. |
| `info_screen_area_unlock` | Display screen showing area-unlock details (`area_name`). |
| `info_screen_level_details` | Display screen showing level details (`level_name`). |
| `info_sign` | Sign with custom `text`. |
| `info_tutorial_marker` | Pulsing holographic floor ring highlighting tutorial areas. |

### point/lights

| Classname | Purpose |
|---|---|
| `light_tiny` / `light_small` / `light_medium` / `light_large` / `light_xlarge` | Adjustable point light (`color`, `energy`, `attenuation`, `fade_begin`, `flicker`, `shadows`); size picks default scale. |

### point/panels

| Classname | Purpose |
|---|---|
| `panel_destructive` | Destroy this panel to fire `uid_to_invoke`. |
| `panel_electrical` | Must be shot with the revolver's electric special-fire to fire `uid_to_invoke`. |
| `panel_fuse` | Fuse-collectible panel that activates portals; give it a `uid_to_invoke`. |

### point/pickup

| Classname | Purpose |
|---|---|
| `pickup_ammo_permanent_revolver` / `pickup_ammo_permanent_shotgun` | Persistent ammo pickups. |
| `pickup_ammo_revolver` / `pickup_ammo_shotgun` | Standard ammo refill pickups (shotgun variant supports `quad`). |
| `pickup_computer_chip` | Collectible for puzzles/objectives. |
| `pickup_fuse` | Fuse collectible for portal activation. |
| `pickup_keycard` | Keycard collectible; set `type` (`RED`/`BLUE`) to match gated doors/switches. |
| `pickup_medkit` / `pickup_medkit_permanent` | Health pickups (standard / persistent). |
| `pickup_revolver` / `pickup_shotgun` | Weapon pickups granting the gun + starting ammo. |

### point/powerup

| Classname | Purpose |
|---|---|
| `powerup_adrenaline` | Temporary speed + fire-rate boost. |
| `powerup_arcane_chainsaw` | Temporary melee weapon/enhanced melee damage. |
| `powerup_armor` | Temporary damage reduction/armor points. |
| `powerup_bullet_bounce` | Temporary projectile ricochet (`quad` variant available). |
| `powerup_bullet_choke` | Temporary spread/damage modifier (`quad` variant available). |
| `powerup_revolver_head_burster` | Revolver Head Burster mod. |
| `powerup_revolver_storm` | Temporary rapid/multi-shot revolver fire. |
| `powerup_revolver_vampire` | Revolver Vampire Shots mod. |
| `powerup_shotgun_dragonfire` | Shotgun shells that ignite enemies on hit. |
| `powerup_shotgun_kinetic_battery` | Next ground slam: 10x damage, 2x AoE. |

### point/switches

| Classname | Purpose |
|---|---|
| `door_switch` | Wall switch; fires `uid_to_invoke` (optional `oneshot`). |
| `door_switch_floor` | Floor-plate variant of the above. |
| `keycard_switch` | Requires matching `keycard` type to fire `uid_to_invoke`. |

### point/traps

| Classname | Purpose |
|---|---|
| `trap_bullet` | Fires bullets on a loop, or on-demand if given a `uid`. |
| `trap_landmine` | Explodes when the player steps on it. |
| `trap_laser` | Continuous damaging laser. |
| `trap_rocket` | Fires rockets (homing disabled) on a loop, or on-demand if given a `uid`. |
| `trap_steam` | Steam hazard. |
| `trap_steam_valve` | Destructible valve that spawns a `trap_steam`. |

### point/triggers

| Classname | Purpose |
|---|---|
| `trigger_bool_relay` | Reads a save-state boolean; if true, immediately fires the named trigger on ready. |
| `trigger_enemy_arena_spawner` | Wave-based arena spawner (`rng_*` or `static_spawn` waves); fires `uid_to_invoke` when cleared. |
| `trigger_enemy_spawner` | Periodically spawns one `enemy_type` up to a concurrent/lifetime cap. |
| `trigger_explosion` | Spawns one or more explosions at this point (`explosion_type`, `count`, `ms_delay`). |
| `trigger_load_scene` | Transitions to a new scene when triggered by an adjacent area. |
| `trigger_menu` | Opens a specific menu screen by `menu_type` index (see `.tres` for the full enum). |
| `trigger_new_enemy` | Spawns a named `enemy_type` when triggered. |
| `trigger_new_scene` | Instantiates a scene at `scene_path` when triggered. |
| `trigger_remove_ammo` | Strips revolver/shotgun ammo when triggered. |
| `trigger_remove_guns` | Strips revolver/shotgun weapons when triggered. |
| `trigger_sound` | Plays `audio_path` (2D or 3D) when its `uid` is fired; supports `ms_delay`. |
| `trigger_terminal_email` | Adds a persisted email to the lounge terminal inbox; usually fired via a `trigger_area`'s `uid_to_invoke`. |
| `trigger_toggle_bool` | Sets a named save-state boolean to true when invoked (auto-saves). |

### solid (no subfolder)

| Classname | Purpose |
|---|---|
| `func_crystal` | Glowing crystal structure brush. Quake-style procedural crystal shader at 32 texels/m (pixel-snapped base-to-tip gradient over the group height, no dither, per-face shade, brush texture screen-blended at 0.9, subtle animated rising light band + facet glint on top). Semi-translucent, no light (`color` default cyan, `glow`, `opacity` default 0.85). |
| `func_destructable_barrel` | Explosive/breakable barrel (`godmode` makes it indestructible but still explodes when hit). |
| `func_destructable_control_panel` | Destructible control panel; fires `uid_to_invoke` when broken. |
| `func_display_screen` | CRT-styled brush display (heading/subtext, font sizes, `destroyable`, `flip_text`). |
| `func_display_poster` | CRT-styled digital poster display (`poster_dir`, `poster_index`, `deterministic`, `destroyable`, `flip_x`, `flip_y`, `glow_intensity`, `enable_light`). |
| `func_graffiti` | Wall graffiti projection brush (`flip_x`). Automatically selects graffiti texture (0-17) based on brush world position; non-destructible; avoids decal limits (32 max). |
| `func_group` | Groups brush entities for later toggling; give it a `uid`. |
| `func_ice` | Static solid that reduces player traction (slippery). |

### solid/audio

| Classname | Purpose |
|---|---|
| `audio_ambient_area` | Plays one of a comma-separated `ambient_names` list to anything inside the brush volume. |
| `audio_room_tone` | Fades in looping ambient audio (`audio_name`) on the ambience bus when the player enters the brush volume, and fades out on exit (`fade_time`, `volume_db`, `pitch_scale`). |

### solid/destructable

| Classname | Purpose |
|---|---|
| `destructable_dynamic` | Generic destructible; `destruction_mode` = 0 default / 1 charged-melee-only / 2 ground-slam-only / 3 destroy-on-bump. Pieces/audio follow the texture prefix — glass windows are `destructable_dynamic` with a `G*` texture (glass impact FX + shatter overlay) and `freeze 1`. |
| `destructable_light` | Breakable light fixture. |
| `destructable_vending_machine` | Destructible vending machine that spits out health/armor pickups. |

### solid/info

| Classname | Purpose |
|---|---|
| `info_area_achievement_unlock` | Unlocks a Steam/in-game achievement (`achievement_name`) on entry. |
| `info_area_remove_ammo` | Strips ammo on entry (pairs with `trigger_remove_ammo` logic). |
| `info_area_secret_unlock` | Unlocks a secret by `secret_int` id on entry. |
| `info_area_toggle_boolean` | Sets a named save-state boolean to true on entry (auto-saves). |
| `info_control_prompt` | Shows an on-screen control prompt while the player is inside (`action_display`, `action_name`, `context`). |
| `info_dangerous_fluid` | Acid/lava/arctic hazard (`fluid_type` 0=acid/1=lava/2=arctic); `instant_kill` toggle, `enable_sound` toggle, optional `teleport_name` respawn. |
| `info_fog_volume` | Local fog volume (`color`, `density`). |
| `info_footstep_surface` | Marks a surface as queryable for footstep sound selection. |
| `info_hurtbox` | Generic damage/effect volume for hazards and traps. |
| `info_particle_collision` | Volume that handles particle-system collision interactions. |
| `info_particle_no_rain` | Suppresses the rain particle effect inside (sheltered zones). |
| `info_water_flowing` | Moving water hazard (`direction`, `fog_color`, `instant_kill`, `teleport_name`). |
| `info_water_transparent` | Standard water hazard (`color`, `subdivisions`, `instant_kill`, `teleport_name`). |
| `info_window` | Marks a brush as an indestructible window. |

### solid/platforms

| Classname | Purpose |
|---|---|
| `platform_rotate_x` | Continuously rotates the brush group on the X axis (`speed`, `direction`), no trigger required. |
| `platform_rotate_y` | Continuously rotates the brush group on the Y axis (`speed`, `direction`), no trigger required. |
| `platform_rotate_z` | Same, on the Z axis. |
| `platform_crusher` | Configurable crushing block: extends to a `uid_to_move_to` marker, becomes lethal at max extension, retracts back to start, repeats; supports `fake_crush_count` fake-out jabs plus delay/pause tuning. No trigger required. |

### solid/triggers

| Classname | Purpose |
|---|---|
| `trigger_area` | General-purpose trigger volume; `is_button`/`is_player_only`/`keycard`/`ms_timeout`/`trigger_once`/`uid_to_invoke`/`emit_bus`. See 2.3–2.4. |
| `trigger_area_arena` | Requires all enemies inside to die before firing its `uid_to_invoke` on exit. |
| `trigger_area_weather` | Sets local weather (`weather_type`: 0 none / 1 rain / 2 snow / 3 ash / 4 sandstorm). |
| `trigger_electric_panel` | Like `trigger_area`, but only activates on the revolver's electric special-fire. |
| `trigger_elevator` | Rides the player to an `info_marker_target` and back; see 2.5. |
| `trigger_explosion_force` | Frozen brush that gets a physics impulse (`direction`, `direction_spread`, `force`, `force_variation`, `torque_scale`) when triggered. |
| `trigger_jump_pad` | Launches entities that touch it (`jump_force`). |
| `trigger_looping_moveable_brush` | Loops back to start instantly on reaching `move_distance`. |
| `trigger_looping_moveable_smooth_brush` | Same, but loops back smoothly rather than teleporting. |
| `trigger_move_brush` | Moves a brush `move_distance` along `direction` at `move_speed` when triggered; `toggleable` for back-and-forth. |
| `trigger_remove_weapons` | Strips weapons on entry; `restore_on_leave` to give them back on exit. |

## 5. Troubleshooting

- **New entity class doesn't show up in TrenchBroom.** You edited/added a `.tres` under
  `trenchbroom/fgd/` but forgot to re-export. Re-run 1.2 (Export GameConfig), then restart TrenchBroom
  or reopen the map so it reloads the `.fgd`.
- **Entity class exists in the `.tres` folder but you can't find it in TrenchBroom at all.** Check
  it's actually listed in `entity_definitions` inside `trenchbroom/fpsdevkit_fgd.tres` — a `.tres` file
  sitting in `trenchbroom/fgd/` is not automatically included; it has to be added as an `ext_resource`
  and appended to that array. (`info_teleport_portal` and `pickup_arc` are examples of `.tres` files
  that currently exist but are *not* wired into the shipped FGD — they won't appear in TrenchBroom
  until someone adds them to the array.)
- **Trigger/switch does nothing in-game.** Double-check the `uid` on the receiving entity exactly
  matches the `uid_to_invoke` string on the firing entity — this is plain string matching,
  case-sensitive, no fuzzy lookup.
- **Textures/models missing in TrenchBroom.** Re-check the Game Path from step 1.3 — it must point at
  `trenchbroom/` (not the repo root), since that's where `textures/` and `models/` live.
