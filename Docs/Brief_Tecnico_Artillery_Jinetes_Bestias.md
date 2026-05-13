# BRIEF TÉCNICO: Artillery 2D - Jinetes & Bestias
## Documento base para desarrollo en Unity | Versión 0.1 - Abierto a refinamiento

---

## 1. CONCEPTO CORE (Definido)

**Género:** Artillery 2D por turnos (spiritual successor de Gunbound/Worms)  
**Perspectiva:** 2D lateral (side-scrolling)  
**Estilo visual:** Fantasía orgánica - jinetes sobre bestias elementales  
**Plataforma inicial:** PC (Steam) → Mobile (iOS/Android)  
**Online:** 1v1 inicial, escalable a 2v2, 3v3, 4v4, 5v5  
**Modelo:** Free-to-play, monetización cosmética (sin stats P2W)

**Diferenciador:**  
- 1 personaje = 1 jinete + 1 bestia (bloqueo fijo en partida)  
- Maestría por bestia (ranking separado del ELO general)  
- Cooperativo en equipo: sinergias de terreno, combos tácticos  
- Estados de daño visuales (Intacta → Herida → Moribunda) sin números en HUD

---

## 2. MECÁNICAS FUNDAMENTALES (Definidas)

### 2.1 Loop de combate por turno
1. Jugador activo: 20 segundos para planificar
2. Fase de movimiento (opcional): mover bestia (distancia limitada por "stamina" visual)
3. Fase de apuntado: ángulo + potencia (carga orgánica en la bestia, no barra UI numérica)
4. Fase de disparo: proyectil sigue trayectoria parabólica
5. Impacto: daño por proximidad + efecto de terreno según bestia
6. Verificación de muerte → cambio de turno

### 2.2 Balística base
- **Sistema:** Simulación propia (NO Rigidbody2D puro para reproducibilidad en multiplayer)
- **Variables:** Ángulo (0-180°), Potencia (0-100%), Viento (vector 2D con magnitud y dirección)
- **Fórmula:** `posición = pos_inicial + velocidad_inicial * t + 0.5 * gravedad * t² + factor_viento * t`
- **Gravedad:** Constante global (9.8 m/s² escalada a unidades del juego)
- **Viento:** Cambia cada 2-3 turnos, visualizado con partículas ambientales
- **Colisión:** Raycast continuo contra terreno (sprite mask / tilemap destructible)

### 2.3 Terreno destructible
- **Tecnología:** Sprite Shape 2D + Shader de máscara de destrucción O Tilemap con chunks
- **Recomendación inicial:** Tilemap de 32x32px con sistema de "chunks" para destrucción circular
- **Persistencia:** El terreno modificado debe sincronizarse en multiplayer (estado autoritativo en host/server)
- **Efectos:** Cráter circular básico → expandir a formas irregulares según tipo de proyectil

### 2.4 Estados de daño (Bestia = HP)
| Estado | Visual | Efecto mecánico sutil |
|--------|--------|----------------------|
| Intacta | Brillo natural, animación fluida | 100% capacidad |
| Herida | Grietas/manjas, humo leve | Movimiento -10%, proyectil dispersión +5% |
| Moribunda | Ojos apagados, jadeo | Movimiento -25%, proyectil dispersión +15% |

**Nota:** Sin números de HP visibles. Solo segmentos de color (verde/amarillo/rojo) en HUD.

### 2.5 Muerte
- Animación conjunta: bestia cae (dirección del último impacto), jinete se desprende
- Pausa dramática 0.3s (time scale local, no global)
- Jugador pasa a modo espectador (ve cámara libre o sigue a aliados)
- **TODO:** Definir si hay revancha/resurrección según modo de juego

---

## 3. BESTIAS BASE - ROSTER INICIAL (6 personajes)

