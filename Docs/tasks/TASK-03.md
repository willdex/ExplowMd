# TASK-03: Fix QuickSetup Duplicates

## ID
TASK-03

## Goal
Prevent QuickSetup from creating duplicate GameBootstrap and PlaygroundSetup instances on repeated Play/Stop cycles.

## Problem
Each Play without Stop creates new instances of GameBootstrap and PlaygroundSetup, corrupting scene state and causing multiple beasts/projectiles.

## Requirements
- QuickSetup.Awake() must check FindFirstObjectByType<T>() before creating
- If instance already exists, do not create new one
- Detected duplicate should log warning

## Constraints
- Do NOT change QuickSetup responsibility (auto-setup on empty scene)
- Do NOT modify GameBootstrap or PlaygroundSetup

## Dependencies
None (standalone task)

## Validation Checklist
- [ ] 3x Play/Stop cycles produces exactly 1 GameBootstrap
- [ ] 3x Play/Stop cycles produces exactly 1 PlaygroundSetup
- [ ] Exactly 2 beasts exist in scene after setup
- [ ] No console errors about duplicate components

## Expected Result
Clean state on every Play, no accumulated duplicates.

## Priority
P0 - Critical Blocker

## Estimated Complexity
10 minutes