# Troubleshooting

**Start with `mod check`.** Most problems show up there with the exact file and line.

```
shothop --headless -- mod check
```

Still stuck? The game writes everything to a log. `mod path` prints where it is. Search it for `Mods:`.

## My mod doesn't show up

1. Run `mod list`. If it says `ERROR`, the reason is right there.
2. Check the folder depth. You need `mods/neon_pack/mod.json`. Unzipping often makes `mods/neon_pack/neon_pack/mod.json`.
3. `"enabled": false` in `mod.json`? Run `mod enable neon_pack`.
4. Added it while the game was open? Hit **REFRESH** in CUSTOM MAPS.

| `mod list` says | Fix |
|---|---|
| `no mod.json` | Add one. `mod new` makes a correct one you can copy |
| `mod.json is empty or not a JSON object` | Broken JSON. A missing comma or quote. Paste it into any JSON validator |
| `id '...' must be letters, digits and underscores` | No spaces, dashes or capitals in `id` |
| `duplicate id` | Two mods share an `id`. Rename one |

## My map isn't in CUSTOM MAPS

- It has to end in `.map` and sit in your mod's `maps/` folder (or the plain `maps/` folder).
- Folders and files with `autosave` in the name, and anything in a `z_archived` folder, are skipped on purpose. TrenchBroom's autosaves go there.

## My map loads but something's missing

| Symptom | Likely cause |
|---|---|
| You fall forever | No `info_player_spawn`, or `kill_depth` above your floor |
| A wall shows the placeholder texture | Texture not found. `mod check` lists which |
| An entity is just gone | Its class name changed between game versions. `mod check` flags it, `mod entities` lists real names |
| No sound from an entity | Sound name typo, or the file isn't `.ogg`/`.wav`/`.mp3`. `mod check` flags it |
| Finishing the level does nothing | Your portal needs `use_round_complete_screen` set to `1` |

## My sector isn't in the lounge terminal

- It's under **SECTOR DATA**, below the campaign sectors.
- The button stays locked until at least one of its levels has a map installed. `mod check` reports levels with no map.
- Sector ids `alpha`, `bravo`, `charlie` and `delta` belong to the campaign and are ignored.

## My replacement sound doesn't play

1. Restart the game. `replace/` only loads at startup.
2. The file name has to match exactly. `mod sounds <part of the name>` shows the real names, and `mod check` flags files that match nothing.
3. Use `.ogg`. `.wav` and `.mp3` miss sounds the game loads early.
4. Hearing the original some of the time? There are more variations (`_b`, `_c` ...). Replace them all.

## My tuning does nothing

1. Restart the game. Tuning only loads at startup.
2. Run `mod check`. Unknown keys and wrong types are skipped.
3. `nodes.json` value valid but no effect? The object sets that property itself on spawn. See [Tuning](tuning.md#when-nodesjson-does-nothing).

## No leaderboard scores or achievements

That's tuning. Any `data/game_const.json` or `data/nodes.json` in an enabled mod turns off leaderboards, ghosts and achievements. `mod list` says so at the bottom. Run `mod disable <id>` for clean runs.

Custom levels never post Steam leaderboard scores. Only the campaign does.

## The mod tools print nothing (Windows)

Use `shothop.console.exe`, not `shothop.exe`. See [Mod tools](cli.md#find-the-game).
