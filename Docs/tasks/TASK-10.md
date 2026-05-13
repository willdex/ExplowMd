# TASK-10: AI Depth Budget Guard

## ID
TASK-10

## Goal
Prevent MinimaxAI from consuming excessive CPU time with unbounded search depth.

## Problem
MinimaxAI search space can explode with 6 beast types, variable wind, 6 projectile types. Can cause 1+ second hitches on AI turn.

## Requirements
- Add to Constants.cs: AI_MAX_DEPTH = 4, AI_MAX_THINK_MS = 100
- Wrap MinimaxAI search in Stopwatch
- Return best move found when budget exceeded
- Log warning when timeout triggers

## Constraints
- Do NOT change AI algorithm (Minimax)
- Do NOT change heuristic evaluation
- Must not break existing AI difficulty levels

## Dependencies
None (standalone safety task)

## Validation Checklist
- [ ] Constants.cs has new AI budget values
- [ ] AI returns within 100ms on timeout
- [ ] Log entry appears when budget exceeded
- [ ] AI quality remains acceptable (not broken by early exit)

## Expected Result
AI never causes visible hitches, degrades gracefully.

## Priority
P3 - Stability and AI

## Estimated Complexity
20 minutes