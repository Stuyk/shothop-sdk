# Mod tools

Shothop has mod tools built into the game. You run the game from a terminal with `-- mod` and a command. It does the thing, prints the result, and closes. No window, no menu.

```
shothop --headless -- mod help
```

- `--headless` keeps the game window from opening.
- `--` separates the game's own options from yours. **Don't skip it.**
- `mod <command>` is the tool you want.

## Find the game

In Steam, right-click **Shothop**, then **Manage** → **Browse local files**. That folder has the game file you run.

| OS | Run this file | Example |
|---|---|---|
| Windows | `shothop.console.exe` | `.\shothop.console.exe --headless -- mod help` |
| Linux | `shothop.x86_64` | `./shothop.x86_64 --headless -- mod help` |

**On Windows use `shothop.console.exe`, not `shothop.exe`.** The normal exe can't print to a terminal, so you'd see nothing.

Open a terminal in that folder:

- **Windows:** in the folder, click the address bar, type `powershell`, hit Enter.
- **Linux:** right-click the folder and pick **Open in Terminal**.

The examples below write `shothop` for short. Use the file from the table.

Each command takes a few seconds, since it's the whole game starting up.

## Commands

| Command | What it does |
|---|---|
| `mod help` | Lists every command |
| `mod path` | Prints where your mods, maps and logs live |
| `mod new <id> [name]` | Makes a mod with a starter map you can play right away |
| `mod list` | Lists installed mods and whether they loaded |
| `mod check [id]` | Checks a mod, or every mod, for mistakes |
| `mod enable <id>` | Turns a mod on |
| `mod disable <id>` | Turns a mod off without deleting it |
| `mod zip <id> [out.zip]` | Packs a mod into one zip to share |
| `mod keys [filter]` | Lists every tuning key and its default |
| `mod classes [filter]` | Lists class names for `data/nodes.json` |
| `mod props <Class>` | Lists one class's properties and defaults |
| `mod textures [filter]` | Lists built-in brush texture names |
| `mod sounds [filter]` | Lists built-in sounds with their folder, marks ones a mod replaced |
| `mod entities [filter]` | Lists entity names your maps can use |

`[filter]` is optional. It keeps lines containing that text, upper or lower case.

## mod new

```
shothop --headless -- mod new neon_pack "Neon Pack"
```

The id is lowercase letters, digits and underscores. It's the folder name, so pick something that won't clash with someone else's mod. The name is what players see. Leave it out and you get the id in title case.

You get:

```
mods/neon_pack/
  mod.json             a sector called "Neon Pack" with one level
  README.md            short notes for you
  maps/neon_pack_01.map    a floor, a light and a spawn point
  maps/neon_pack_01.json   title and description for the map list
  audio/  music/  textures/  replace/  data/
```

**It's playable right now.** Start Shothop, open **CUSTOM MAPS** and pick **Neon Pack 01**.

## mod check

```
shothop --headless -- mod check neon_pack
```

Run this before you share anything. It reads every map and data file and tells you exactly what's wrong:

```
== neon_pack (C:/Users/you/AppData/Roaming/Godot/app_userdata/Shothop/mods/neon_pack)
  ERROR neon_01.map: unknown entity 'pickup_ammo_shotgun_permanent_shotgun' (see: mod entities)
  ERROR neon_01.map: texture 'NEON_WALL' not found (built-in or in textures/)
  ok    neon_02.map: entities and textures ok
  ok    sector 'neon': 2 level(s)
  ERROR game_const: CONTROLLED_SHOOTER_HEALTH expects float like 10.0
  warn  tuning is on: leaderboards, ghosts and achievements are off while this mod is enabled

3 error(s), 1 warning(s)
```

What it checks:

1. `mod.json` reads and has a valid id.
2. Every entity in your maps exists in this version of the game.
3. Every brush texture is built in or in your `textures/`.
4. Every sound your entities name (`audio_name`, `audio_path`, `sound_name`, `file_path`) exists.
5. Portals point at maps that are installed.
6. Each map has an `info_player_spawn`.
7. Every level listed in a sector has a map.
8. Music files can be read.
9. Every file in `replace/` matches a built-in sound, and the replacement took.
10. Tuning keys and values are real and the right type.

Leave out the id to check every mod at once. It exits with code 1 when it finds errors, so you can use it in scripts.

## mod zip

```
shothop --headless -- mod zip neon_pack
```

Makes `mods/neon_pack.zip` with your mod folder inside. Give it a second argument to save it somewhere else:

```
shothop --headless -- mod zip neon_pack C:/Users/you/Desktop/neon_pack.zip
```

Players unzip it into their mods folder. Done.

## Looking stuff up

You don't have the game's source, so these tell you the real names:

```
shothop --headless -- mod textures metal
shothop --headless -- mod sounds door
shothop --headless -- mod entities enemy
shothop --headless -- mod keys SHIELD
shothop --headless -- mod classes Pad
shothop --headless -- mod props JumpPad
```

`mod keys` also shows when an installed mod has changed a value:

```
CONTROLLED_SHOOTER_HEALTH                     10.0   (mods set 20.0)
```

## Other things you can do

- `mod disable neon_pack` before you test something without it. `mod enable neon_pack` brings it back.
- `mod list` after installing someone's mod, to make sure it loaded.
- `mod path` when you can't find your mods folder.