### 3.1 Estructura de datos por bestia
```
BestiaData:
  - nombre: string
  - descripcion: string
  - tipo_elemento: enum {ROCA, VIENTO, LODO, METAL, ESPINA, OSCURIDAD}

  // Movimiento
  - tipo_movimiento: enum {SALTO_PESADO, FLOTAR, ARRASTRE, DASH, RODAR, CAIDA}
  - velocidad_movimiento: float (unidades/segundo)
  - altura_salto: float
  - gravedad_personalizada: float (opcional, default = global)

  // Balística
  - angulo_minimo: float (grados)
  - angulo_maximo: float (grados)
  - potencia_maxima: float
  - peso_proyectil: float (afecta arco y velocidad de caída)
  - tipo_proyectil: enum {DIRECTO, PARABOLICO, REBOTE, DIVIDIDO, RETARDADO, ATRAVEZAR}
  - daño_base: float
  - radio_explosion: float

  // Habilidad especial (1 por bestia)
  - habilidad_nombre: string
  - habilidad_cooldown: int (turnos)
  - habilidad_efecto: string (descripción para implementación)

  // Visual
  - modelo_3d_path: string (referencia a asset)
  - animaciones: list {idle, mover, apuntar, cargar, disparar, herida, moribunda, muerte}
  - efectos_particulas: list {carga, disparo, impacto, estado_herida, estado_moribunda}
```

### 3.2 Roster inicial (conceptos definidos)

#### 1. BOULDER (Roca)
- **Jinete:** Minero enmascarado con pico
- **Movimiento:** Salto pesado, aterriza con micro-cráter (shake de cámara leve)
- **Ángulo:** 25° - 60° (estrecho, bajo)
- **Potencia:** Alta (80/100)
- **Proyectil:** Fragmento de roca propia. Arco bajo, caída rápida, daño alto
- **Habilidad:** "Temblor" - Salta y aterriza intencionalmente, daño en área + destrucción de terreno bajo él
- **Esencia:** Tanque de artillería. Directo, brutal, predecible para el enemigo

#### 2. GALE (Viento)
- **Jinete:** Mensajero con capa de plumas, máscara de ave
- **Movimiento:** Flota 1 segundo antes de caer. Viento la empuja ×1.5 (afecta más, pero puede aprovecharse)
- **Ángulo:** 15° - 75° (amplio)
- **Potencia:** Media (60/100)
- **Proyectil:** Ráfaga de 3 plumas cortantes. Daño individual bajo, total medio
- **Habilidad:** "Corriente" - Cambia dirección del viento global por 1 turno
- **Esencia:** Bailarina del viento. Fragilidad por velocidad

#### 3. MIRE (Lodo)
- **Jinete:** Chamán con máscara de barro, collares de hueso
- **Movimiento:** Se arrastra, deja rastro de lodo (terreno resbaladizo para TODOS durante 2 turnos)
- **Ángulo:** 30° - 65°
- **Potencia:** Media-Baja (50/100)
- **Proyectil:** Bola de lodo que se adhiere. Daño bajo, reduce movimiento enemigo 1 turno
- **Habilidad:** "Pantano" - Área de terreno se vuelve lodo denso (movimiento -50% en esa zona)
- **Esencia:** Controlador de campo. No mata, desangra

#### 4. SPARK (Metal)
- **Jinete:** Forjador con guanteletes de bronce, gafas protectoras
- **Movimiento:** Dash corto en línea recta (2x velocidad normal), cooldown 2 turnos
- **Ángulo:** 35° - 55° (muy estrecho)
- **Potencia:** Alta (85/100)
- **Proyectil:** Núcleo de metal caliente. Rebota 1 vez en terreno sólido, daño alto
- **Habilidad:** "Imán" - Atrae proyectiles enemigos cercanos hacia sí mismo (daño reducido, pero protege aliados)
- **Esencia:** Ingeniero de ángulos. El mapa es tu aliado

