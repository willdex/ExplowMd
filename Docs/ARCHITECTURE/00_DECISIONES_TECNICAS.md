# 00_DECISIONES_TECNICAS - Artillery 2D Jinetes & Bestias
## Versión: 0.1 | Fecha: 2026-05-10

---

## 1. FILOSOFÍA DE FÍSICA

### Decisión: HÍBRIDA (NO puramente custom physics)

**Bestias → Rigidbody2D**
- Colisiones entre bestias, terreno, obstacles
- Determinismo logrado con FixedUpdate + interpolation
- Config: Dynamic rigidbody, mass=1, drag=0, fixed angle

**Proyectiles → BallisticsSystem (cinemática pura)**
- Sin Rigidbody, cálculo frame-by-frame
- Euler integration con sub-steps para collision detection
- Viento aplica como aceleración constante
- WindFactor = 0.02 (multiplicador para no dominar gravedad)

**Justificación técnica:**
- Rigidbody es determinista si: fixed timestep constante + same initial conditions
- Proyectiles son "ammo" → no necesitan física real, solo trayectoria predecible
- Bestias sí necesitan física (colisiones, knockback, terrain response)

### Integrador: EULER (para BallisticsSystem)
- Simple, suficiente para artillery (gravedad constante)
- Si precisión insuficiente → RK4 con 4 sub-steps
- No Verlet (no tenemos constraints)

### Time Step: 0.02s (50hz) tanto para physics como ballistic
- Sync con FixedUpdate de Unity
- En multiplayer: host comanda, clientes interpolan

---

## 2. SISTEMA DE TURNOS

### State Machine (5 estados)

```
WAITING → AIM → SHOOTING → IMPACT → TURN_END → (next player) WAITING
```

**WAITING:** Turno no iniciado, esperando trigger
**AIM:** Jugador puede apuntar, 20s de timer
**SHOOTING:** Proyectil en vuelo, no hay input
**IMPACT:** Pausa 0.5s post-impacto, mostrar daño
**TURN_END:** Cleanup, cambiar turno

### Timer
- 20 segundos por turno
- Timeout = autofire con min power (0.3)
- Visual: círculo que se cierra alrededor de bestia activa

---

## 3. TERRENO DESTRUCTIBLE

### Tecnología: Tilemap + Custom Destruction

**Tilemap layers:**
- Ground (collision, indestructible)
- Destructible (tiles que se eliminan)
- Background (decorativo)

**Destrucción circular:**
- Radio en tiles (3-5 tiles según proyectil)
- Al destruir: SetTile(null) en área circular
- Sync: Batch de tiles modificados, broadcast cada 200ms o >20 tiles

**Placeholder para proto:**
- Tilemap con tiles de color sólido (32x32)
- No shader de máscara todavía

---

## 4. IA MINIMAX

### Configuración por dificultad

| Dificultad | Depth | Aleatoriedad | Tiempo máx |
|------------|-------|--------------|------------|
| Easy       | 2     | ±20          | 0.5s       |
| Medium     | 3     | ±10          | 0.8s       |
| Hard       | 4     | ±2           | 1.0s       |

### Discretización de acciones
- Ángulo: steps de 5°
- Potencia: steps de 10%
- Movimiento: 3 posiciones posibles (quieto, izq, der)

### Heurísticas por bestia (evaluación de estado)

**BOULDER:**
```
score = damageDealt * 1.2
      + heightAdvantage * 30
      - distanceToEnemy * 2
```
- Valora daño directo, altura, distancia corta

**GALE:**
```
score = windFavorability * 50
      + evasionPotential * 40
      - exposure * 30
```
- Valora viento a favor, posicionamiento, minimiza exposición

**MIRE:**
```
score = slowZonesCreated * 20
      + enemySlowTime * 15
      + damage * 0.8
```
- Valora control de campo sobre daño directo

**SPARK:**
```
score = bounceBonus * 25
      + directHitBonus * 35
      + positioningLateral * 20
```
- Valora rebotes y ángulos indirectos

**THORN:**
```
score = spikesInField * 15
      + spikeChainPotential * 40
      + damage * 0.9
```
- Valora presión psicológica y espinas en campo

**CHASM:**
```
score = directHits * 50
      + terrainHolesNearEnemy * 30
      + distanceBonus * 1.5
```
- Valora sniper y terreno

---

## 5. MULTIPLAYER (FUTURO)

### Arquitectura: Cliente autoritativo con validación Host

