# Explow Project - Complete Overview

**Project:** Explow 2D Artillery Game
**Engine:** Unity 6.3.15f1 (2D URP)
**Language:** C# with Explow.* namespaces
**MCP:** HTTP on port 8080 (working)
**Repository:** https://github.com/wickd/Explow
**Docs Repo:** https://github.com/willdex/ExplowMd

---

## What Is Explow

Explow is a tactical turn-based artillery game where two beasts (player-controlled and AI) take turns firing projectiles at each other across destructible terrain. Inspired by Gunbound/Jinetes Bestias.

**Core Loop:**
1. Player 0 (human) aims and fires
2. Projectile travels with ballistics + wind
3. Impact damages terrain and/or beast
4. Turn switches to AI
5. AI automatically fires
6. Repeat until one beast reaches 0 HP

---

## Project Structure

```
Assets/_Project/
├── Scripts/
│   ├── AI/           # MinimaxAI, HeuristicEvaluator, ActionGenerator, StateSimulator, AIPlayer
│   ├── Combat/       # BallisticsSystem, DamageCalculator, WindSystem
│   ├── Core/         # Constants, GameManager, GameState, TurnManager
│   ├── Entities/
│   │   ├── Beast/    # BeastController, BeastData, BeastState
│   │   ├── Projectile/  # ProjectileController, ProjectilePool
│   │   └── Rider/    # RiderController
│   ├── Input/        # InputManager, AimingController
│   ├── Terrain/      # TerrainManager
│   ├── Tools/        # GameBootstrap, PlaygroundSetup, QuickSetup
│   └── UI/           # HUDController, HealthBarVisual, TurnTimerUI, WindIndicatorUI, AimingIndicator
├── Prefabs/
│   └── Projectiles/  # ProjectileController.prefab
├── Resources/
│   └── Projectiles/  # ProjectileController.prefab (for Resources.Load)
└── Scenes/
    └── test.unity
```

---

## Critical Files

### Bootstrap System (Runtime Orchestration)
- **GameBootstrap.cs** - Main orchestrator, wires TurnManager events, creates AIPlayer, handles shot flow
- **PlaygroundSetup.cs** - Creates beasts, terrain (Tilemap), systems (Wind, TurnManager, ProjectilePool)
- **QuickSetup.cs** - Auto-creates GameBootstrap/PlaygroundSetup if not in scene (prevents duplicates)

### Turn System
- **TurnManager.cs** - Phase machine (WAITING→AIM→SHOOTING→IMPACT→AIM...). Fires OnTurnStarted, OnShotSubmitted, OnImpactOccurred, OnGameOver
- **GameState.cs** - Data structures: WindData, BeastState, ShotInput, ExplosionResult
- **Constants.cs** - All global values (physics, AI, beast stats)

### Combat
- **BallisticsSystem.cs** - Deterministic projectile physics using Euler integration. NOT Rigidbody2D.
- **DamageCalculator.cs** - Damage with falloff based on distance
- **WindSystem.cs** - Wind affecting projectiles

### AI
- **AIPlayer.cs** - MonoBehaviour wrapper, calls MinimaxAI after 0.5s delay
- **MinimaxAI.cs** - Alpha-beta pruning, 3 difficulty levels
- **ActionGenerator.cs** - Generates angle/power combinations
- **HeuristicEvaluator.cs** - Evaluates game states
- **StateSimulator.cs** - Simulates turn outcomes

### Projectile System
- **ProjectileController.cs** - Uses BallisticsSystem.CalculatePosition() each frame, checks collisions with Raycast2D
- **ProjectilePool.cs** - Object pooling, loads prefab via Resources.Load()

### Terrain
- **TerrainManager.cs** - Destroys tiles in circular pattern via Tilemap.SetTile(null, ...)

---

## Key Technical Decisions

### 1. No Rigidbody2D for Projectiles
Projectiles use BallisticsSystem.CalculatePosition() directly in Update(). This ensures deterministic physics independent of Unity's physics engine.

### 2. Event-Based Turn Flow
```
TurnManager.StartTurn(playerId)
  → OnTurnStarted(playerId)
  → GameBootstrap.HandleTurnStarted()
    → if AI: StartCoroutine(AIDelayThenThink())
      → AIPlayer.Think()
        → OnAIDecision(ShotInput)
          → TurnManager.SubmitShot(angle, power)
            → OnShotSubmitted(ShotInput)
              → GameBootstrap.HandleShotSubmitted()
                → FireProjectile()
                  → ProjectileController.Initialize()
                    → BallisticsSystem.CalculatePosition() each frame
                      → CheckCollision() → Explode()
                        → OnImpact()
                          → TurnManager.OnProjectileImpact()
                            → CompleteImpact()
                              → StartTurn(nextPlayerId)
```

### 3. Layer Convention
- Layer 8 = Terrain
- Layer 9 = Beast
- Layer 10 = Projectile
- Projectile collisionMask must include layer 8 for terrain hits

### 4. Projectile Pool Location
ProjectileController.prefab exists in BOTH:
- `Assets/_Project/Prefabs/Projectiles/`
- `Assets/_Project/Resources/Projectiles/` (for Resources.Load)

### 5. Beast Creation
Uses GameObject + SpriteRenderer (NOT PrimitiveType.Capsule) to avoid CapsuleCollider/Rigidbody2D conflicts. Beasts have CircleCollider2D (trigger) + Rigidbody2D (Kinematic).

---

## Current State (2026-05-12)

### Working ✅
- ProjectileController.prefab created and loads
- ProjectilePool.Initialize() loads prefab via Resources.Load
- TurnManager initializes with Player 0 first
- GameBootstrap wires to TurnManager events
- AIPlayer created by GameBootstrap
- Terrain created as Tilemap with layer 8

