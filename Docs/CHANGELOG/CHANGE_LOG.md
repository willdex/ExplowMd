# CHANGE_LOG - Explow Artillery 2D

## 2026-05-10 - v0.1.1 (Alpha)

### Added
- PlaygroundSetup.cs - Crea bestias y terreno automáticamente en escena
- GameBootstrap.cs - Sistema de bootstrapping que conecta TurnManager, WindSystem, IA y proyectiles
- ProjectilePlaceholder.cs - Proyectil simple que usa BallisticsSystem para trayectoria
- Tags "Player" y "Beast" para detección de colisiones

### Changes
- PlaygroundSetup ahora crea bestias con tags correctos
- GameBootstrap ahora es el orchestrator principal del juego
- TurnManager se auto-inicializa si no encuentra WindSystem

### Technical Improvements
- Sistema de proyectil funcional con física simplificada
- IA responde automáticamente cuando es su turno (0.5s delay para parecer pensante)
- Loop de juego completo: Turno -> Disparo -> Impacto -> Cambio turno

### Known Issues
- Terreno no es destructible todavía (proximamente)
- Sin aiming visual (línea de predicción)
- Sin HUD funcional
- Proyectil no inflige daño real a bestias (solo log)

---

## 2026-05-10 - v0.1.0 (Alpha)

### Added
- Estructura de proyecto Unity 6.3 URP
- Sistema de documentación (ARCHITECTURE, IMPLEMENTATION, CHANGELOG)
- Constants.cs con enums globales
- BeastData.cs ScriptableObject
- GameState.cs y BeastState.cs
- BallisticsSystem.cs (sistema de física custom)
- TurnManager.cs (state machine de turnos)
- WindSystem.cs (viento global)
- DamageCalculator.cs (cálculo de daño)
- TerrainManager.cs (terreno destructible con Tilemap)
- BeastController.cs (control de bestia con Rigidbody2D)
- ProjectileController.cs (proyectil que usa BallisticsSystem)
- AimingController.cs + AimingIndicator.cs (sistema de aiming)
- HUDController.cs + HealthBarVisual.cs + TurnTimerUI.cs (UI del HUD)
- MinimaxAI.cs + ActionGenerator.cs + StateSimulator.cs + HeuristicEvaluator.cs (IA)
- AIPlayer.cs (controlador de IA)
- GameManager.cs (orquesta el juego)
- InputManager.cs (abstracción de input)
- NetworkManager.cs (placeholder para multiplayer futuro)

### Decisions Made
- Física híbrida: Rigidbody2D para bestias, BallisticsSystem custom para proyectiles
- Integrador Euler para trayectorias (no RK4, no Verlet)
- Tilemap para terreno destructible (no Sprite Shape)
- Cliente autoritativo con validación host para multiplayer
- URP como render pipeline
- Legacy Input System (por compatibilidad MCP)

### Technical Specs
- Time step: 0.02s (50hz)
- Depth IA: 3 para Medium, 2 para Easy, 4 para Hard
- Turn timer: 20s
- Wind change: cada 3 turnos

---

## Formato: YYYY-MM-DD - vX.Y.Z (Stage)
Stages: Alpha → Beta → RC → Release