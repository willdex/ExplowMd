# Ballistics System

## Design Decision
**NO Rigidbody2D for projectiles** - required for multiplayer determinism.

## Core Formula
```
position = start + velocity * t + 0.5 * (gravity + windForce) * t²
```

## Constants (Constants.cs)
- GRAVITY = 9.8
- WIND_AFFECTION_MULTIPLIER = 0.02
- TIME_STEP = 0.02 (physics substeps)

## CalculateInitialVelocity
```csharp
Vector2 BallisticsSystem.CalculateInitialVelocity(float angle, float power, float powerMax)
```

## CalculatePosition
```csharp
Vector2 CalculatePosition(Vector2 start, Vector2 velocity, float time, WindData wind)
```

## Collision Detection
- Raycast2D from current position to next predicted position
- Continuous collision check against terrain layer
- On impact: fire OnImpact delegate

## Projectile Types
- DIRECT, PARABOLIC, BOUNCE, SPLIT, DELAYED, PIERCE

## Used By
- ProjectileController (runtime flight)
- AimingIndicator (trajectory preview)