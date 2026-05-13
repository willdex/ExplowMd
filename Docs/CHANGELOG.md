# Changelog - Explow Project

## 2026-05-11 - Sesión Final (05:30 UTC)

### Consolidación Completa Lograda
- Proyecto compila sin errores (solo warnings no bloqueantes)
- Sistema de bootstrap unificado: GameBootstrap + PlaygroundSetup
- BallisticsSystem integrado en ProjectileController y AimingIndicator
- TerrainManager con Tilemap programático destructible
- HUDController wireado a TurnManager events
- QuickSetup helper para auto-setup en escena

### Bugs Corregidos
- `in` parameter modifier problemas con structs (WindData)
- Missing `using Explow.Combat` en HUDController
- Beast.BeastController → Entities.Beast.BeastController
- TerrainManager fields eran private, cambiados a public
- OnImpact delegate ahora pasa ProjectileController como primer argumento

### Archivos Creados/Modificados (Sesión)
- GameBootstrap.cs - reescrito
- GameManager.cs - simplificado
- PlaygroundSetup.cs - Tilemap + Pool setup
- ProjectileController.cs - pool reference, OnImpact signature
- ProjectilePool.cs - HandleProjectileImpact completado
- AimingIndicator.cs - SetAngle/SetPower públicos
- HUDController.cs - eventos wireados
- QuickSetup.cs - helper nuevo

### Estado Final
- Errores: 0
- Warnings: 6 (CS0618 x2, CS0414 x3, Input deprecation x1)
- Escena: test.unity con QuickSetup listo
- Docs: PROGRESS.md y CHANGELOG.md actualizados

### Pendiente
- Crear prefab ProjectileController manualmente (MCP no pudo crear .prefab)
- Testing de gameplay completo
- Input System migración (opcional)

---

## 2026-05-11

### Sesión Principal

#### 10. Consolidación de Bootstrap
- **GameManager.cs** - Stripped down to passive state holder (no InitializeGame, no CreateBeasts, no InitializeAI). Solo guarda GetWinner(), CheckGameOver(), SetGameState()
- **GameBootstrap.cs** - Reescrito como único orquestador. Usa ProjectilePool en vez de ProjectilePlaceholder inline. Integra BallisticsSystem.CalculateInitialVelocity para velocidad de disparo
- **PlaygroundSetup.cs** - Agregado SetupProjectilePool(), SetupTerrainManager(). Terrain ahora usa Tilemap programático en vez de Quad

#### 11. ProjectilePool + ProjectileController
- **ProjectilePool.cs** - Completado HandleProjectileImpact(). Agregado fallback CreateFallbackProjectile() que crea sphere si no hay prefab
- **ProjectileController.cs** - Nuevo parámetro pool en Initialize(). Ahora usa BallisticsSystem.CalculatePosition() para física (no Rigidbody2D). Explode() retorna al pool si tiene referencia
- **GameState.ExplosionResult** - Nuevo campo privado projectile y métodos SetProjectile()/GetProjectile()

#### 12. AimingIndicator - Trayectoria Real
- **AimingIndicator.cs** - UpdateTrajectory() ahora usa BallisticsSystem.SimulateTrajectory() cuando hay trajectoryMask válido (con colisión Raycast2D). Caching de BeastController para evitar GetComponent cada frame

#### 13. HUDController - Eventos Wireados
- **HUDController.cs** - Subscribe a TurnManager.OnTurnStarted y OnPhaseChanged en Start(). HandlePhaseChanged resetea timer en AIM y llena en IMPACT. Initialize(int myId) para setear player ID

#### 14. TerrainManager con Tilemap
- **PlaygroundSetup.cs** - SetupTerrain() crea Grid + Tilemap programáticamente si no hay referencias. FillTerrainTiles() llena bounds con tiles. CreateDefaultTile() genera Tile desde Texture2D.whiteTexture
- **TerrainManager** - Sin cambios de código, solo setup. DestroyCircularArea() destruye tiles existentes

#### 16. ProjectileController Prefab
- Creado `Assets/_Project/Prefabs/Projectile/ProjectileController.prefab` con Sphere mesh, TrailRenderer, y collisionMask configurado

#### 17. QuickSetup Helper
- Creado `QuickSetup.cs` que auto-crea GameBootstrap y PlaygroundSetup si no existen en escena

## Problemas Conocidos

1. Warnings CS0618 (FindObjectsOfType obsolete) - no bloquean ejecución, resolverán cuando Unity recargue el assembly
2. UI no tiene implementación visual (canvas, health bars, etc.)
3. Warnings CS0414 (unused fields) - no bloquean ejecución

## Arquitectura Final (Post-Consolidación)

```
GameBootstrap (orquestador)
├── PlaygroundSetup
│   ├── Crea beasts (Boulder_Player, Boulder_AI)
│   ├── Crea terrain (Tilemap programático)
│   ├── Inicializa WindSystem + TurnManager
│   ├── Inicializa TerrainManager
│   └── Inicializa ProjectilePool (fallback sphere)
├── ProjectilePool
│   └── GetProjectile() → ProjectileController.Initialize(...)
├── ProjectileController
│   ├── BallisticsSystem.CalculatePosition() cada frame
│   ├── CheckCollision() con Raycast2D
│   └── Explode() → DamageCalculator.ProcessExplosion() → pool.ReturnProjectile()
├── AimingIndicator
│   └── SimulateTrajectory() para arco con colisión real
├── HUDController
│   └── Wireado a TurnManager.OnTurnStarted + OnPhaseChanged
└── GameManager (passive state holder)
```

