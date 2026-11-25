# example_map_pack

A sector with a hub and two levels. Copy this folder into your **mods** folder.

- **`mod.json`** declares the sector `example_pack` with levels `example_pack_01` and `example_pack_02`. It shows in the lounge under **SECTOR DATA**.
- **`example_pack_hub.map`** has an `info_terminal_sector` (`sector` = `example_pack`) that retunes the `info_portal_interactive` with `uid` `portal_example_pack`. Use the terminal to pick a level.
- **`example_pack_01.map`** has a trigger that wakes two melee spawners, then a portal to level 2 with the results screen on.
- **`example_pack_02.map`** wakes shooters and a hammer bot, then the portal takes you back to the hub.

Each map keeps `info_player_spawn`, `info_environment` and `info_map_data` together at the start. Each map has its own look: the hub is Alpha, level 1 is Bravo (volcanic), level 2 is Charlie (space). Compare their `info_environment` settings.

Start with the hub from CUSTOM MAPS.
