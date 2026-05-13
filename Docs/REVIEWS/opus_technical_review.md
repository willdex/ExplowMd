Here's a full senior technical review of the Explow project based on all documentation.
  ::view-transition-group(*),
  ::view-transition-old(*),
  ::view-transition-new(*) {
    animation-duration: 0.25s;
    animation-timing-function: cubic-bezier(0.19, 1, 0.22, 1);
  }
VvisualizeVvisualize show_widget
Critical Issues
Three blockers are preventing any playable game loop from running:
The missing ProjectileController.prefab is the most impactful: the object pool returns null, meaning no shot can be visualized regardless of how well ballistics calculates. This cascades — AI visualization, aiming indicator feedback, and terrain destruction callbacks all depend on a live projectile in the scene.
The AI branch in TurnManager.OnTurnStarted is unwired, which means any non-human turn silently stalls. Since TurnManager is the phase authority, a stalled turn locks the entire game loop.
QuickSetup creating duplicate capsule instances on repeated Play is a development workflow blocker. It will waste debugging time because system state becomes corrupted before any logic runs.
Immediate fix order:

Create ProjectileController.prefab with a placeholder SpriteRenderer, wire pool return on death
Add null guard + log to OnTurnStarted, confirm AIPlayer.Think() is subscribed
Add FindFirstObjectByType<T>() existence check in QuickSetup before instantiating any singleton


Multiplayer Risks
Determinism is the existential risk for the entire MP architecture. The docs state "no float race conditions" as a requirement but there is no evidence of a test harness validating this. BallisticsSystem uses float math with Vector2, which in Unity is 32-bit and can diverge between host and client if update order or Time.fixedDeltaTime ever jitters.
Risk breakdown:
WindData is described as a struct passed into CalculatePosition — if wind is generated on host and broadcast correctly as a deterministic seed rather than a float value, this is safe. But the docs don't confirm this. Wind must be integer-seeded and reproduced from seed, not from float broadcast.
BallisticsSystem.TIME_STEP = 0.02 (fixed substep) is the right call, but it must be decoupled from Unity's Time.deltaTime entirely. Any Update() call feeding into ballistics will break determinism. Ballistics must run only in a fixed-step accumulator or FixedUpdate.
TileBase set to null for terrain destruction is simple enough to sync, but the DestroyTilesAt(Vector2 center, float radius) signature passes a float radius — this should be an int tile count to avoid rounding disagreement between clients.
Photon PUN 2 free tier at 20 CCU is fine for dev/alpha, but plan for the moment you hit a dedicated server wall. The host-authoritative model is correct; make sure input RPCs (IniciaTurno, FinalizaTurno) carry a sequence number for replay validation.

Terrain System Risks
The tilemap MVP is appropriate for the current stage. The main risks are:
The FillTerrainTiles bounds calculation bug (listed in known issues as one-sided) must be fixed before any terrain destruction test is meaningful — you can't validate explosion radius on a half-generated map.
EXPLOSION_RADIUS_TILES = 3f stored as a float is a latent MP desynch risk. Store as int, convert to tile units consistently.
The migration path to pixel-level destruction (Sprite Shape + shader mask) is noted as future but should be architected now. The TerrainManager interface should hide its implementation — expose DestroyAt(Vector2Int center, int radiusTiles), not tilemap internals. This means swapping the backend later won't break ProjectileController's impact callback.
TERRAIN_SYNC_BATCH_SIZE = 20 is reasonable, but with 3-tile radius destruction per shot, a full game match could generate hundreds of delta events. Add a dirty-region coalescing step: buffer all tile changes from one explosion into one RPC, not 20 individual ones.

Ballistics System Improvements
The core formula and the decision to avoid Rigidbody2D for projectiles are both correct and production-safe.
CalculateInitialVelocity(float angle, float power, float powerMax) should clamp angle to BeastData.angleRange inside the function, not rely on the caller. This prevents AI or network inputs from firing out-of-spec shots.
The OnImpact delegate is the right decoupling pattern, but it currently has no contract on what payload it carries. Define an ImpactData struct (Vector2 position, float radius, ProjectileType type, int damage) before wiring terrain and damage to it. Retrofitting this later will break multiple subscribers.
WIND_AFFECTION_MULTIPLIER = 0.02 applied to projectileWeight from BeastData means heavier beasts (BOULDER, SPARK) are nearly unaffected by wind. Verify this is intentional — in Gunbound, wind heavily affects lighter shot types. Consider making windAffection a per-projectile-type coefficient, not just a global multiplier on weight.
ProjectileType.BOUNCE and SPLIT have no collision or spawn logic described. These must be spec'd before implementing — BOUNCE needs a reflection vector from terrain normal, SPLIT needs a spawner pool reference. Neither is trivial to retrofit into the current ProjectilePool.

