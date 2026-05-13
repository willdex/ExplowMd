# MILESTONE-01: First Playable Local Match

## Goal
Deliver a complete, testable local gameplay loop with two beasts, human vs AI.

## Required Tasks
- TASK-01: ProjectileController.prefab
- TASK-02: AI turn execution
- TASK-03: QuickSetup duplicates fix
- TASK-06: ShotInput expansion (for AI replay)
- TASK-07: AimingController input
- TASK-08: AimingIndicator trajectory
- TASK-09: Terrain destruction

## Playable Outcome
1. Two beasts placed on generated terrain (left and right sides)
2. Human player can aim, charge power, and fire visible projectile
3. Projectile follows parabolic arc with wind deflection
4. Terrain deforms on impact (circular hole)
5. Damage applied, HP bar updates
6. AI takes turn automatically within 1 second
7. Turn switches between players until one beast reaches 0 HP
8. Game over screen displays winner

## Technical Risks
1. **Terrain bounds bug** - FillTerrainTiles creates terrain on one side only. Must fix before validating destruction.
2. **Ballistics determinism** - Not yet tested, but not required for local MP.
3. **AI depth explosion** - No budget guard yet, could hitch.

## Validation Criteria
- [ ] Game starts with 2 beasts
- [ ] Player 0 (human) can aim and fire
- [ ] Projectile visible, follows correct arc
- [ ] Impact creates terrain hole
- [ ] Damage reduces HP
- [ ] AI responds with shot
- [ ] Turn switches correctly
- [ ] Game ends when HP = 0
- [ ] No errors in console

## Est. Complexity
4-6 hours total (TASK-01 through TASK-09)