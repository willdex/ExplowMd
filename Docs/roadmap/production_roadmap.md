# Production Roadmap

## Priority Order
```
P0: Critical blockers (nothing runs without these)
P1: Impact contract + determinism (unblocks multiplayer)
P2: Core gameplay loop (human can play)
P3: Stability + AI
P4: Multiplayer infrastructure
P5: Polish
```

## Implementation Sequence

### Phase 0: Fix Critical Blockers (1-2 hours)
```
TASK-01 → TASK-02 → TASK-03
```
Create projectile prefab, fix AI turn, fix duplicates.
**After Phase 0:** Basic scene runs without crashes.

### Phase 1: First Playable (4-6 hours)
```
TASK-06 → TASK-07 → TASK-08 → TASK-09
```
Expand ShotInput, wire input, add trajectory preview, terrain destruction.
**After Phase 1:** MILESTONE-01 achievable - full local game loop.

### Phase 2: Multiplayer Foundation (2-3 hours)
```
TASK-04 → TASK-05 → TASK-10 → TASK-11
```
ImpactData, determinism test, AI budget, seeded wind.
**After Phase 2:** MILESTONE-02 achievable - stable multiplayer prep.

### Phase 3: HUD Polish (1-2 hours)
```
TASK-12
```
Wire UI to game events.
**After Phase 3:** Full UI feedback, feels like complete game.

### Phase 4: Multiplayer (Deferred)
- Photon PUN room creation
- Network sync for terrain
- Host authoritative ballistics
- Input sequence numbers for replay

### Phase 5: Art Pipeline (Deferred)
- 3D beast models from Blender
- Animator controllers
- VFX particle systems
- Sound effects

## Dependency Graph
```
[TASK-01]                    [TASK-03]
    ↓                            ↓
[TASK-02] → [TASK-06] → [TASK-07] → [TASK-08] → [TASK-09]
    │             │                │
    │             └────────────────┘
    │                     │
[TASK-04] → [TASK-05] → [TASK-10] → [TASK-11]
    │                           │
    └───────────────────────────┘
                    ↓
              [TASK-12]

All tasks feed into MILESTONE-01 or MILESTONE-02
```

## Key Principles
1. **Local gameplay first** - Don't touch multiplayer until local loop is perfect.
2. **Determinism before Photon** - Ballistics must be provably consistent.
3. **No architecture changes** - Current namespace/folder structure is fine.
4. **Small atomic tasks** - Each task is isolated, testable, reversible.
5. **Indie-friendly** - No enterprise patterns, no overengineering.

## Known Blockers at Start
1. TASK-01 (projectile prefab) - Create manually in Unity Editor
2. Terrain bounds bug - Fix in PlaygroundSetup.FillTerrainTiles
3. QuickSetup duplicate creation - Already identified in TASK-03

## Estimated Total Time
- Phase 0: 1-2 hours
- Phase 1: 4-6 hours
- Phase 2: 2-3 hours
- Phase 3: 1-2 hours
- **Total to MILESTONE-02: 8-13 hours**

## Next Action
Start with TASK-01: Create ProjectileController.prefab manually in Unity Editor.

---
*Generated from Opus technical review*
*Do NOT implement until documentation is reviewed*