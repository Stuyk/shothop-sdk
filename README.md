# Shothop SDK

Everything you need to make maps and mods for **Shothop**.

A Shothop mod is a folder. Maps, sounds, music, textures and a bit of JSON. No code, no engine, no build step. You drop the folder in, start the game, and play it.

## Pick your path

1. **I want to make a level.** Set up TrenchBroom below, then read [Maps](docs/maps.md).
2. **I want a full mod.** Same setup, then do [Your first mod in 10 minutes](docs/quick_start.md), or copy one from [`examples/`](examples).
3. **I want to tweak numbers.** Read [Tuning](docs/tuning.md). You don't even need TrenchBroom.

## What's in here

| Folder | What it is |
|---|---|
| `Shothop/` | TrenchBroom game config and entity definitions (`Shothop.fgd`) |
| `textures/` | Every built-in brush texture, so TrenchBroom can show them |
| `trenchbroom/` | Models TrenchBroom uses to preview entities |
| `docs/` | The guides |
| `examples/` | A working example of every kind of mod. Copy one in and play it |

## Set up TrenchBroom (once)

You need [TrenchBroom](https://trenchbroom.github.io/). It's free.

1. Unzip this SDK somewhere it can stay, like `C:/Modding/ShothopSDK/`.
2. Open TrenchBroom, click **New Map**, then **Open Preferences**.
3. Click the small folder icon under the game list. It opens TrenchBroom's `games` folder.
4. Copy this SDK's `Shothop` folder into that `games` folder.
5. Restart TrenchBroom. It only looks for games on launch.
6. **New Map**, then **Open Preferences**, click **Shothop**, and set **Game Path** to this SDK folder.
7. Click **Apply**, then pick **Shothop** on the game screen.
8. Bottom right, under **Mods**, enable both `Shothop` and `textures`.

Textures now show in the texture browser, and right-clicking the 3D view gives you every Shothop entity.

## The mod tools

The game has a few terminal commands built in. They make a mod for you, check it for mistakes, and pack it up to share:

```
shothop --headless -- mod new neon_pack "Neon Pack"
shothop --headless -- mod check neon_pack
shothop --headless -- mod zip neon_pack
```

**Read [Mod tools](docs/cli.md) first.** It shows where the game lives on your machine and the exact command for your OS.

## Guides

| Guide | Read it when |
|---|---|
| [Your first mod in 10 minutes](docs/quick_start.md) | You're starting out |
| [Mods](docs/mods.md) | You want the full mod folder and `mod.json` reference |
| [Mod tools](docs/cli.md) | You want every terminal command |
| [Maps](docs/maps.md) | You're building levels: metadata, screenshots, linking levels together |
| [Sounds, music and textures](docs/assets.md) | You're adding your own files |
| [Tuning](docs/tuning.md) | You want to change health, damage and other numbers |
| [Troubleshooting](docs/troubleshooting.md) | Something doesn't show up or doesn't work |
| [Entity reference](docs/fgd_guide.md) | You need what an entity does and its properties |

---

Huge thanks to [Foxtex by Foxhead](https://foxh3ad.itch.io/foxtexcom). This game would not be possible without them.
