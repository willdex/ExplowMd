# Multiplayer Design

## Architecture: Photon PUN 2 (Free Tier)
- 20 CCU limit
- Sufficient for dev + alpha

## Host/Client Model
1. Host creates room, generates terrain
2. Host calculates ballistics, broadcasts result
3. Clients reproduce host's validated inputs

## Turn Sync
- RPC: IniciaTurno, FinalizaTurno
- TurnManager authority on host

## Terrain Sync
- Host broadcasts DestroyTile(x, y, radius)
- Delta compression for bandwidth

## Anti-Cheat
- Input validation on host
- Server-side damage calculation (future)

## Determinism Requirement
- BallisticsSystem must produce identical results on all clients
- No floating point race conditions
- Fixed timestep for physics