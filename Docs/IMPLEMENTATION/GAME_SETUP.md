# ESCENA GAME - SETUP MANUAL

## Pasos para crear la escena jugable:

### 1. Crear GameObjects necesarios

En Unity, crea estos GameObjects vacíos:

```
GameObject "Managers" ( empty )
├── "TurnManager" (AddComponent: TurnManager)
├── "WindSystem" (AddComponent: WindSystem)
└── "HUDController" (AddComponent: HUDController)

GameObject "PlaygroundSetup" (AddComponent: PlaygroundSetup)
GameObject "GameBootstrap" (AddComponent: GameBootstrap)
```

### 2. Configurar Tags

Para cada bestia que crea PlaygroundSetup, necesitas añadir Tags:

1. En Unity: Edit > Project Settings > Tags and Layers
2. En Layers tab, añadir "Beast" en User Layer 8 o 9
3. En Tags tab, añadir Tag "Player" y "Beast"

### 3. Asignar referencias en PlaygroundSetup (opcional)

Si quieres controlar las posiciones desde el Inspector:
- En PlaygroundSetup, arrastra los GameObjects a los campos públicos

### 4. Play!

Presiona Play y deberías ver:
- 2 bestias (cápsulas roja y azul)
- Terreno café debajo
- Turnos alternando entre jugador e IA
- IA dispara automáticamente después de 0.5 segundos

---

## LOG ESPERADO EN CONSOLA

```
[PlaygroundSetup] Created terrain
[PlaygroundSetup] Created beast Player0 at (-5, 2)
[PlaygroundSetup] Created beast Player1 at (5, 2)
[PlaygroundSetup] Systems initialized - GAME READY
[TurnManager] Player 0 turn started. Time: 20s. Phase: AIM
```

Cuando la IA dispara:
```
[GameBootstrap] Turn started - Player 1
[GameBootstrap] Player controls DISABLED (AI turn)
[GameBootstrap] AI decision: 45.0° / 0.50
[TurnManager] Shot submitted - Player:1 Angle:45.0, Power:0.50
[GameBootstrap] Shot fired: 45.0° / 0.50
[Projectile] Hit Terrain at (x, y)
[GameBootstrap] Projectile hit at (x, y)
[TurnManager] Impact detected, processing...
[TurnManager] Player 0 turn started. Time: 20s. Phase: AIM
```

---

## CONTROLES (próximamente)

- Mouse: Apuntar (futuro)
- Click: Disparar con potencia actual (futuro)
- A/D: Mover bestia (futuro)

---

## TROUBLESHOOTING

### Error: "BeastData not found"
- Normal por ahora, usa datos hardcodeados en PlaygroundSetup

### Error: "TurnManager not found"
- Asegúrate que el GameObject "TurnManager" existe y tiene el componente

### Bestias no aparecen
- PlaygroundSetup corre en Start(), asegúrate que está en un GameObject activo

### IA no dispara
- Revisa que GameBootstrap esté en un GameObject activo
- Revisa la consola por errores de la IA