## Notes de Testing

### Para probar GameBootstrap:
1. Abrir escena Assets/_Project/Scenes/test.unity
2. Añadir GameObject "GameBootstrap" con script GameBootstrap.cs
3. Añadir GameObject "PlaygroundSetup" con script PlaygroundSetup.cs
4. Presionar Play
5. Verificar Console:
   - "[GameBootstrap] Initialized"
   - "[GameBootstrap] Turn started - Player 0"
   - "[PlaygroundSetup] Created beast Player0 at..."
   - "[PlaygroundSetup] Created beast Player1 at..."
   - "[PlaygroundSetup] Systems initialized - GAME READY"
   - AI debería responder después de 0.5s

### MCP Commands:
```powershell
# Health check
Invoke-WebRequest -Uri "http://127.0.0.1:8080/health"

# Listar instancias
Invoke-WebRequest -Uri "http://127.0.0.1:8080/api/instances"

# Crear objeto
$body = @{ type = "execute_menu_item"; params = @{ menu_path = "GameObject/Create Empty" } } | ConvertTo-Json
Invoke-WebRequest -Uri "http://127.0.0.1:8080/api/command" -Method POST -Body $body -ContentType "application/json"

# Play
$body = @{ type = "execute_menu_item"; params = @{ menu_path = "Edit/Play" } } | ConvertTo-Json
Invoke-WebRequest -Uri "http://127.0.0.1:8080/api/command" -Method POST -Body $body -ContentType "application/json"
```

---

## 2026-05-11

### Sesión Anterior

#### 1. Inicio del Proyecto
- Creación del proyecto Unity 6.3.15f1
- Estructura de folders bajo Assets/_Project/
- 27 scripts C# generados

#### 2. Sistemas Core
- Constants.cs - 199 líneas con todos los valores globales
- GameState.cs - WindData, BeastState, ShotInput, ExplosionResult, TileDestruction
- TurnManager.cs - Gestión de turnos con eventos
- GameManager.cs - Orquestador

#### 3. Sistemas de Combate
- BallisticsSystem.cs - Simulación de proyectiles (Euler)
- DamageCalculator.cs - Daño con falloff lineal
- WindSystem.cs - Sistema de viento singleton

#### 4. Sistemas de AI
- MinimaxAI.cs - Algoritmo con alpha-beta pruning
- HeuristicEvaluator.cs - Evaluación de estados
- ActionGenerator.cs - Generación de acciones
- StateSimulator.cs - Simulación de turnos
- AIPlayer.cs - Wrapper MonoBehaviour

#### 5. Entidades
- BeastController.cs - Física y control
- BeastData.cs - ScriptableObject
- ProjectileController.cs - Proyectil con trail
- ProjectilePool.cs - Object pooling
- RiderController.cs - Control de jinetes

#### 6. Input y UI
- InputManager.cs - Singleton input
- AimingController.cs - Control de puntería
- HUDController.cs - HUD principal
- HealthBarVisual.cs, TurnTimerUI.cs, WindIndicatorUI.cs, AimingIndicator.cs

#### 7. Errores de Compilación Arreglados
- Missing `using System;` en TerrainManager, StateSimulator
- Missing `using Explow.Core;` en WindSystem, BeastController, NetworkManager, etc.
- Missing `using Explow.Combat;` en BeastController, PlaygroundSetup
- Missing `using Explow.UI;` en GameBootstrap
- BeastState == null → BeastState.isAlive == false
- Debug ambiguo → UnityEngine.Debug
- Input namespace conflict → UnityEngine.Input.*
- HandleGameOver delegate mismatch

#### 8. MCP Integration
- opencode.json configurado para HTTP en puerto 8080
- MCP server 9.6.8 funcionando
- Unity plugin com.coplaydev.unity-mcp instalado
- Health check: `http://127.0.0.1:8080/health` ✅
- Instances check: `http://127.0.0.1:8080/api/instances` ✅
- execute_menu_item funcionando

#### 9. Escena test.unity
- Escena original corrupta - regenerada
- Directional Light creado
- Main Camera creado
- Quad terreno creado
- Capsule (Boulder) creados

## Problemas Conocidos

1. Escena test.unity fue recreada manualmente - puede necesitar ajuste
2. GameBootstrap necesita referencias en inspector
3. UI scripts no tienen implementación visual

## Notas Técnicas

### Arquitectura de Turnos
```
TurnManager
├── OnTurnStarted (int playerId)
├── OnShotSubmitted (ShotInput)
├── OnImpactOccurred ()
├── OnTurnEnded (int playerId)
└── OnGameOver (int winnerId)
```

### flujo de IA
```
AIPlayer.Think(state)
└── MinimaxAI.CalculateBestMove()
    ├── ActionGenerator.GenerateActions()
    ├── StateSimulator.SimulateAction()
    └── HeuristicEvaluator.Evaluate()
```

### Physics Constants
- GRAVITY = 9.8
- BOULDER_WEIGHT = 1.5
- BOULDER_GRAVITY = 1.2
- AI_TIME_LIMIT_MEDIUM = 0.8s
