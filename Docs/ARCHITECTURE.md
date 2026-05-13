# Explow Project - Arquitectura y Estado

**Última actualización:** 2026-05-11
**Estado:** EN PROGRESO

## Resumen
Explow es un juego de artillería 2D táctico (turn-based) desarrollado con Unity 6.3.15f1.

## Estructura del Proyecto

```
Assets/_Project/
├── Scripts/
│   ├── AI/           # MinimaxAI, HeuristicEvaluator, ActionGenerator, StateSimulator, AIPlayer
│   ├── Combat/       # BallisticsSystem, DamageCalculator, WindSystem
│   ├── Core/         # Constants, GameManager, GameState, TurnManager
│   ├── Entities/     # Beast, Projectile, Rider
│   ├── Input/        # InputManager, AimingController
│   ├── Multiplayer/  # NetworkManager
│   ├── Terrain/      # TerrainManager
│   ├── Tools/       # GameBootstrap, PlaygroundSetup
│   └── UI/           # HUDController, HealthBarVisual, TurnTimerUI, WindIndicatorUI, AimingIndicator
├── Data/
│   ├── Beasts/       # BeastData_Boulder.asset, BeastData_Gale.asset
│   └── Terrains/
├── Prefabs/
│   ├── Beasts/
│   ├── Effects/
│   ├── Projectiles/
│   └── UI/
└── Scenes/
    └── test.unity
```

## Sistemas Implementados

### Core Systems
- **Constants** - Valores globales (física, AI, combate)
- **GameState** - Estado del juego (WindData, BeastState, ShotInput, ExplosionResult, TileDestruction)
- **GameManager** - Orquestador principal
- **TurnManager** - Gestión de turnos

### Combat Systems
- **BallisticsSystem** - Simulación de proyectiles
- **DamageCalculator** - Cálculo de daño con falloff
- **WindSystem** - Sistema de viento con variación

### AI Systems
- **MinimaxAI** - Algoritmo Minimax con 3 profundidades
- **HeuristicEvaluator** - Evaluación de estados
- **ActionGenerator** - Generación de acciones
- **StateSimulator** - Simulación de estados
- **AIPlayer** - MonoBehaviour wrapper

### Entity Systems
- **BeastController** - Control de beasts
- **BeastData** - ScriptableObject de stats
- **ProjectileController** - Proyectiles
- **ProjectilePool** - Pool de objetos
- **RiderController** - Control de jinetes

## Fases del Proyecto

### Fase 1: Estructura ✅
- 27 scripts C# generados
- Namespaces configurados (Explow.*)
- Proyecto Unity 6.3.15f1 con 2D URP

### Fase 2: MCP Integration ✅
- MCP server funcionando en puerto 8080
- opencode.json configurado para HTTP
- Unity plugin instalado (com.coplaydev.unity-mcp)

### Fase 3: Game Bootstrap ✅
- GameBootstrap.cs - Orquestador runtime
- PlaygroundSetup.cs - Setup automático de escena
- TurnManager con eventos
- WindSystem singleton

### Fase 4: AI Opponent ✅
- MinimaxAI con Easy/Medium/Hard
- Heurísticas de distancia, HP, viento
- ActionGenerator con variaciones de ángulo/power

### Fase 5: Gameplay Loop ⏳
- Sistema de turnos completo
- Disparo con física
- Detección de impacto
- Daño con falloff

## Lo Que Falta

1. **Testing** - Jugar el loop completo
2. **UI del aiming** - Indicador visual de disparo
3. **Terreno destructible** - TerrainManager
4. **Pool de proyectiles** - Integrar ProjectilePool
5. **Beasts adicionales** - Gale, Mire, Spark, Thorn, Chasm

## Integración MCP

**Estado:** ✅ Funcional
- Servidor: http://127.0.0.1:8080
- Proyecto detectado: Explow v6000.3.15f1
- Hash: 09c453eb4f0e353f

**Comandos probados:**
- `execute_menu_item` ✅
- `read_console` ✅
- `get_instances` ✅

## Próximos Pasos

1. Testing del gameplay loop completo
2. Implementar UI de aiming
3. Integrar ProjectilePool con física real
4. Testear IA con distintos difficulty levels
