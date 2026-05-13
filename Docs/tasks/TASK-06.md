# TASK-06: Expand ShotInput Struct

## ID
TASK-06

## Goal
Add missing identification fields to ShotInput for multiplayer replay and AI evaluation.

## Problem
ShotInput only has angle, power, useSpecialAbility. Missing playerID, turnSequenceNumber, beastID needed for host-side validation and replay.

## Requirements
```csharp
public struct ShotInput {
    public int playerId;
    public int turnSequenceNumber;  // for replay ordering
    public int beastId;
    public float angle;
    public float power;
    public bool useSpecialAbility;
    public long timestamp;
}
```

## Constraints
- Do NOT break existing ShotInput usage
- Add new fields only, don't remove existing
- timestamp already exists (keep it)

## Dependencies
TASK-04 (ImpactData struct definition context)

## Validation Checklist
- [ ] TurnManager reads all new fields on shot submission
- [ ] AIPlayer generates ShotInput with playerId/beastId
- [ ] GameBootstrap logs new fields on shot fire

## Expected Result
ShotInput fully specifies which player, which beast, which turn.

## Priority
P2 - Required for AI replay + multiplayer

## Estimated Complexity
15 minutes