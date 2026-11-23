# Custom Maps Metadata & Screenshots

Custom `.map` files placed in `user://maps/` (either directly or inside per-map subfolders) can include a JSON metadata sidecar and a preview screenshot for the in-game Custom Levels browser.

## File Layout

**Flat layout (`user://maps/`):**
```text
user://maps/
  my_arena.map
  my_arena.json
  my_arena.png
```

**Subfolder layout (`user://maps/<map_folder>/`):**
```text
user://maps/my_arena/
  my_arena.map
  map.json        (or my_arena.json)
  screenshot.png  (or preview.png / my_arena.png)
```

Supported image formats: `.png`, `.jpg`, `.jpeg`, `.webp`.

## JSON Schema (`<stem>.json` or `map.json`)

All fields are optional:

```json
{
  "title": "My Arena",
  "description": "Fast-paced vertical arena with jump pads and shotgun pickups.",
  "author": "MapperName",
  "version": "1.0.0",
  "screenshot": "screenshot.png"
}
```

- `title`: Display name in the browser list and preview header. Defaults to the formatted `.map` filename.
- `description`: Shown in the scrollable description area.
- `author`: Displayed as `BY <AUTHOR>`.
- `version`: Displayed as `v<version>`.
- `screenshot`: Relative filename inside the map's directory (path traversal `..` and absolute paths are ignored). If omitted, the browser auto-discovers `<stem>.(png|jpg|jpeg|webp)`, or `screenshot.*` / `preview.*` in subfolders.
