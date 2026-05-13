# TASK-09: Wire Terrain Destruction on Impact

## ID
TASK-09

## Goal
Connect projectile impact to TerrainManager tile destruction.

## Problem
TerrainManager.DestroyTilesAt exists but is not subscribed to projectile impact events. Explosions don't deform terrain.

## Requirements
- Subscribe TerrainManager to ImpactData event
- Change radius parameter from float to int (tile count)
- Call DestroyTilesAt with impact position and EXPLOSION_RADIUS_TILES
- Validate circular destruction pattern

## Constraints
- Do NOT change TerrainManager internal logic
- Do NOT modify BallisticsSystem collision detection
- Use existing EXPLOSION_RADIUS_TILES constant (3f → 3 tiles)

## Dependencies
- TASK-04 (ImpactData struct)
- TASK-05 (determinism validated)

## Validation Checklist
- [ ] Impact creates circular gap in tilemap
- [ ] Gap radius matches EXPLOSION_RADIUS_TILES
- [ ] Multiple impacts accumulate destruction
- [ ] No desync between client instances (MP future)

## Expected Result
Terrain deforms where projectiles land.

## Priority
P2 - Core gameplay loop

## Estimated Complexity
30 minutes