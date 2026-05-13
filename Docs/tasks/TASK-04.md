# TASK-04: Define ImpactData Struct

## ID
TASK-04

## Goal
Create a typed payload structure for projectile impact events to replace loose delegate signatures.

## Problem
OnImpact delegate has no defined contract. Subscribers (TerrainManager, DamageCalculator) don't know what data to expect. Retrofitting later will break multiple subscribers.

## Requirements
```csharp
public struct ImpactData {
    public Vector2 position;
    public float radius;          // in world units
    public ProjectileType type;    // DIRECT, PARABOLIC, BOUNCE, SPLIT, etc.
    public int rawDamage;          // before falloff calculation
    public int shooterId;          // who fired this shot
}
```

## Constraints
- Do NOT change OnImpact delegate signature in existing subscribers yet
- Add to Explow.Core namespace with GameState structs
- Binary compatible with current OnImpact(ProjectileController, Vector2, ExplosionResult)

## Dependencies
None (standalone definition task)

## Validation Checklist
- [ ] ImpactData struct defined in correct namespace
- [ ] All required fields present
- [ ] ShotInput includes playerId (matches existing TASK ordering)

## Expected Result
ImpactData exists and can be used to wire subsystems.

## Priority
P1 - Impact contract unblocks terrain + damage wiring

## Estimated Complexity
10 minutes