# Terrain System

## Implementation: Tilemap (MVP)
- 32x32px tiles
- Grid cell size: 1 unit
- Two tilemaps: ground (static) + destructible

## Destruction
- Circular radius removal per explosion
- EXPLOSION_RADIUS_TILES = 3f
- TileBase set to null in radius

## TerrainManager
- destructibleTilemap / groundTilemap references
- DestroyTilesAt(Vector2 center, float radius)
- TileDestruction struct for sync

## Future Migration
- Sprite Shape + shader mask for organic destruction
- Texture2D pixel-level destruction

## Sync (Multiplayer Future)
- Host authoritative
- Delta compression for tile changes
- TERRAIN_SYNC_BATCH_SIZE = 20