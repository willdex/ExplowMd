# SPRINT 01 - Loop de Combate Completo
## Artillery 2D - Jinetes & Bestias
## Fecha inicio: 2026-05-10 | Duración estimada: 1 semana

---

## OBJETIVO DEL SPRINT

Crear un prototipo jugable completo con:
- 1 bestia (Boulder) controllable por jugador humano
- 1 bestia (Boulder) controlada por IA Minimax
- Terreno destructible tipo canyon
- Sistema de turnos funcional
- Loop de combate: apuntar → disparar → impacto → cambio turno

---

## METAS POR DÍA

### Día 1 (Hoy)
- [x] Estructura de carpetas del proyecto
- [x] Sistema de documentación (CHANGELOG, DECISIONES)
- [x] Constants.cs y enums globales
- [x] BeastData.cs ScriptableObject
- [x] GameState.cs y BeastState.cs
- [x] BallisticsSystem.cs
- [x] TurnManager.cs
- [x] WindSystem.cs
- [x] DamageCalculator.cs
- [ ] TerrainManager.cs
- [ ] BeastController.cs

### Día 2
- [ ] ProjectileController.cs
- [ ] ProjectilePool.cs
- [ ] AimingController.cs
- [ ] AimingIndicator.cs
- [ ] HUDController.cs + HealthBarVisual.cs + TurnTimerUI.cs
- [ ] WindIndicatorUI.cs

### Día 3
- [ ] MinimaxAI.cs
- [ ] ActionGenerator.cs
- [ ] StateSimulator.cs
- [ ] HeuristicEvaluator.cs
- [ ] AIPlayer.cs
- [ ] GameManager.cs (actualizar con nueva estructura)
- [ ] InputManager.cs

### Día 4-5
- [ ] Crear escena Game.scene
- [ ] Configurar Tilemap con terreno Canyon
- [ ] Crear prefabs placeholder para Boulder
- [ ] Integrar todos los sistemas
- [ ] Playtest y debug

### Día 6-7
- [ ] Fix bugs encontrados
- [ ] Ajustar balance (HP, daño, timer)
- [ ] Demo funcional

---

## ARCHIVOS CREADOS

### Core/
- Constants.cs (enums + valores balance)
- GameManager.cs
- GameState.cs
- TurnManager.cs

### Combat/
- BallisticsSystem.cs
- DamageCalculator.cs
- WindSystem.cs

### Terrain/
- TerrainManager.cs

### Entities/Beast/
- BeastData.cs
- BeastController.cs

### Entities/Projectile/
- ProjectileController.cs
- ProjectilePool.cs

### Entities/Rider/
- RiderController.cs

### Input/
- InputManager.cs
- AimingController.cs

### UI/
- HUDController.cs
- HealthBarVisual.cs
- TurnTimerUI.cs
- WindIndicatorUI.cs
- AimingIndicator.cs

### AI/
- MinimaxAI.cs
- ActionGenerator.cs
- StateSimulator.cs
- HeuristicEvaluator.cs
- AIPlayer.cs

### Multiplayer/
- NetworkManager.cs (placeholder)

---

## PRÓXIMOS PASOS (SPRINT 02)

1. Implementar segunda bestia (Gale)
2. Terreno más elaborado (múltiples mapas)
3. Sistema de habilidades especiales
4. Efectos visuales (VFX, partículas)
5. Animaciones básicas para bestias

---

## NOTAS

- Usamos Unity 6.3 LTS con 2D URP
- Input System: Legacy (por compatibilidad MCP)
- Física: híbrida (Rigidbody2D para bestias, BallisticsSystem custom para proyectiles)
- IA: Minimax con depth 3, heurísticas por bestia

---

*Plan sujeto a cambios según feedback en playtest.*