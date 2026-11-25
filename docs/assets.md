# Sounds, music and textures

Your mod can bring its own files. Drop them in the right folder and refer to them by name. No importing, no converting to some special format.

## Sounds

Put `.ogg` (best), `.wav` or `.mp3` files in your mod's `audio/` folder. Subfolders are fine.

Then use the **file name** in the entity, with or without the extension:

| Entity | Property | Example |
|---|---|---|
| `audio_sound` | `audio_path` | `neon_alarm` |
| `audio_room_tone` | `audio_name` | `neon_room_hum` |
| `audio_ambient_sound` | `audio_name` or `sound_name` | `neon_rain` |
| `audio_attached` | `audio_path` or `file_path` | `neon_engine` |
| `audio_slider` | `file_path` | `neon_train` |
| `audio_loop` | `path` | `neon_fan` |
| `info_map_data` | `vo_on_start` | `neon_intro` |

The game looks in its own sounds first, then every mod's `audio/` and `maps/` folders. **Give your files unique names** (start them with your mod id) so a built-in sound with the same name doesn't win.

Want a built-in sound instead? `mod sounds door` lists them.

### Loudness

Match the game or your sounds will be way too loud or way too quiet next to everything else:

| Kind | Target |
|---|---|
| Sound effects | -20 LUFS |
| Ambient loops | -22 LUFS |
| Music | -14 LUFS |

[ffmpeg](https://ffmpeg.org/) does it in one line:

```
ffmpeg -i input.wav -af loudnorm=I=-20:TP=-1.5 -c:a libvorbis -q:a 6 output.ogg
```

Change `-20` to the target from the table.

## Replace built-in sounds and textures

Swap any of the game's own files: shotgun blasts, footsteps, UI clicks, wall textures, weapon skins, robots, anything. It works across the whole game, campaign included.

There are two places to put replacements:

| Folder | Use it for |
|---|---|
| `replace/` inside your mod | Replacements you share as part of a mod |
| `replace/` next to `mods/` and `maps/` | Your own tweaks for the whole game, no mod needed. **Wins over every mod** |

`mod path` prints both.

### How

1. Find the name:

   ```
   shothop --headless -- mod sounds shotgun
   shothop --headless -- mod textures brass
   ```

   ```
   sfx_shotgun_shot_a                         sfx/shotgun
   base_brass                           assets/models/pickups/revolver_ammo, assets/models/pickups/shotgun_ammo_white
   ```

2. Drop your file in a `replace/` folder with exactly that name: `replace/sfx_shotgun_shot_a.ogg`, `replace/CNAL11_1.png`.
3. Restart the game. Replacements load once at startup.

Every place the game uses that file now uses yours.

### One file or every file with that name

A plain name replaces **every** built-in file with that name. `replace/base_brass.png` swaps the brass on both ammo pickups.

Want just one? Copy the game's folder path:

```
replace/assets/models/pickups/revolver_ammo/base_brass.png
```

Now only the revolver ammo changes. Any other subfolders (`replace/shotgun/...`) are just for keeping things tidy and match by name.

### Rules

- **Sounds: use `.ogg`.** `.wav` and `.mp3` only partly work. Some sounds load before mods do and keep the original.
- **Textures: same size as the original.** A different size gets stretched to fit. `mod check` tells you when sizes don't match.
- **Model skins have to match the model's UV layout.** Same size, same layout, your colours.
- **Replace every variation.** Lots of sounds come in sets (`_a` to `_e`) and the game picks one at random. Replace only `_a` and you hear yours one shot in five.
- **Keep sounds about as loud as the original.** Sound effects sit around -20 LUFS.
- Looping sounds keep looping.
- Two mods replacing the same file? Higher `priority` wins. Your own `replace/` folder beats them all.
- `mod sounds` and `mod textures` mark replaced files with `(replaced)`. `mod check` flags files that don't match any built-in name.

**Replacements are cosmetic, so leaderboards, ghosts and achievements stay on.**

Examples: [`example_weapon_sounds`](../examples/mods/example_weapon_sounds) (shotgun and revolver shots) and [`example_texture_swap`](../examples/mods/example_texture_swap) (two wall textures, everywhere).

## Music

Put tracks in your mod's `music/` folder. Every `.ogg`, `.wav` or `.mp3` there joins the in-level playlist. Tracks crossfade over 4 seconds and the playlist moves on when one ends.

Want only your music? Set `"replace_music": true` in `mod.json`.

Music doesn't play on the main menu, and maps with `disable_music` set stay silent.

## Textures

Put `.png` (or `.jpg`, `.webp`) files in your mod's `textures/` folder. A brush textured `NEON_WALL` in TrenchBroom uses `NEON_WALL.png`.

**Copy the same files into the SDK's `textures/` folder too.** That's where TrenchBroom looks, so that's how they show up in the texture browser.

**`textures/` is for new textures.** Use names the game doesn't already have, like `NEON_WALL`. A built-in name always shows the built-in texture. To change one of those, put your file in `replace/` instead ([how](#replace-built-in-sounds-and-textures)).

For a texture that isn't built in, the game checks these places, in order:

1. The folder the map is in.
2. A `textures/` folder next to the map.
3. Your mod's `textures/`.
4. Every other mod's `textures/`.

### Name prefixes set the surface

The first letters of a texture name decide what it's made of. That changes bullet impact sounds, sparks, decals and what breaks:

| Starts with | Surface |
|---|---|
| `MT` | Metal |
| `GL` or `LT` | Glass |
| `WD` | Wood |
| `OU` or `GR` | Dirt |
| `CN`, `BK` or `SN` | Concrete |

So `MTNEON_PANEL` is metal and `GLNEON_SIGN` is glass.

### Tips

- **Power of two sizes:** 64, 128, 256, 512. Odd sizes look wrong when scaled.
- Textures are drawn with nearest filtering, so pixel art stays crisp.
- `mod textures` lists every built-in name if you'd rather reuse what's there.
- `mod check` tells you about any texture it can't find.