Recommended Architecture Changes
GameBootstrap coupling risk. As the "runtime orchestrator that creates systems and wires events," it will become a 500-line God class if unchecked. Introduce a ServiceLocator or simple Registry<T> so systems register themselves and GameBootstrap only sequences initialization order, not references.
ShotInput is underspecified. It currently holds angle, power, useSpecialAbility. For multiplayer replay and AI evaluation it needs playerID, turnSequenceNumber, and beastID. Add these now — the struct is cheap and these fields are essential for any host-side validation.
MinimaxAI + StateSimulator depth is unbounded. With 6 beast types, variable wind, and 6 projectile types, search space can explode. Add a hard maxDepth constant in Constants.cs and a time-budget guard (System.Diagnostics.Stopwatch, abort if > 100ms). Log when the budget is hit so you can tune it.
FindFirstObjectByType as a singleton pattern is fine for now but creates implicit ordering dependencies. If two systems call it during Awake() before the target exists, you get null. Add a [RuntimeInitializeOnLoadMethod] ordering in GameBootstrap, or use a simple static Instance with a null check and informative error.
Namespace Explow.Input conflicts with Unity's own UnityEngine.Input. Rename to Explow.PlayerInput before the Input System migration — the deprecation warning is a preview of a future breaking change.

Technical Priorities
Ranked by production impact:
PrioritySystemReasonP0Fix 3 critical blockersNothing runs without theseP1ImpactData struct + impact callback contractUnblocks terrain + damage wiringP1Ballistics determinism test harnessUnblocks any multiplayer workP2ShotInput struct expansionRequired for AI replay + MPP2Player input + AimingIndicatorFirst human-playable loopP2Terrain destruction on impactCore gameplay loopP3AI depth + time budget guardPrevents hitches in AI turnsP3HUD integrationQoL, not loop-blockingP4Photon integrationOnly safe after local loop is stableP5Art pipeline / 3D modelsNo dependency on code systems

MiniMax Task Queue
Compact implementation tasks in dependency order:
TASK-01 [P0] Create ProjectileController.prefab
  - Add SpriteRenderer (32x32 placeholder)
  - Wire ProjectilePool return on HP=0 or terrain hit
  - Validate: pool.Get() returns non-null object in scene

TASK-02 [P0] Fix AI turn execution
  - Confirm AIPlayer.Think() subscribes to TurnManager.OnTurnStarted
  - Add log: "AI turn started for {beastID}"
  - Validate: AI fires a shot in sandbox scene

TASK-03 [P0] Fix QuickSetup duplicates
  - Check FindFirstObjectByType<T>() before each Instantiate
  - Validate: 3x Play/Stop cycles produce exactly 1 of each singleton

TASK-04 [P1] Define ImpactData struct
  - Fields: Vector2 position, float radius, ProjectileType type, int rawDamage
  - Replace OnImpact delegate signature
  - Update all subscribers (TerrainManager, DamageCalculator)

TASK-05 [P1] Ballistics determinism test
  - Run CalculatePosition 1000 steps with same seed, two instances
  - Assert Vector2 equality to 4 decimal places
  - Add to CI / Editor Test Runner

TASK-06 [P2] Expand ShotInput struct
  - Add: int playerID, int turnSequenceNumber, int beastID
  - Validate: TurnManager reads and logs all fields on shot submission

TASK-07 [P2] AimingController input loop
  - Read angle (left stick / mouse X), power (hold button charge)
  - Clamp to BeastData.angleRange and powerMax
  - Feed ShotInput to TurnManager on release

TASK-08 [P2] AimingIndicator trajectory preview
  - Sample CalculatePosition at 20 steps
  - Render as LineRenderer or GL.Lines
  - Update every frame during AIM phase only

TASK-09 [P2] Wire terrain destruction
  - Subscribe TerrainManager.DestroyTilesAt to ImpactData event
  - Change radius param from float to int tiles
  - Validate: explosion leaves circular gap in tilemap

TASK-10 [P3] AI depth budget guard
  - Add MaxDepth = 4, MaxThinkMs = 100 to Constants.cs
  - Wrap MinimaxAI search in Stopwatch, return best move found on timeout
  - Log when timeout triggers

TASK-11 [P3] Wind as seeded value
  - Replace WindSystem float broadcast with int seed + RNG
  - Reproduce on client from seed only
  - Validate: two instances with same seed produce identical wind vectors

TASK-12 [P4] HUD integration
  - Wire HealthBarVisual to BeastController HP events
  - Wire TurnTimerUI to TurnManager phase events
  - Wire WindIndicatorUI to WindSystem change event

Recommended Next Milestone
Milestone: "First Playable Local Match"
Definition of done:

Two beasts placed on a valid generated terrain
Human player can aim, charge, and fire a visible projectile
Projectile follows correct parabolic arc with wind deflection
Terrain deforms on impact
Damage applied, HP bar updates
AI takes its turn automatically and fires
Turn switches correctly until one beast reaches 0 HP

This milestone requires TASK-01 through TASK-09 above. It produces a locally playable, testable game loop without any multiplayer infrastructure — which is exactly the right state before touching Photon. Everything after this milestone (MP, art, VFX, audio) has a stable foundation to build on.