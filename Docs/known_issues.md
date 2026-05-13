# Known Issues

## Critical (Blocking)
1. **Projectile not visible** - ProjectileController prefab missing, pool returns null
2. **AI turn not executing** - TurnManager.OnTurnStarted AI branch needs verification
3. **Multiple capsule instances** - QuickSetup creates duplicates on repeated Play without Stop

## Medium Priority
1. **Audio listener warnings** - Multiple cameras with AudioListener
2. **Terrain only one side** - Bounds calculation incorrect in FillTerrainTiles
3. **Sprite texture 4x4** - Texture2D.whiteTexture is too small

## Minor
1. **InputManager deprecation** - Unity recommends Input System migration
2. **FindObjectsOfType deprecation** - CS0618 warning in ProjectileController
3. **No animator on beasts** - Placeholder sprites only

## Resolved
- [x] CapsuleCollider conflict with Rigidbody2D → Changed to Sphere primitive
- [x] LayerMask.GetMask in constructor → Moved to Awake
- [x] Texture2D.whiteTexture size → Create valid 32x32 texture