#### 5. THORN (Espina)
- **Jinete:** Cazador con armadura de espinas, capa de púas
- **Movimiento:** Rueda en bola, ignora terreno irregular, NO puede detenerse a mitad de camino
- **Ángulo:** 20° - 70°
- **Potencia:** Media (65/100)
- **Proyectil:** Espinas que se clavan en enemigo o terreno. Explotan al turno siguiente. Enemigo puede "quitarlas" gastando su turno
- **Habilidad:** "Erupción" - Espinas enterradas en terreno cercano explotan simultáneamente
- **Esencia:** Presión psicológica. El enemigo juega contra el reloj

#### 6. CHASM (Oscuridad/Vacío)
- **Jinete:** Ermitaño con farol apagado, túnica rasgada
- **Movimiento:** Teletransporte corto hacia abajo (caída controlada de 3 unidades). No puede subir fácilmente (requiere salto gastando turno completo)
- **Ángulo:** 40° - 80° (alto, difícil de usar en terreno plano)
- **Potencia:** Muy Alta (95/100)
- **Proyectil:** Rayo de luz sólida del farol. Atraviesa terreno (no destruye), daña SOLO en impacto directo
- **Habilidad:** "Abismo" - Crea hueco circular en terreno bajo enemigo (caída forzada, daño por gravedad)
- **Esencia:** Sniper del subsuelo. Posicionamiento imposible

---

## 4. PIPELINE DE ARTE (Recomendaciones técnicas)

### 4.1 Modelado 3D vs 2D
**Decisión recomendada:** 3D modelado en Blender, renderizado en Unity con shaders "2.5D"  
**Justificación:**
- Permite rotación de bestias al apuntar (ángulo de tiro visible sin sprites rotados manualmente)
- Animaciones de muerte/daño más dinámicas (bestia que cae, jinete que se desprende)
- Reutilización de rigs para variantes de bestias
- Exportación a móvil optimizable (LOD, batching)

**Alternativa descartada por ahora:** Sprite 2D tradicional. Requiere demasiados frames para animaciones de balística y destrucción.

### 4.2 Blender → Unity Workflow
```
Blender:
  1. Modelar bestia en T-pose (o pose idle base)
  2. Rigging: Esqueleto con huesos principales {cuerpo, cabeza, boca, apendice_tiro, patas/x alas}
  3. Animaciones clave en Blender: idle, mover, apuntar, cargar, disparar, herida, moribunda, muerte
  4. Jinete: Modelo separado, rig separado, pero emparentado a hueso "montura" de la bestia
  5. Exportar como FBX (uno por bestia, animaciones embebidas o separadas)

Unity:
  1. Importar FBX, configurar Avatar (Humanoid para jinete, Generic para bestia)
  2. Animator Controller: Blend trees por estado {Idle, Mover, Combate, Daño}
  3. Shader Graph: Toon shader 2.5D (cel-shading + outline) o PBR simplificado
  4. VFX Graph/Particle System: Efectos de elemento (roca, viento, lodo, fuego)
  5. Sorting Layers: Bestias en "Characters", jinetes en "Riders", proyectiles en "Projectiles"
```

### 4.3 Especificaciones técnicas de modelo
| Elemento | Polígonos | Texturas | Notas |
|----------|-----------|----------|-------|
| Bestia base | 3,000 - 5,000 tris | 1x 1024x1024 (diffuse + normal + emission) | Estilo stylized/low-poly |
| Jinete | 1,500 - 2,500 tris | 1x 512x512 | Detalle en máscara/capa |
| Proyectil | 200 - 500 tris | 1x 256x256 | Con trail de partículas |
| Terreno tile | 4 tris (plano) | Tileable 512x512 | Shader de destrucción por máscara |

