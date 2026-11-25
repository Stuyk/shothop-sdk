# example_single_map

A single map with no mod around it. Copy this folder into your **maps** folder.

What to look at in TrenchBroom:

- **`info_player_spawn`** on the left. Where you start. Right next to it: **`info_environment`** (Alpha sky and sunlight) and **`info_map_data`**. Keep those three together in every map.
- **The invisible `area_trigger`** across the room (`skip` texture). Walk through it and it wakes the three `enemy_spawner`s with `uid` `arena_wave`. That's the best practice for enemies. See [Maps](../../../docs/maps.md#enemies-use-triggers).
- **`info_map_data`** sets the trophy times: platinum 20s, gold 30s, silver 40s.
- **`portal_scene`** on the right ends the run. `use_round_complete_screen` is `1`, so your time and ghost save.
- `example_single_map.json` and `screenshot.png` are what CUSTOM MAPS shows.
