# MILESTONE-02: Stable Multiplayer Foundation

## Goal
Prepare architecture for networked play without implementing full Photon integration.

## Required Tasks
- TASK-04: ImpactData struct
- TASK-05: Ballistics determinism test
- TASK-10: AI depth budget guard
- TASK-11: Wind as seeded value

## Playable Outcome
No new gameplay features. Infrastructure only:
- ImpactData contract defined for all subscribers
- Ballistics determinism validated
- AI performance bounded
- Wind deterministic for future network sync

## Technical Risks
1. **ImpactData migration** - Changing delegate signatures breaks subscribers. Must update carefully.
2. **Determinism test may reveal issues** - Could require BallisticsSystem refactor.

## Validation Criteria
- [ ] ImpactData used by all impact handlers
- [ ] Determinism test passes (1000 iterations, 4 decimal precision)
- [ ] AI never exceeds 100ms think time
- [ ] Wind regenerates identically from same seed
- [ ] No regressions in local gameplay (re-run MILESTONE-01 validation)

## Est. Complexity
2-3 hours total

## Dependencies
Requires MILESTONE-01 complete