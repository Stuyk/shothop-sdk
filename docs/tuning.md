# Tuning

Change health, damage, speeds and more with two JSON files in your mod's `data/` folder. No maps needed.

**Tuning turns off Steam leaderboards, ghost saving and achievements** while your mod is enabled. Everyone else's runs stay fair, and you get to do whatever you want with yours.

## game_const.json

`data/game_const.json` changes the game's core numbers by name:

```json
{
  "CONTROLLED_SHOOTER_HEALTH": 25.0,
  "CONTROLLED_HAMMER_DAMAGE": 40.0,
  "DIFFICULTY_DAMAGE_TAKEN_MULTIPLIER": [0.5, 1.0, 1.5]
}
```

Rules:

1. **Keep the type.** `10.0` stays a decimal, `100` stays a whole number.
2. **Arrays keep their length.** The `DIFFICULTY_*` ones have one value per difficulty: Easy, Normal, Hardcore.
3. Unknown keys and wrong types are skipped. `mod check` tells you which.
4. Two mods setting the same key? The one with the higher `priority` wins.
5. **Restart the game** after editing. Tuning is read once at startup.

`mod keys` prints this list from your installed game, so it's always current:

```
shothop --headless -- mod keys
shothop --headless -- mod keys HAMMER
```

### Every key

| Key | Default | Notes |
|---|---|---|
| `PLAYER_MAX_HEALTH` | `100` |  |
| `PLAYER_MAX_ARMOR` | `50` |  |
| `PLAYER_REVOLVER_MULTIPLIER_EXPIRATION_MS` | `3000` |  |
| `PLAYER_DAMAGE_CHARGE_BAYONET` | `12.0` |  |
| `PLAYER_DAMAGE_MELEE_BAYONET` | `4.0` |  |
| `PLAYER_DAMAGE_BULLET_REVOLVER` | `5.0` |  |
| `PLAYER_DAMAGE_BULLET_SHOTGUN` | `3.0` |  |
| `PLAYER_DAMAGE_BARRELING` | `10.0` |  |
| `PLAYER_DAMAGE_HOLY_CHAINSAW` | `12.0` |  |
| `PLAYER_DAMAGE_LIGHTNING_SHOT` | `25.0` |  |
| `PLAYER_DAMAGE_LIGHTNING_ARC` | `7.0` |  |
| `PLAYER_BULLET_REVOLVER_SPEED` | `128.0` |  |
| `PLAYER_BULLET_SHOTGUN_SPEED` | `128.0` |  |
| `CONTROLLED_MELEE_DAMAGE` | `15.0` |  |
| `CONTROLLED_MELEE_HEALTH` | `12.0` |  |
| `CONTROLLED_ARCANE_DAMAGE` | `8.0` |  |
| `CONTROLLED_ARCANE_HEALTH` | `10.0` |  |
| `CONTROLLED_DYNAMITE_DAMAGE` | `30.0` |  |
| `CONTROLLED_DYNAMITE_HEALTH` | `8.0` |  |
| `CONTROLLED_SHIELD_DAMAGE` | `10.0` |  |
| `CONTROLLED_SHIELD_HEALTH` | `50.0` |  |
| `CONTROLLED_SHIELD_SLAM_DAMAGE` | `15.0` | Close-range slam shockwave (full damage at the center) |
| `CONTROLLED_SHOOTER_DAMAGE` | `2.0` |  |
| `CONTROLLED_SHOOTER_HEALTH` | `10.0` |  |
| `CONTROLLED_ROCKET_BOT_DAMAGE` | `25.0` |  |
| `CONTROLLED_ROCKET_BOT_HEALTH` | `80.0` |  |
| `CONTROLLED_MINIGUN_DAMAGE` | `1.5` |  |
| `CONTROLLED_MINIGUN_HEALTH` | `45.0` |  |
| `CONTROLLED_FLYING_DRONE_DAMAGE` | `5.0` |  |
| `CONTROLLED_FLYING_DRONE_HEALTH` | `1.0` |  |
| `CONTROLLED_KNIFE_DASH_DAMAGE` | `20.0` |  |
| `CONTROLLED_KNIFE_HEALTH` | `12.0` |  |
| `CONTROLLED_HAMMER_DAMAGE` | `25.0` |  |
| `CONTROLLED_HAMMER_HEALTH` | `30.0` |  |
| `CONTROLLED_HAMMER_SHOCKWAVE_DAMAGE` | `12.0` |  |
| `CONTROLLED_ACHILLES_DAMAGE` | `2.0` |  |
| `CONTROLLED_ACHILLES_HEALTH` | `8.0` |  |
| `CONTROLLED_SPINNER_DAMAGE` | `1.5` |  |
| `CONTROLLED_SPINNER_HEALTH` | `6.0` |  |
| `CONTROLLED_LOOTBOT_HEALTH` | `25.0` |  |
| `DIFFICULTY_ENEMY_SPEED_MULTIPLIER` | `[0.75, 1.0, 1.0]` | Enemy movement speed |
| `DIFFICULTY_DAMAGE_TAKEN_MULTIPLIER` | `[1.0, 1.25, 2.0]` | All damage the player takes (enemies + hazards) |
| `DIFFICULTY_SPAWNER_FREQUENCY_OFFSET` | `[0.0, 0.0, -1.0]` | Delta (seconds) to enemy_spawner interval |
| `DIFFICULTY_SPAWNER_COUNT_BONUS` | `[0, 0, 1]` | Bonus concurrent & finite lifetime count on enemy_spawner |
| `ENEMY_CATCH_UP_SPEED_FACTOR` | `0.5` | Chase speed added per m/s the player moves above sprint speed |
| `ENEMY_CATCH_UP_FAR_DISTANCE_MIN` | `15.0` | Distance (m) where the far-away bonus starts |
| `ENEMY_CATCH_UP_FAR_DISTANCE_MAX` | `40.0` | Distance (m) where the far-away bonus is at full strength |
| `ENEMY_CATCH_UP_FAR_BONUS` | `0.2` | Fraction of base chase speed added at full far-away distance |
| `ENEMY_CATCH_UP_MAX_MULTIPLIER` | `1.6` | Chase speed never exceeds base * this |
| `ENEMY_CATCH_UP_SMOOTHING` | `4.0` | How fast the bonus follows changes in player speed (per second) |
| `CONTROLLED_SPIDER_MINE_DAMAGE` | `6.0` |  |
| `CONTROLLED_SPIDER_MINE_HEALTH` | `2.0` |  |
| `CONTROLLED_GAS_SPIDER_HEALTH` | `20.0` |  |
| `CONTROLLED_FLAMETHROWER_BOT_DAMAGE` | `1.0` |  |
| `CONTROLLED_FLAMETHROWER_BOT_HEALTH` | `10.0` |  |
| `CONTROLLED_MINE_BOT_HEALTH` | `15.0` |  |
| `CONTROLLED_MINE_BOT_EXPLOSION_DAMAGE` | `50.0` |  |
| `BARREL_DAMAGE` | `99.0` |  |
| `BARREL_DAMAGE_FOR_PLAYER` | `10.0` |  |
| `TURRET_SNIPER_DAMAGE` | `20.0` |  |
| `TURRET_SMG_DAMAGE` | `5.0` |  |
| `TURRET_ROCKET_DAMAGE` | `25.0` |  |
| `TURRET_FLAME_DAMAGE` | `2.0` |  |
| `TRAP_LASER_DAMAGE` | `12.0` |  |
| `TRAP_LASER_DAMAGE_FOR_PLAYER` | `40.0` |  |
| `TRAP_STEAM_DAMAGE` | `10.0` |  |
| `TRAP_BULLET_DAMAGE` | `10.0` |  |
| `TRAP_ROCKET_DAMAGE` | `25.0` |  |
| `TRAP_GAS_DAMAGE` | `5.0` | per MS_DAMAGE_TICK (350ms -> ~14.3 base DPS) |
| `TRAP_LANDMINE_DAMAGE` | `20.0` |  |
| `GAS_BOT_BLOB_SPLASH_DAMAGE` | `15.0` |  |
| `GAS_BOT_BLOB_SPLASH_RADIUS` | `3.5` |  |