### 4.4 Animaciones requeridas por bestia (8 base)
1. **Idle:** Respiración, flotación, pulso elemental
2. **Mover:** Salto, arrastre, dash, rodar (loop)
3. **Apuntar:** Cuerpo se inclina, cabeza gira, jinete se aferra
4. **Cargar:** Brillo interno creciente, partículas de carga (loop hasta disparo)
5. **Disparar:** Recoil, expulsión de proyectil, flash de elemento
6. **Herida:** Reacción al impacto, grietas/efectos de daño aparecen
7. **Moribunda:** Movimiento lento, jadeo, ojos apagados
8. **Muerte:** Caída, jinete se desprende, desvanecimiento en partículas de elemento

**TODO:** Definir si las transiciones entre estados son blend trees o cambios abruptos (más barato técnicamente).

### 4.5 VFX - Sistema de partículas
| Efecto | Tecnología | Notas |
|--------|-----------|-------|
| Carga de disparo | Particle System 2D/3D | Color según elemento, intensidad ligada a potencia |
| Trail de proyectil | Trail Renderer + partículas | Rastro persistente 0.5s para leer trayectoria |
| Impacto/explosión | Particle System burst | Forma circular básica → expandir a elemental (rocas volando, plumas, lodo) |
| Destrucción terreno | Shader de máscara + partículas de escombros | Eficiencia crítica: no instanciar demasiados prefabs |
| Estado herida | Particle System loop leve | Humo, chispas, fluidos que gotean |
| Estado moribunda | Particle System + post-proceso local | Pulso lento, color desaturado |

---

## 5. ARQUITECTURA UNITY (Estructura base)

### 5.1 Sistemas principales (Scriptable Objects + MonoBehaviours)
```
Assets/
  _Project/
    Data/
      Bestias/           <-- ScriptableObjects con stats de cada bestia
      Proyectiles/       <-- Prefabs + datos de balística
      Terrenos/          <-- Configuraciones de mapa
    Scripts/
      Core/
        GameManager.cs       -- Estado global de partida (turnos, rondas, victoria)
        TurnManager.cs       -- Control de secuencia de turnos
        InputManager.cs      -- Abstracción de input (PC teclado/ratón, mobile touch)
      Combat/
        BallisticsSystem.cs  -- Cálculo de trayectoria, colisión, daño
        WindSystem.cs        -- Vector de viento global, cambios por turno
        TerrainSystem.cs     -- Destrucción de terreno, sincronización
      Entities/
        BeastController.cs   -- Movimiento, estado de daño, animaciones
        RiderController.cs   -- Animaciones del jinete (emparentado)
        Projectile.cs        -- Movimiento del proyectil, detección de impacto
      UI/
        HUDManager.cs        -- HP visual, ángulo/potencia (sin números), viento
        AimingUI.cs          -- Indicador de ángulo, carga de potencia
      Multiplayer/
        NetworkManager.cs    -- Photon PUN / Netcode for GameObjects
        SyncManager.cs       -- Sincronización de terreno, estados, turnos
      AI/
        MinimaxAI.cs         -- TODO: Implementar IA para single-player y bots
    Prefabs/
      Bestias/           <-- Prefabs de cada bestia con Animator, colliders, VFX
      Jinetes/           <-- Prefabs emparentables
      Proyectiles/       <-- Prefabs con Rigidbody (controlado por script) + collider
      Terrenos/          <-- Prefabs de mapas con Tilemap/CompositeCollider2D
    Scenes/
      Boot.unity         -- Inicialización, carga de datos
      MainMenu.unity     -- Menú, selección de bestia, matchmaking básico
      Game.unity         -- Arena de combate (carga dinámica de terreno)
```

### 5.2 Física: Decisión crítica
**NO usar Rigidbody2D para proyectiles en multiplayer.**  
**SÍ usar simulación determinista propia.**

