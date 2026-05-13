# TASK-12: HUD Integration

## ID
TASK-12

## Goal
Connect UI components to game systems for visible feedback.

## Problem
HUDController exists but isn't wired to game events. No visible HP, turn timer, or wind indicator.

## Requirements
- HealthBarVisual subscribes to BeastController.OnStateChanged
- TurnTimerUI subscribes to TurnManager phase events
- WindIndicatorUI subscribes to WindSystem change event

## Constraints
- Do NOT change game logic
- Do NOT add new UI elements
- Use existing UI prefabs and components

## Dependencies
- TASK-07 (AimingController for turn flow)
- TASK-09 (terrain destruction for impact feedback)

## Validation Checklist
- [ ] HP bar updates when damage applied
- [ ] Turn timer counts down during AIM phase
- [ ] Wind direction/intensity visible
- [ ] Game over screen appears when beast HP = 0

## Expected Result
Player sees all relevant game state through UI.

## Priority
P3 - QoL, not loop-blocking

## Estimated Complexity
40 minutes