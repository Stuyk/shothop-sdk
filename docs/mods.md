# Mods

A mod is a folder inside your mods folder. That's the whole format.

**Mods are data only.** Maps, sounds, music, textures and JSON. The game never runs code from a mod, so installing one can't hurt your machine.

## Where mods go

Run `mod path` to print it, or use this table:

| OS | Mods folder |
|---|---|
| Windows | `%APPDATA%\Godot\app_userdata\Shothop\mods` |
| Linux | `~/.local/share/godot/app_userdata/Shothop/mods` |

The **OPEN FOLDER** button in **CUSTOM MAPS** opens the folder that holds both `mods` and `maps`.

## Layout

Only `mod.json` is required. Leave out any folder you don't need.

```
mods/
  neon_pack/
    mod.json
    maps/        your levels (.map + optional .json and screenshot)
    audio/       sounds your map entities use by name
    music/       tracks for the in-level playlist
    textures/    brush textures for your maps
    replace/     sounds and textures named like built-in ones, to swap them
    data/
      game_const.json   number changes (see Tuning)
      nodes.json        per-class property changes (see Tuning)
```

## mod.json

```json
{
  "id": "neon_pack",
  "name": "Neon Pack",
  "version": "1.0.0",
  "author": "you",
  "description": "Three levels in a neon district.",
  "enabled": true,
  "priority": 0,
  "replace_music": false,
  "sectors": [
    { "id": "neon", "name": "Neon District", "levels": ["neon_01", "neon_02", "neon_03"] }
  ]
}
```

| Field | Default | What it does |
|---|---|---|
| `id` | folder name | Lowercase letters, digits, underscores. Must be unique across installed mods |
| `name` | the id | What players see |
| `version` | empty | Shown in `mod list`. Bump it when you ship an update |
| `author` | empty | You |
| `description` | empty | One line about the mod |
| `enabled` | `true` | `false` keeps it installed but off. `mod disable` sets this for you |
| `priority` | `0` | Load order. Higher loads later and wins when two mods tune the same thing |
| `replace_music` | `false` | `true` plays only your music instead of the game's playlist |
| `sectors` | none | Groups your levels. See below |

Broken JSON or a bad id means the mod doesn't load at all. `mod list` shows why.

## Sectors

The campaign is split into sectors: Alpha, Bravo, Charlie. A sector in your mod works the same way.

```json
"sectors": [
  { "id": "neon", "name": "Neon District", "levels": ["neon_01", "neon_02", "neon_03"] }
]
```

- `levels` are map file names without `.map`, in play order.
- Each sector gets a button in the lounge terminal under **SECTOR DATA**, with per-level times, badges and a teleport.
- A level whose map isn't installed is left out instead of showing up broken.
- `alpha`, `bravo`, `charlie` and `delta` are taken.

You can put more than one sector in a mod.

## What gets you flagged

One rule: **any gameplay tuning turns off Steam leaderboards, ghost saving and achievements.** Everything else is fair game.

| Your mod has | Leaderboards, ghosts, achievements |
|---|---|
| Maps, sounds, music, textures | **Still on** |
| Sound and texture replacements (`replace/`) | **Still on** |
| Anything in `data/game_const.json` | **Off** |
| Anything in `data/nodes.json` | **Off** |

"Anything" means anything. One key counts, even one the game ignores.

And the game says so. With tuning enabled:

- The **main menu** shows `MODDED: <mod names> change gameplay. Leaderboards, ghosts and achievements are off.`
- The **round results screen** shows `MODDED RUN: <mod names> changed gameplay. This run doesn't count...` and hides the leaderboard button.

Nobody gets a tuned run onto the board by accident. Run `mod disable <id>` for clean runs.

Steam leaderboards only cover the campaign, so your own levels never post scores there anyway. Ghosts of your best runs still save for them, as long as no tuning is on.

Your mod can't change the campaign either. The campaign levels, their sectors and your progress always come from the game.

## When changes show up

| You changed | Shows up after |
|---|---|
| A map | The next time you load it |
| Sounds, music, textures | **REFRESH** in CUSTOM MAPS |
| `replace/` (yours or a mod's) | Restarting the game |
| `mod.json`, sectors, a new mod | **REFRESH** in CUSTOM MAPS |
| `data/game_const.json`, `data/nodes.json` | Restarting the game |

## Installing someone else's mod

1. Unzip it into your mods folder. You should end up with `mods/their_mod/mod.json`, not `mods/their_mod/their_mod/mod.json`.
2. Start the game.
3. Not showing up? Run `mod list`. It tells you if it failed and why.