```csharp
// Pseudocódigo - BallisticsSystem.cs
public class BallisticsSystem : MonoBehaviour
{
    // Simulación local (cliente) y validación server/host
    public static Vector2 CalculatePosition(Vector2 start, Vector2 velocity, float time, WindData wind)
    {
        Vector2 gravity = new Vector2(0, -9.8f * gravityScale);
        Vector2 windForce = wind.vector * wind.affectionMultiplier;

        return start + velocity * time + 0.5f * (gravity + windForce) * time * time;
    }

    // Raycast continuo contra terreno para detectar impacto antes de frame
    public static bool CheckCollision(Vector2 from, Vector2 to, out RaycastHit2D hit)
    {
        hit = Physics2D.Raycast(from, to - from, Vector2.Distance(from, to), terrainLayer);
        return hit.collider != null;
    }
}
```

**Razón:** Rigidbody es no-determinista por frame rate, orden de ejecución, y floating point. Para multiplayer y replay, necesitas reproducir exactamente la misma trayectoria en todos los clientes.

**TODO:** Definir si la simulación corre en:
- Cliente autoritativo (host calcula, clientes reproducen) - más simple, riesgo de cheat
- Server autoritativo (servidor calcula, manda resultado) - más seguro, requiere backend

### 5.3 Terreno destructible - Opciones técnicas

**Opción A: Tilemap con chunks (Recomendada para MVP)**
- Tilemap de Unity con tiles individuales
- Destrucción: cambiar tile a "vacío" en radio circular
- Pros: Simple, eficiente, nativo de Unity
- Cons: Destrucción es cuadrada/pixelada, no orgánica

**Opción B: Sprite Shape + Shader de máscara**
- Sprite Shape define contorno del terreno
- Shader recibe textura de máscara (blanco = terreno, negro = vacío)
- Destrucción: dibujar círculo negro en textura de máscara
- Pros: Destrucción orgánica, visualmente superior
- Cons: Más complejo, sincronización de textura en multiplayer pesada

**Opción C: Mesh dinámico + boolean operations**
- Mesh 2D del terreno, modificado en runtime
- Pros: Máxima flexibilidad
- Cons: Complejidad alta, performance en móvil dudosa

**Decisión inicial:** Opción A (Tilemap) para prototipo. Migrar a B si el juego lo justifica visualmente.

### 5.4 Multiplayer - Photon PUN 2 (Free tier)

**Plan gratuito:** 20 CCU (Concurrent Users), suficiente para:
- Desarrollo y pruebas internas
- Alpha cerrada (10-15 testers, rara vez más de 5 partidas simultáneas)
- Demo para inversores (partidas 1v1 programadas)

**Arquitectura propuesta:**
```
Host/Cliente (Photon PUN):
  - Host: Crea sala, genera terreno, sincroniza destrucción
  - Cliente: Recibe estado inicial, reproduce inputs del host
  - Turnos: RPC (Remote Procedure Call) para "IniciaTurno", "FinalizaTurno"
  - Balística: Host calcula trayectoria, manda resultado (impacto en coordenada X,Y, daño Z)
  - Terreno: Host manda "DestruyeTile(x,y,radio)" a todos los clientes
```

**TODO:** Evaluar migración a Photon Fusion (más caro, mejor para competivo) o Mirror + VPS propio si el juego crece.

---

## 6. IA - MINIMAX (Para single-player y bots)

### 6.1 Requerimiento
Implementar IA basada en Minimax para:
- Modo campaña/práctica (jugador vs. IA)
- Bots que reemplacen jugadores desconectados
- Tutorial interactivo

### 6.2 Consideraciones para artillery
Minimax tradicional asume información perfecta y turnos discretos. En artillery:
- **Espacio de estados enorme:** Posiciones continuas, ángulos continuos, viento variable
- **Aleatoridad:** Dispersión de proyectil, viento cambiante
- **Tiempo de cálculo:** Debe decidir en <1 segundo

### 6.3 Simplificaciones necesarias (para hacerlo viable)
1. **Discretizar acciones:** Ángulo en pasos de 5°, potencia en pasos de 10%
2. **Heurística de evaluación:** No simular hasta el final, evaluar estado después de 1 turno
3. **Profundidad limitada:** Minimax a 2-3 niveles (mi turno + respuesta del enemigo)
4. **Simulación rápida:** Usar la misma `BallisticsSystem` pero con pasos de tiempo más grandes

