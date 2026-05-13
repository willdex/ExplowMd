# Core Gameplay

## Turn Loop
1. Active player has 20s to aim
2. Optional movement phase (limited by stamina)
3. Aiming: angle + power (organic charge mechanic)
4. Shot: parabolic trajectory
5. Impact: proximity damage + terrain effect
6. Death check → turn switch

## Key Systems
- **TurnManager**: Phase control (AIM → SHOOTING → IMPACT → TURN_END)
- **BallisticsSystem**: Deterministic projectile physics (no Rigidbody2D)
- **WindSystem**: Global wind vector, changes every 2-3 turns
- **DamageCalculator**: Falloff-based damage, movement penalties

## Damage States
| State | HP Ratio | Movement | Dispersion |
|-------|----------|----------|------------|
| INTACT | >60% | 100% | 0% |
| WOUNDED | 30-60% | 90% | +5% |
| DYING | <30% | 75% | +15% |

## ShotInput Structure
- angle, power, useSpecialAbility
- Processed by TurnManager → FireProjectile
- AI decision via MinimaxAI