### Issues Being Debugged 🔄
- **Turn timing issue** - GameBootstrap was connecting before TurnManager phase was AIM
  - Fix: Coroutine now waits up to 10 frames for TurnManager.Phase != WAITING
- **Projectile not visible** - Prefab exists but may not have SpriteRenderer sprite set
- **AI turn not executing** - isProcessingTurn bug was fixed in OnProjectileImpact()

### Known Issues
1. ProjectileController.prefab sprite may be missing (SpriteRenderer has no sprite assigned)
2. Terrain bounds may only cover one side (FillTerrainTiles bounds bug)
3. QuickSetup can still create duplicates if not stopped properly

---

## Debug Commands (MCP HTTP)

```python
# Health check
requests.get("http://127.0.0.1:8080/health")

# Get logs
requests.post("http://127.0.0.1:8080/api/command",
    json={"type": "read_console", "params": {"action": "get", "count": 30, "types": ["log"]}})

# Execute code
requests.post("http://127.0.0.1:8080/api/command",
    json={"type": "execute_code", "params": {"action": "execute", "code": "..."}})
```

---

## File Reference Map

| File | Purpose | Key Methods |
|------|---------|-------------|
| TurnManager.cs | Phase machine | StartTurn(), SubmitShot(), OnProjectileImpact() |
| GameBootstrap.cs | Orchestrator | HandleTurnStarted(), FireProjectile(), AIDelayThenThink() |
| AIPlayer.cs | AI wrapper | Think(), OnAIDecision() |
| BallisticsSystem.cs | Physics | CalculatePosition(), CalculateInitialVelocity(), SimulateTrajectory() |
| ProjectileController.cs | Projectile entity | Initialize(), Update(), Explode() |
| ProjectilePool.cs | Pool manager | GetProjectile(), ReturnProjectile() |
| TerrainManager.cs | Terrain destruction | DestroyCircularArea() |
| Constants.cs | Global values | All beast stats, physics constants, AI depth/power |

---

## Beast Types (from Constants.cs)

| Beast | HP | Damage | Radius | Weight | Gravity | Angle Range | Power |
|-------|-----|--------|--------|--------|---------|-------------|-------|
| BOULDER | 120 | 45 | 2.5 | 1.5 | 1.2 | 25-60° | 80 |
| GALE | 80 | 45 | 1.5 | 0.6 | 0.6 | 15-75° | 60 |
| MIRE | 100 | 20 | 2.0 | 1.0 | 1.0 | 30-65° | 50 |
| SPARK | 90 | 40 | 2.0 | 1.2 | 1.0 | 35-55° | 85 |
| THORN | 85 | 25 | 1.8 | 0.9 | 1.0 | 20-70° | 65 |
| CHASM | 75 | 50 | 2.5 | 0.8 | 1.1 | 40-80° | 95 |

---

## Validation Checklist

- [ ] TurnManager.Phase transitions correctly AIM→SHOOTING→IMPACT→AIM
- [ ] OnTurnStarted fires for both Player 0 and Player 1
- [ ] GameBootstrap.HandleTurnStarted enables controls for Player 0, starts AI coroutine for Player 1
- [ ] AIDelayThenThink waits 0.5s then calls AIPlayer.Think(state)
- [ ] AIPlayer.Think() produces ShotInput with angle/power
- [ ] HandleAIDecision calls TurnManager.SubmitShot()
- [ ] HandleShotSubmitted calls FireProjectile()
- [ ] FireProjectile gets projectile from Pool, initializes with velocity
- [ ] ProjectileController.Update() moves via BallisticsSystem.CalculatePosition
- [ ] CheckCollision detects terrain (layer 8) via Raycast2D
- [ ] Explode() calls OnImpact, returns projectile to pool
- [ ] HandleProjectileImpact calls TurnManager.OnProjectileImpact()
- [ ] CompleteImpact increments turn, switches player, calls StartTurn()

---

## Git Commits

- **3e945d3** - "Gameplay loop fixes + production documentation"
- **8f5bbae** - "Initial commit: Explow 2D artillery game foundation"

---

## Testing Instructions

1. Open Unity, open scene `Assets/_Project/Scenes/test.unity`
2. Ensure QuickSetup GameObject exists in scene (it auto-creates on Play)
3. Press Play
4. Expected logs:
   ```
   [QuickSetup] Created GameBootstrap
   [QuickSetup] Created PlaygroundSetup
   [GameBootstrap] Connected to TurnManager. Phase=AIM, CurrentPlayer=0
   [GameBootstrap] Turn started - Player 0
   [GameBootstrap] Player controls ENABLED
   ```
5. After 20 seconds or when human fires:
   ```
   [GameBootstrap] HandleShotSubmitted: playerId=0, angle=XX, power=XX
   [GameBootstrap] FireProjectile called: pos=..., angle=XX, power=XX
   [ProjectileController] Initialized at position ...
   ```
6. On impact:
   ```
   [GameBootstrap] Projectile IMPACT at (X, Y)
   [TurnManager] Impact detected, processing...
   [TurnManager] Player 1 turn started
   [GameBootstrap] AIDelayThenThink starting for Player 1
   [GameBootstrap] AI decision received: XX.X° / X.XX for Player 1
   ```

---

## Contact / Context

- **Game Designer:** User (Wick)
- **AI Assistant:** Claude via opencode (MiniMax-M2 model)
- **MCP:** Unity MCP plugin (com.coplaydev.unity-mcp) v9.6.9b
- **Unity Version:** 6000.3.15f1 (2D URP template)

---

*Last updated: 2026-05-12*
*This file is meant to give any AI a complete understanding of the project state and architecture.*