### 6.4 Función de evaluación heurística (propuesta)
```csharp
float EvaluateState(GameState state, int playerId)
{
    float score = 0;

    // HP propia vs. enemigo (peso alto)
    score += (myHP - enemyHP) * 100;

    // Distancia al enemigo (preferir distancia óptima según bestia)
    float optimalDistance = GetOptimalDistance(myBeast);
    score -= Mathf.Abs(distanceToEnemy - optimalDistance) * 10;

    // Posición ventajosa (altura, cobertura)
    score += HeightAdvantage(myPosition) * 20;
    score += CoverAvailability(myPosition) * 15;

    // Estado de bestia (intacta > herida > moribunda)
    score += GetStateValue(myBeastState) * 50;

    // Viento favorable para mi tipo de bestia
    score += WindAdvantage(myBeast, currentWind) * 25;

    return score;
}
```

**TODO:** Refinar heurísticas por bestia (Gale valora viento positivo, Boulder ignora viento, etc.)

### 6.5 Implementación sugerida
```
Scripts/AI/
  - MinimaxAI.cs           -- Algoritmo principal con poda alpha-beta
  - ActionGenerator.cs     -- Genera acciones discretas posibles {angulo, potencia, movimiento}
  - StateSimulator.cs      -- Simula resultado de una acción (sin ejecutar en juego real)
  - HeuristicEvaluator.cs -- Funciones de evaluación por tipo de bestia
  - AIDifficulty.cs        -- Ajusta profundidad de Minimax y aleatoridad de errores
```

---

## 7. UI/UX - PRINCIPIOS (Sin números)

### 7.1 HUD en partida
| Elemento | Representación |
|----------|---------------|
| HP (bestia) | Silueta vertical con 3 segmentos de color (verde/amarillo/rojo) |
| Ángulo | Línea dinámica desde la bestia, longitud fija, dirección del cursor |
| Potencia | Brillo interno de la bestia + pulso de partículas (más intenso = más potencia) |
| Viento | Partículas ambientales + veleta simple en esquina (dirección + intensidad visual, no numérica) |
| Turno | Bestia del jugador activo con borde brillante, nombre sutil |
| Tiempo | Círculo que se cierra alrededor de la bestia (20s → 0s) |

### 7.2 Menús
- **Selección de bestia:** Bestia grande en centro, rotación 360° al pasar mouse, stats como iconos (no números)
- **Perfil de jugador:** Título de maestría, bestia más jugada, insignias visuales
- **Resultados:** Animación de bestias, no tabla de números

---

## 8. MONETIZACIÓN (Cosmética pura)

### 8.1 Lo que se vende
- Skins de jinete (no afecta hitbox)
- Variantes de color de bestia (mismo modelo, palette swap)
- Efectos de proyectil (trail distintivo, impacto único)
- Animaciones de victoria/derrota
- Insignias y títulos de maestría (desbloqueables por juego, no comprables)

### 8.2 Lo que NO se vende (nunca)
- Stats de bestia
- Ventaja de ángulo/potencia
- Items de combate
- "Revives" o segundas oportunidades

---

## 9. ROADMAP TÉCNICO (Sugerido)

| Fase | Duración | Entregable | Tecnología |
|------|----------|-----------|------------|
| **Proto 1** | 2-3 semanas | Balística local, 1 bestia, 1 mapa, hotseat | Unity physics sim, Tilemap básico |
| **Proto 2** | 2-3 semanas | 2 bestias, terreno destructible, HUD sin números | Animator, Particle System |
| **Alpha** | 4-6 semanas | Online 1v1 (Photon free), 4 bestias, matchmaking por código | Photon PUN, RPC sync |
| **Beta** | 6-8 semanas | 6 bestias, 2v2, ranked básico, maestría | Backend simple (Firebase/PlayFab) |
| **Launch** | +2 meses | Polish, trailers, marketing | --- |

