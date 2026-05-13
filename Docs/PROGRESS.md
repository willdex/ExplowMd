# Progress - Explow Project

**Fecha:** 2026-05-11 05:45 UTC
**Estado:** Git commit hecho, listo para testing de gameplay

## Estado de Git
- ✅ Commit inicial creado (8f5bbae)
- Archivos de proyecto comprometidos
- Archivos MCP no comprometidos (mcp_*.py, temp_script.py, 0.0.0a0)

## Testing Checklist

### Para probar:
1. Abrir escena `Assets/_Project/Scenes/test.unity`
2. Crear GameObject "QuickSetup" → añadir `QuickSetup.cs`
3. Presionar **Play**

### Logs esperados:
```
[QuickSetup] Created GameBootstrap
[QuickSetup] Created PlaygroundSetup
[PlaygroundSetup] Created beast Player0 at (-5, 2)
[PlaygroundSetup] Created beast Player1 at (5, 2)
[PlaygroundSetup] Systems initialized - GAME READY
[GameBootstrap] Initialized - Player 0 starts!
[GameBootstrap] Turn started - Player 0
[GameBootstrap] Player controls ENABLED
[GameBootstrap] Turn started - Player 1
[GameBootstrap] AI decision: XX.X° / X.XX
[GameBootstrap] Shot fired: XX.X° / X.XX
[GameBootstrap] Projectile hit at (X, X)
[PlaygroundSetup] TerrainManager initialized
```

## Completado ✅

### Proyecto Base
- [x] 29 scripts C# en lugar
- [x] Namespace Explow configurado
- [x] Unity 6.3.15f1 con 2D URP
- [x] MCP plugin instalado (HTTP port 8080)

### Sistemas Core - CONSOLIDADO
- [x] Constants.cs - valores globales
- [x] GameState.cs - estructuras de datos
- [x] TurnManager.cs - gestión de turnos
- [x] GameManager.cs - **stripped down to passive state holder** (no init, no AI)

### Bootstrap Unificado
- [x] GameBootstrap.cs - **único orquestador** (reemplaza ProjectilePlaceholder inline)
- [x] PlaygroundSetup.cs - setup de escena (beasts, terrain, systems, projectile pool)
- [x] GameManager.cs - solo estado pasivo (GetWinner, CheckGameOver, SetGameState)

### Combate - INTEGRADO
- [x] BallisticsSystem.cs - física de proyectil (usado por ProjectileController y AimingIndicator)
- [x] DamageCalculator.cs - cálculo de daño con falloff
- [x] WindSystem.cs - viento con randomización

### ProyectoPool - INTEGRADO
- [x] ProjectilePool.cs - **fallback to sphere si no hay prefab**
- [x] ProjectileController.cs - usa BallisticsSystem.CalculatePosition para física
- [x] HandleProjectileImpact completado

### AI
- [x] MinimaxAI.cs
- [x] HeuristicEvaluator.cs
- [x] ActionGenerator.cs
- [x] StateSimulator.cs
- [x] AIPlayer.cs

### UI - WIRED TO EVENTS
- [x] HUDController.cs - suscribe a TurnManager.OnTurnStarted + OnPhaseChanged
- [x] AimingIndicator.cs - usa SimulateTrajectory para arco con colisión real
- [x] WindIndicatorUI.cs
- [x] HealthBarVisual.cs
- [x] TurnTimerUI.cs

### Terrain - TILEMAP
- [x] TerrainManager.cs - destruye tiles circulares
- [x] PlaygroundSetup - crea Tilemap programáticamente si no hay referencias
- [x] FillTerrainTiles() - llena el área con tiles de color terrenoColor

## En Progreso 🔄

### Testing de Turno Completo
- [ ] Verificar física de proyectil (BallisticsSystem)
- [ ] Confirmar detección de impacto
- [ ] Verificar daño y HP

## Pendiente ⏳

### Alta Prioridad
- [ ] Crear ProjectileController.prefab (手动 via Unity Editor)
- [ ] Implementar input del jugador (AimingController)
- [ ] Configurar AimingIndicator en escena

### Media Prioridad
- [ ] BeastData para más beasts (Gale, Mire, Spark, etc.)
- [ ] Sistema de Special Abilities
- [ ] Efectos de partículas (explosión)

### Baja Prioridad
- [ ] Sonidos y música
- [ ] Pantalla de Game Over real
- [ ] Migrar a Texture2D/SpriteMask para destrucción de píxeles

## Arquitectura Final

```
GameBootstrap (orquestador, en escena)
├── PlaygroundSetup (setup inicial, en escena)
│   ├── Crea beasts (Boulder_Player, Boulder_AI)
│   ├── Crea terrain (Tilemap programático)
│   ├── Inicializa WindSystem + TurnManager
│   ├── Inicializa TerrainManager
│   └── Inicializa ProjectilePool (con fallback sphere)
├── HUDController (wireado a TurnManager events)
├── AimingIndicator (SimulateTrajectory con colisión)
├── GameManager (solo estado pasivo)
└── AIPlayer (creado por GameBootstrap)
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