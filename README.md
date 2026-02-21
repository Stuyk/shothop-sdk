
# 🛠️ Shothop Mapping SDK Setup Guide

This SDK contains everything you need to build custom levels for **Shothop**. We use the **TrenchBroom** editor, a modern tool for Quake-style brush mapping.

## 1. Prerequisites

* **Download TrenchBroom:** Get the latest version from [TrenchBroom.github.io](https://trenchbroom.github.io/).
* **Install the SDK:** Unzip this folder to a permanent location on your drive (e.g., `C:/Modding/Shothop/`).

---

## 2. TrenchBroom Configuration

1. Open **TrenchBroom**.
2. Click on New Map
3. Open Preferences...
4. In the game presets on the left side, click the tiny folder icon to open where Trenchbroom stores custom games. It should open a folder.
5. Copy the `Shothop` folder and paste it into the Trenchbroom's `games` folder.
6. Reload Trenchbroom
7. Open **Trenchbroom Again**
8. Click New Map
9. Click Open Preferences
10. Click on Shothop, and set the Game Path to your unzipped SDK location
11. Hit Apply, Ok
12. Select Shophop on the Select Game Screen
13. In the bottom right under `Mods` you'll see `Shothop` and `textures`. Enable both. `+`

---

## 3. Creating Your First Map

1. On the TrenchBroom Welcome Screen, click **"New Map"** and select your game profile.
2. **Texture Loading:** Your textures should appear in the "Textures" browser tab on the right. If they don't, ensure your Game Path is set correctly to the SDK root.
3. **Placing Entities:** Right-click in the 3D view to Create Entity. You will see game-specific objects (Player Starts, Enemies, Pickups) defined in our FGD.

---

The `.map` file will be utilized in the future for custom Shothop maps.

---

Huge thanks to [Foxtex by Foxhead](https://foxh3ad.itch.io/foxtexcom). This game would not be possible without them.
