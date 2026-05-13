# Beast Roster

## 6 Base Beasts

| Beast | Element | Move Type | Angle Range | Power | Special |
|-------|---------|-----------|-------------|-------|---------|
| BOULDER | Rock | HEAVY_JUMP | 25°-60° | 80 | Tremor (area jump) |
| GALE | Wind | FLOAT | 15°-75° | 60 | Current (wind change) |
| MIRE | Mud | CRAWL | 30°-65° | 50 | Swamp (slow zone) |
| SPARK | Metal | DASH | 35°-55° | 85 | Magnet (attract shots) |
| THORN | Thorn | ROLL | 20°-70° | 65 | Eruption (delayed explode) |
| CHASM | Void | TELEPORT | 40°-80° | 95 | Abyss (create hole) |

## BeastData ScriptableObject
- maxHP, moveSpeed, jumpHeight
- angleRange, powerMax, projectileWeight
- baseDamage, explosionRadius
- abilityName, abilityCooldown, abilityEffectType

## BeastController
- Requires Rigidbody2D (Kinematic mode)
- Movement/Jump/Charge/Release mechanics
- HP-based damage state visuals