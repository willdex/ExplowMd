# TASK-11: Wind as Seeded Value

## ID
TASK-11

## Goal
Make WindSystem deterministic by broadcasting seed instead of float values.

## Problem
WindData uses float magnitude/direction. If broadcast as float, slight precision differences can cause trajectory divergence between clients.

## Requirements
- WindSystem uses int seed to initialize Random
- Only seed is broadcast (int32)
- Client recreates wind from seed
- Validate: two instances with same seed produce identical wind

## Constraints
- Do NOT change WindData public interface
- Seed must be deterministic per turn
- Wind changes every 2-3 turns per spec

## Dependencies
TASK-05 (determinism test harness ready)

## Validation Checklist
- [ ] WindSystem generates from int seed
- [ ] Seed only is serialized for network
- [ ] Identical seeds produce identical wind
- [ ] Test harness validates wind determinism

## Expected Result
Wind is bit-exact across all clients.

## Priority
P3 - Multiplayer foundation

## Estimated Complexity
25 minutes