# TASK-01: Create ProjectileController.prefab

## ID
TASK-01

## Goal
Create a visible projectile prefab that can be instantiated by ProjectilePool and travels along the calculated ballistic trajectory.

## Problem
ProjectilePool.GetProjectile() returns null because no ProjectileController prefab exists in the project. This blocks ALL shot visualization regardless of how well BallisticsSystem calculates.

## Requirements
- Create `Assets/_Project/Prefabs/Projectiles/ProjectileController.prefab`
- Add SpriteRenderer component (32x32 placeholder texture)
- Configure CircleCollider2D as trigger
- Leave ProjectileController.cs as the script (no changes to script itself)

## Constraints
- Do NOT modify ProjectileController.cs
- Do NOT add Rigidbody2D (BallisticsSystem handles physics)
- Prefab must be in Resources folder or registered with ProjectilePool

## Dependencies
None (standalone task)

## Validation Checklist
- [ ] Prefab exists at correct path
- [ ] Prefab has SpriteRenderer with valid texture
- [ ] Prefab has CircleCollider2D (trigger)
- [ ] ProjectilePool.Get() returns non-null in sandbox test
- [ ] Projectile appears in scene at fired position

## Expected Result
Pool.GetProjectile() returns a visible GameObject that can be positioned and moved by BallisticsSystem.

## Priority
P0 - Critical Blocker

## Estimated Complexity
15 minutes