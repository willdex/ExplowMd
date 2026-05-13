# TASK-08: AimingIndicator Trajectory Preview

## ID
TASK-08

## Goal
Show visual arc predicting projectile path during AIM phase.

## Problem
Player cannot verify aim before firing. No visual feedback on whether angle/power will hit target.

## Requirements
- Sample BallisticsSystem.CalculatePosition at 20 steps
- Render as LineRenderer or GL.Lines
- Color gradient: green (start) → red (predicted impact)
- Update every frame during AIM phase
- Hide when not in AIM phase

## Constraints
- Do NOT modify BallisticsSystem
- Must handle wind deflection in preview
- Should stop at terrain collision (raycast check)

## Dependencies
- TASK-01 (projectile prefab)
- TASK-07 (AimingController input)

## Validation Checklist
- [ ] Line appears during AIM phase
- [ ] Line follows parabolic arc matching BallisticsSystem
- [ ] Line color indicates predicted impact zone
- [ ] Line disappears when turn ends

## Expected Result
Player sees predicted trajectory before firing.

## Priority
P2 - Playability systems

## Estimated Complexity
30 minutes