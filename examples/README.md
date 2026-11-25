# Examples

One working example for every kind of mod. Each one passes `mod check` and loads in the game. Every folder has a README saying what to look at.

**Fastest way to learn: copy one in, play it, then open it in TrenchBroom and see how it's put together.**

| Example | Type | Shows you |
|---|---|---|
| [`maps/example_single_map`](maps/example_single_map) | Single map | A map with no mod around it. A trigger that wakes enemy spawners, pickups, metadata, screenshot, trophy times, a finishing portal |
| [`mods/example_map_pack`](mods/example_map_pack) | Map pack | A sector with a hub, a level terminal, and two levels linked by portals, each with a triggered enemy wave |
| [`mods/example_textures`](mods/example_textures) | Textures | Your own brush textures, with surface prefixes (metal, concrete, wood) |
| [`mods/example_sounds`](mods/example_sounds) | Sounds | Your own ambient sound and level start sound, played by name |
| [`mods/example_music`](mods/example_music) | Music | Replacing the in-level playlist with your own track |
| [`mods/example_weapon_sounds`](mods/example_weapon_sounds) | Sound replacement | Swapping the shotgun and revolver shots for your own |
| [`mods/example_tuning`](mods/example_tuning) | Tuning | Changing enemy health, damage, difficulty and knockback |

## Install them

Run `mod path` to find your folders ([how](../docs/cli.md#find-the-game)). Then:

1. Copy everything in `examples/mods/` into your **mods** folder.
2. Copy `examples/maps/example_single_map` into your **maps** folder.
3. Start Shothop and open **CUSTOM MAPS**.

Check they're happy:

```
shothop --headless -- mod check
```

**`example_tuning` turns off leaderboards, ghosts and achievements while it's enabled.** The other examples don't. Run `mod disable example_tuning` when you're done playing with it.

**`example_music` replaces the game's music.** Run `mod disable example_music` to get the soundtrack back.

**`example_textures` needs its PNGs in TrenchBroom too** if you want to edit the map. Copy `mods/example_textures/textures/*.png` into the SDK's `textures/` folder.

## Use one as a starting point

1. Copy the example folder and rename it, like `example_map_pack` to `neon_pack`.
2. Change `id` and `name` in `mod.json` to match.
3. Rename the maps and update any sector `levels` and portal `map_name` values that point at them.
4. Run `mod check neon_pack`.