**Fases de un turno en red:**
1. Host → Broadcast: "Player X turn start" (RPC)
2. Player X → Host: "SubmitShot(angle, power)" (RPC)
3. Host valida input (límites de bestia, línea de visión)
4. Host → All: "ExecuteShot(angle, power, seed)" (RPC)
5. Todos ejecutan same BallisticsSystem con same seed
6. Host → All: "ImpactResult(damage, tilesDestroyed, newPositions)" (RPC)

### Anti-cheat light
- Validación de inputs en host
- Rate limiting: max 3 inputs/segundo por cliente
- Snap de posición si diverge >0.5 unidades

---

## 6. RENDER PIPELINE

### Decisión: URP (Universal Render Pipeline)

**Justificación:**
- Mejor para efectos 2D (sprites, shaders)
- VFX Graph compatible si necesitamos partículas avanzadas
- Mobile-ready desde el inicio
- Documentación robusta

**Settings 2D URP:**
- Color Space: Gamma (para 2D más simple)
- Anti-aliasing: 4x para PC
- Texture Quality: Full Res

---

## 7. INPUT SYSTEM

### Decisión: Legacy Input (por compatibilidad con unity-mcp)

**Mapeo de controles:**
- Mouse Move: Apuntar (angle)
- Left Click Hold: Cargar potencia
- Left Click Release: Disparar
- A/D: Mover bestia (opcional por turno)
- Space: Saltar (si bestia lo soporta)
- ESC: Pause menu

### Aiming visual
- Línea punteada desde bestia en dirección de angle
- Arco de predicción (2 segundos de vuelo)
- Color de línea: blanco normal, amarillo cuando potencia alta

---

## 8. ESTRUCTURA DE DATOS CORE

### BeastData (ScriptableObject)
```csharp
- beastName: string
- beastType: enum (BOULDER, GALE, MIRE, SPARK, THORN, CHASM)
- maxHP: int
- moveSpeed: float
- jumpHeight: float
- angleMin, angleMax: float
- powerMax: float
- projectileWeight: float (afecta caída)
- baseDamage: float
- explosionRadius: float
- moveType: enum (HEAVY_JUMP, FLOAT, CRAWL, DASH, ROLL, TELEPORT)
- specialAbility: SpecialAbilityData
```

### GameState (para IA y networking)
```csharp
- currentPlayerId: int
- turnPhase: enum
- wind: WindData
- beasts: List<BeastState>
- turnTimeRemaining: float
- turnCount: int
```

### BeastState
```csharp
- ownerId: int
- position: Vector2
- velocity: Vector2
- hp: float
- damageState: enum (INTACT, WOUNDED, DYING)
- hasMovedThisTurn: bool
- turnsUntilSpecialReady: int
```

---

## 9. ORDEN DE IMPLEMENTACIÓN

```
SPRINT 1 (Meta: Loop jugable completo):
1. Constants.cs + Enums
2. BeastData.cs
3. GameState.cs
4. BallisticsSystem.cs
5. TurnManager.cs
6. BeastController.cs
7. ProjectileController.cs
8. WindSystem.cs
9. TerrainManager.cs + DestructibleTilemap.cs
10. DamageCalculator.cs
11. AimingController.cs + AimingIndicator.cs
12. HUDController.cs + HealthBarVisual.cs + TurnTimerUI.cs
13. MinimaxAI.cs + ActionGenerator.cs + StateSimulator.cs + HeuristicEvaluator.cs
14. AIPlayer.cs
15. GameManager.cs
16. InputManager.cs

FINAL: Escena Game.scene funcional con Boulder vs IA
```

---

## 10. TRADE-OFFS ACEPTADOS

| Área | Trade-off | Justificación |
|------|-----------|---------------|
| Physics | Euler vs RK4 | Performance > precisión por ahora |
| Terreno | Tiles rectangulares vs orgánico | Velocidad de implementación |
| IA | Depth limitado | Tiempo real <1s |
| Multiplayer | Cliente autoritativo | Simplicidad para proto |
| Arte | Placeholders vs final | Focus en sistemas jugables |

---

## 11. MIGRACIONES FUTURAS

| Si el juego crece | Migrar a |
|-------------------|----------|
| +20 CCU concurrentes | Photon Fusion o servidor propio |
| Terreno más visual | Sprite Shape + shader máscara |
| Precisión balística | RK4 + substeps |
| IA más smart | Monte Carlo Tree Search |
| Replays | Estado completo por turno |

---

*Documento vivo - Actualizar con cada decisión técnica tomada.*