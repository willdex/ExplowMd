# Next Tasks

## Immediate (Fixes)
1. **Create ProjectileController.prefab** - Visual projectile in scene
2. **Verify AI execution** - Check OnTurnStarted handler calls AIPlayer.Think()
3. **Stop duplicates** - QuickSetup should check existing instances before creating

## Short Term (Gameplay)
1. **Implement player input** - AimingController for angle/power
2. **AimingIndicator UI** - Trajectory preview line
3. **HUD integration** - Health bar, turn timer, wind indicator
4. **Terrain destruction on impact** - TerrainManager.HandleExplosion

## Medium Term (Polish)
1. **3D beast models** - Blender pipeline
2. **Animations** - 8 states per beast
3. **VFX particles** - Charge, impact, trails
4. **Sound effects** - Placeholder audio hooks

## Future (Multiplayer)
1. **Photon PUN integration**
2. **Network sync for terrain**
3. **Host authoritative ballistics**
4. **Replay system**

## Dependency Graph
```
[Projectile Prefab] ← needed for → [AI Shot Visualization]
[AI Execution] ← needs → [TurnManager events wired]
[Terrain Destruction] ← needs → [Projectile Impact callback]
[Player Input] ← needs → [AimingIndicator UI]
```