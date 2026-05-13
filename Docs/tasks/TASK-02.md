# TASK-02: Fix AI Turn Execution

## ID
TASK-02

## Goal
Ensure AIPlayer.Think() is called when AI's turn starts and produces a valid shot.

## Problem
OnTurnStarted event fires but AIPlayer.Think() subscription is unconfirmed. Non-human turns may silently stall, locking the game loop.

## Requirements
- Verify AIPlayer subscribes to TurnManager.OnTurnStarted
- Add debug log: "AI turn started for Player {playerId}"
- Confirm AIDelayThenThink coroutine calls aiPlayer.Think(gameState)

## Constraints
- Do NOT modify TurnManager event signature
- Do NOT change AI algorithm (MinimaxAI)

## Dependencies
- TASK-01 (projectile prefab needed to visualize AI shot)

## Validation Checklist
- [ ] AIPlayer subscription confirmed in GameBootstrap.Initialize()
- [ ] Log appears when Player 1 turn starts
- [ ] AI decision logged (angle/power)
- [ ] AI shot fires visible projectile
- [ ] Projectile follows ballistic trajectory

## Expected Result
AI takes its turn automatically within 1 second of turn start, fires a shot that hits terrain or enemy.

## Priority
P0 - Critical Blocker

## Estimated Complexity
20 minutes (debug + confirm)