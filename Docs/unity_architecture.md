# Unity Architecture

## Namespace Structure
```
Explow.Core        - Constants, GameState, TurnManager, GameManager
Explow.Combat      - BallisticsSystem, DamageCalculator, WindSystem
Explow.Entities    - Beast, Projectile, Rider (with sub-namespaces)
Explow.Terrain     - TerrainManager
Explow.AI          - MinimaxAI, HeuristicEvaluator, ActionGenerator, StateSimulator, AIPlayer
Explow.UI          - HUDController, HealthBarVisual, TurnTimerUI, WindIndicatorUI, AimingIndicator
Explow.Input       - InputManager, AimingController
Explow.Tools       - GameBootstrap, PlaygroundSetup, QuickSetup
```

## Key Classes
- **GameBootstrap**: Runtime orchestrator, creates systems, wires events
- **PlaygroundSetup**: Scene init (beasts, terrain, systems)
- **TurnManager**: Phase state machine, OnTurnStarted/OnShotSubmitted/OnImpactOccurred events
- **ProjectilePool**: Object pool for projectiles

## Project Structure
```
Assets/_Project/
├── Scripts/{namespace}/
├── Data/Beasts/         (ScriptableObjects)
├── Prefabs/
└── Scenes/
```

## MonoBehaviour Patterns
- Singleton via FindFirstObjectByType (no static instance)
- Events for decoupling (OnTurnStarted, OnImpact, etc.)
- SerializeField for inspector exposure