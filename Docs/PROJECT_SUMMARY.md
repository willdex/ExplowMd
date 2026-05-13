# PROJECT_SUMMARY.md - Quick Reference for AI Architect

## What Is This Project?
Explow is a 2D turn-based artillery game (like Gunbound/Jinetes Bestias). Two beasts take turns firing projectiles across destructible terrain. One human vs AI.

## Current State (2026-05-12)
- **Engine:** Unity 6000.3.15f1 (2D URP)
- **Language:** C# with Explow.* namespaces
- **MCP:** Working on port 8080 (HTTP)
- **Git Main:** https://github.com/wickd/Explow
- **Git Docs:** https://github.com/willdex/ExplowMd

## Working Systems ✅
1. **TurnManager** - Phase machine: WAITING→AIM→SHOOTING→IMPACT→(loop)
2. **GameBootstrap** - Runtime orchestrator, wires events
3. **ProjectilePool** - Loads prefab, provides projectiles
4. **BallisticsSystem** - Deterministic Euler integration physics
5. **MinimaxAI** - 3 difficulty levels, alpha-beta pruning
6. **TerrainManager** - Tilemap destruction (circular holes)
7. **WindSystem** - Affects projectile trajectory

## What's Broken 🔴
1. **GameBootstrap timing** - Was connecting before TurnManager ready
   - Fix applied: coroutine waits for Phase != WAITING
2. **Projectile visibility** - Prefab sprite may not be assigned
3. **AI turn not firing** - Previously isProcessingTurn=true blocking Update()
   - Fix applied: set isProcessingTurn=false in OnProjectileImpact()

## Game Loop Flow
```
Player 0 (Human) Turn:
  AIM phase → Player aims → Fire → SHOOTING
  Projectile flies → Impact → Terrain damage + Beast damage
  IMPACT pause → AI Turn

Player 1 (AI) Turn:
  AIM phase starts → OnTurnStarted(playerId=1)
  → AIDelayThenThink (0.5s delay)
  → AIPlayer.Think(gameState)
  → MinimaxAI.CalculateBestMove()
  → OnAIDecision(ShotInput)
  → TurnManager.SubmitShot(angle, power)
  → Projectile fires → Impact → Human Turn
```

## Key Files
| File | Role |
|------|------|
| `TurnManager.cs` | Phase authority, fires events |
| `GameBootstrap.cs` | Wires everything, fires shots |
| `AIPlayer.cs` | Wraps MinimaxAI, called by GameBootstrap |
| `ProjectileController.cs` | BallisticsSystem.CalculatePosition() each frame |
| `Constants.cs` | All beast stats, physics values |

## Testing
1. Open `Assets/_Project/Scenes/test.unity`
2. Press Play
3. Check console for: `[GameBootstrap] Turn started - Player 0`
4. After 20s or shot: turn should switch to Player 1, AI should fire

## Critical Bug to Fix
`TurnManager.cs` line ~138: `isProcessingTurn = false` was missing in `OnProjectileImpact()`. This caused IMPACT phase timer to never decrement, freezing the game. **FIX APPLIED BUT NOT TESTED YET**

## Next Actions
1. Test the turn system fix (isProcessingTurn in OnProjectileImpact)
2. Verify projectile appears in scene
3. Confirm AI fires after human
4. Validate terrain destruction on impact

## For More Info
See `PROJECT_OVERVIEW.md` for complete architecture documentation.