---

## 10. ÁREAS PENDIENTES (TODO para próxima iteración)

### Diseño de juego
- [ ] Modos de juego detallados (1v1 ranked, 2v2 casual, free-for-all, etc.)
- [ ] Sistema de revancha/resurrección según modo
- [ ] Mapas: temáticas, tamaños, elementos ambientales
- [ ] Progresión de maestría: niveles exactos, recompensas
- [ ] Tutorial interactivo con IA Minimax

### Técnico
- [ ] Decisión final: Cliente autoritativo vs. Server autoritativo
- [ ] Sincronización de terreno: ¿Tilemap states o deltas?
- [ ] Anti-cheat básico: validación de inputs en host
- [ ] Replay system: grabar y reproducir partidas (necesario para ranked)
- [ ] Optimización móvil: LODs, batching, reducción de overdraw

### Arte
- [ ] Concept art de cada bestia (2D sketches antes de modelar en Blender)
- [ ] Palette de colores del juego (estilo visual coherente)
- [ ] Animación de jinete: ¿Procedural (Inverse Kinematics) o animado a mano?
- [ ] VFX de elementos: ¿Shader Graph o Particle System tradicional?

### Audio
- [ ] Directorio de sonidos necesarios (impactos, cargas, ambientes)
- [ ] Música: 1 track gameplay + 1 track menú (presupuesto $150-$300)

### Negocio
- [ ] Pitch deck para inversores (basado en demo jugable)
- [ ] Plan de monetización detallado
- [ ] Estrategia de comunidad (Discord, beta cerrada)

---

## 11. RECURSOS Y REFERENCIAS

### Unity
- Netcode for GameObjects (alternativa a Photon): https://docs.unity3d.com/Packages/com.unity.netcode.gameobjects@1.0/manual/index.html
- 2D Tilemap Extras (destrucción eficiente): Asset Store oficial
- Shader Graph: Built-in RP o URP (URP recomendado para multiplataforma)

### Photon
- PUN 2 Free: https://www.photonengine.com/pun
- Documentación RPC y sincronización: https://doc.photonengine.com/game-guides/pun-2

### Blender
- Rigify (addon oficial para rigging rápido)
- Exportación FBX a Unity: Apply transforms, Forward = -Z, Up = Y

### Referencias de juego
- Gunbound (2002): Mecánicas de balística, sistema de turnos
- Worms W.M.D (2016): Destrucción de terreno, arsenal variado
- ShellShock Live (2015): Artillery puro moderno, online funcional
- Dofus (2004): Turnos por equipo, roles tácticos en 2D

---

## 12. NOTAS FINALES PARA EL SIGUIENTE MODELO DE IA

Este documento es un **punto de partida**, no una especificación cerrada. El siguiente modelo de IA (especializado en coding/architectura) debe:

1. **Tomar decisiones técnicas** donde este documento ofrece opciones (ej: Tilemap vs. Sprite Shape para terreno)
2. **Generar código base** para los sistemas marcados (BallisticsSystem, TurnManager, MinimaxAI)
3. **Definir estructuras de datos exactas** (ScriptableObjects, structs, enums)
4. **Crear un proyecto Unity funcional** con escena de prueba de balística
5. **Implementar IA Minimax** con la heurística propuesta o mejorada

**Preguntas clave para el siguiente modelo:**
- ¿Rigidbody2D controlado por script o simulación propia completa?
- ¿Cómo sincronizar destrucción de Tilemap en Photon PUN sin lag?
- ¿Estructura de datos óptima para el árbol de Minimax en artillery?
- ¿Cómo hacer el aiming visual (línea de predicción de trayectoria) sin revelar demasiado al jugador?

---

*Documento generado como base abierta para refinamiento técnico y de diseño.*
*Versión 0.1 | Enfoque: Unity 2022.3 LTS, 2D/2.5D, multiplayer por turnos, IA Minimax*
