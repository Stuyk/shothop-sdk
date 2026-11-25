# example_tuning

Changes gameplay numbers. Copy this folder into your **mods** folder and restart the game.

**This turns off Steam leaderboards, ghost saving and achievements while it's enabled.** Run `mod disable example_tuning` when you're done.

`data/game_const.json`:

| Key | Default | Here |
|---|---|---|
| `CONTROLLED_SHOOTER_HEALTH` | 10.0 | 20.0 |
| `CONTROLLED_HAMMER_DAMAGE` | 25.0 | 35.0 |
| `DIFFICULTY_DAMAGE_TAKEN_MULTIPLIER` | [1.0, 1.25, 2.0] | [0.75, 1.0, 1.5] |

`data/nodes.json` gives every walking enemy (`EnemyGeneric`) twice the knockback cap and less drag, so shots send them sliding.

`mod keys` and `mod props EnemyGeneric` list everything else you can change.