## nodes.json

`data/nodes.json` sets properties on everything of one class as it spawns. A base class counts too, so `EnemyGeneric` hits every walking enemy:

```json
{
  "EnemyGeneric": { "max_knockback_speed": 60.0, "speed_knockback_decay": 10.0 }
}
```

That one lets enemies get knocked back a lot faster when you blast them, and slide for longer before they stop.

Find what you can change:

```
shothop --headless -- mod classes Enemy
shothop --headless -- mod props EnemyGeneric
```

`mod props` prints each property with its type and default:

```
EnemyGeneric (CharacterBody3D)
  knockback_dampener                   float      1.0
  knockback_immune                     bool       false
  speed_knockback_decay                float      25.0
  max_knockback_speed                  float      30.0
```

Supported values: numbers, `true`/`false`, text, arrays, vectors as `[x, y, z]`, and colours as `"#ff00aa"`.

### When nodes.json does nothing

Your value lands before the object sets itself up. **If the object sets that same property during setup, yours gets replaced.** Two common cases:

1. **Map entities read their own properties.** A jump pad's force, a light's colour, an alarm's spin speed all come from what you set in TrenchBroom. Change those in the map instead.
2. **Enemy health and damage come from `game_const.json`.** Use that file for them.

Rule of thumb: try it, then run `mod check`. If it's valid but nothing changes in game, it's one of those two.
