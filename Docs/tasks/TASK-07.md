# TASK-07: AimingController Input Loop

## ID
TASK-07

## Goal
Implement human player input for aiming angle and power charge.

## Problem
No way for human to aim or fire. AimingController exists but isn't wired to input or TurnManager.

## Requirements
- Read angle: mouse X position relative to beast, or left stick
- Read power: hold space/mouse button to charge 0→100%
- Clamp angle to BeastData.angleRange
- Clamp power to 0-100%
- On release: submit ShotInput to TurnManager

## Constraints
- Do NOT modify TurnManager phase logic
- Do NOT handle AI input (separate flow)
- Only active during human player's AIM phase

## Dependencies
- TASK-01 (projectile visible)
- TASK-06 (ShotInput expanded)
- TASK-08 (AimingIndicator visual)

## Validation Checklist
- [ ] Angle changes as mouse moves
- [ ] Power charges while holding button
- [ ] Releasing button fires shot
- [ ] Angle clamped to beast's allowed range
- [ ] HUD shows current angle/power

## Expected Result
Human can aim and fire using keyboard/mouse.

## Priority
P2 - First human-playable loop

## Estimated Complexity
45 minutes