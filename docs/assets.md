# Sounds, music and textures

Your mod can bring its own files. Drop them in the right folder and refer to them by name. No importing, no converting to some special format.

## Sounds

Put `.ogg` (best), `.wav` or `.mp3` files in your mod's `audio/` folder. Subfolders are fine.

Then use the **file name** in the entity, with or without the extension:

| Entity | Property | Example |
|---|---|---|
| `trigger_sound` | `audio_path` | `neon_alarm` |
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

## Replace built-in sounds

Swap any of the game's own sounds: shotgun blasts, revolver shots, footsteps, UI clicks, enemy noises, anything.

1. Find the sound's name:

   ```
   shothop --headless -- mod sounds shotgun
   ```

   ```
   sfx_shotgun_shot_a                         sfx/shotgun
   sfx_shotgun_shot_b                         sfx/shotgun
   ...
   ```

2. Save your sound as **`.ogg`** with exactly that name in your mod's `replace/` folder, like `replace/sfx_shotgun_shot_a.ogg`. Subfolders are fine.
3. Restart the game. Replacements load once at startup.

That's it. Every place the game plays that sound now plays yours.

- **Use `.ogg`.** `.wav` and `.mp3` only partly work here. Some sounds load before mods do and keep the original.
- **Replace every variation.** Lots of sounds come in sets (`_a` to `_e`) and the game picks one at random. Replace only `_a` and you hear yours one shot in five.
- **Keep it about as loud as the original.** Sound effects sit around -20 LUFS.
- Looping sounds keep looping. The game keeps the original's loop settings.
- Two mods replacing the same sound? Higher `priority` wins.
- `mod sounds` marks every sound a mod replaced with `(replaced)`. `mod check` flags files that don't match any built-in name.

Replacements are cosmetic, so leaderboards, ghosts and achievements stay on.

See [`examples/mods/example_weapon_sounds`](../examples/mods/example_weapon_sounds): sci-fi blasters for the shotgun and revolver.

## Music

Put tracks in your mod's `music/` folder. Every `.ogg`, `.wav` or `.mp3` there joins the in-level playlist. Tracks crossfade over 4 seconds and the playlist moves on when one ends.

Want only your music? Set `"replace_music": true` in `mod.json`.

Music doesn't play on the main menu, and maps with `disable_music` set stay silent.

## Textures

Put `.png` (or `.jpg`, `.webp`) files in your mod's `textures/` folder. A brush textured `NEON_WALL` in TrenchBroom uses `NEON_WALL.png`.

**Copy the same files into the SDK's `textures/` folder too.** That's where TrenchBroom looks, so that's how they show up in the texture browser.

The game checks these places, in order, for each texture in your map:

1. The folder the map is in.
2. A `textures/` folder next to the map.
3. Your mod's `textures/`.
4. Every other mod's `textures/`.
5. The built-in textures.

A texture with the same name as a built-in one replaces it, but **only in your maps**. The campaign never changes.

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
