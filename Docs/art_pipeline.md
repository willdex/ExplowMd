# Art Pipeline

## 3D Modeling (Blender → Unity)
- Low-poly stylized aesthetic
- 3,000-5,000 tris per beast
- 1,500-2,500 tris per rider
- FBX export with embedded animations

## Recommended Animations (8 per beast)
1. Idle, Mover, Apuntar, Cargar
2. Disparar, Herida, Moribunda, Muerte

## Rendering
- 2D/2.5D side-scrolling perspective
- Toon shader or PBR simplified
- Sorting layers: Characters, Riders, Projectiles, Terrain, UI

## VFX (Particle System)
- Charge: Element-colored particles, intensity = power
- Trail: Trail Renderer + particles, 0.5s persistence
- Impact: Burst particle system, elemental variants

## Current Status
- 2D sprites used as placeholder (Texture2D 32x32)
- No 3D models yet
- No animations implemented