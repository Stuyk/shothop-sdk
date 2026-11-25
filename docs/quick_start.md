# Your first mod in 10 minutes

You're gonna make a mod, build a level in it, and play it. You need the game, this SDK, and TrenchBroom set up from the [README](../README.md).

## 1. Make the mod

Open a terminal in the game folder ([how](cli.md#find-the-game)) and run:

```
shothop --headless -- mod new neon_pack "Neon Pack"
```

Use `shothop.console.exe` on Windows and `./shothop.x86_64` on Linux.

It prints where it made the folder. Keep that path handy.

## 2. Play it once

Start Shothop. Open **CUSTOM MAPS**. Pick **Neon Pack 01** and hit play.

It's a floor, a light and you. Boring, but it works, and now you know the whole loop works before you touch anything.

## 3. Build your level

1. In TrenchBroom, **File** → **Open** and pick `mods/neon_pack/maps/neon_pack_01.map`.
2. Build. Brushes for walls, right-click the 3D view for entities (enemies, pickups, lights).
3. Save.

Every entity and what it does is in the [Entity reference](fgd_guide.md).

**Keep one `info_player_spawn` in every map.** No spawn, no player.

**Spawn enemies with a trigger, not by placing them.** A `trigger_area` wakes `trigger_enemy_spawner`s when you walk in. [How](maps.md#enemies-use-triggers), and every example in [`examples/`](../examples) does it.

## 4. Check it

```
shothop --headless -- mod check neon_pack
```

Fix every `ERROR` line. `warn` lines are worth a look but won't break anything.

## 5. Play again

Back in Shothop, **CUSTOM MAPS**, hit **REFRESH**, play.

You don't need to restart the game after editing a map. Refresh picks it up.

## 6. Add more levels

1. Copy `neon_pack_01.map` to `neon_pack_02.map` (and the `.json` next to it) and build it out.
2. Add `"neon_pack_02"` to the `levels` list in `mod.json`.
3. Put a portal at the end of level 1 with `map_name` set to `neon_pack_02`.

Your sector now has two levels. It shows in the lounge terminal under **SECTOR DATA** with progress and times, like the campaign. More on that in [Maps](maps.md).

## 7. Share it

```
shothop --headless -- mod zip neon_pack
```

Send people `neon_pack.zip`. They unzip it into their mods folder, start the game, and that's it.

## Where to go next

- [Sounds, music and textures](assets.md) to make it look and sound like yours.
- [Tuning](tuning.md) to change enemy health, damage and more.
- [Mods](mods.md) for everything `mod.json` can do.
