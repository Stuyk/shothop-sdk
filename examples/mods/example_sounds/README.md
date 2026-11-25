# example_sounds

A map that plays the mod's own sounds by name. Copy this folder into your **mods** folder.

| File | Played by |
|---|---|
| `audio/example_sounds_intro.ogg` | `info_map_data` with `vo_on_start` = `example_sounds_intro`, when the level starts |
| `audio/example_sounds_hum.ogg` | `audio_ambient_sound` with `sound_name` = `example_sounds_hum`, near the far wall |

No paths, no extensions. Just the file name. The names start with the mod id so they never clash with built-in sounds.

Loudness: the hum sits at -22 LUFS (ambient), the intro at -20 LUFS (sound effect).
