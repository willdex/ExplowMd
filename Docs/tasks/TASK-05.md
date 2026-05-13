# TASK-05: Ballistics Determinism Test Harness

## ID
TASK-05

## Goal
Create an automated test that validates BallisticsSystem produces identical results across multiple runs with same inputs.

## Problem
Multiplayer determinism is the "existential risk" per architecture review. No test harness exists to validate float math consistency between host and client.

## Requirements
- Create Unity Editor test (NUnit)
- Run CalculatePosition with identical seed inputs 1000 times
- Assert Vector2 equality to 4 decimal places
- Time budget: test must complete in <1 second

## Constraints
- BallisticsSystem must NOT use Unity's Time.deltaTime
- Must use fixed TIME_STEP constant
- Test must be deterministic (no random seeds in calculation)

## Dependencies
TASK-04 (ImpactData struct)

## Validation Checklist
- [ ] Test exists in Editor Test Runner
- [ ] Test passes with identical inputs
- [ ] Test would fail if float math diverges
- [ ] No Time.deltaTime used in CalculatePosition

## Expected Result
Automated CI/editor test confirms BallisticsSystem determinism.

## Priority
P1 - Unblocks all multiplayer work

## Estimated Complexity
30 minutes