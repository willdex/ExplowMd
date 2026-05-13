# Explow Documentation

This folder contains all documentation for the Explow 2D artillery game project.

## Folder Structure

```
Docs/
├── README.md                    # This file - index and navigation
├── PROJECT_OVERVIEW.md          # Complete project overview (START HERE)
├── ARCHITECTURE.md              # Project architecture description
├── PROJECT_SUMMARY.md           # Quick reference for AI architects
│
├── core_gameplay.md            # Core gameplay mechanics
├── beast_roster.md             # Beast types and stats
├── ballistics_system.md        # Projectile physics system
├── terrain_system.md           # Terrain destruction system
├── unity_architecture.md       # Unity-specific architecture notes
│
├── ai_minimax.md               # AI Minimax algorithm documentation
├── art_pipeline.md             # Art/asset pipeline documentation
├── multiplayer_design.md        # Multiplayer architecture
│
├── known_issues.md             # Current issues and blockers
├── next_tasks.md               # Prioritized task list
├── PROGRESS.md                 # Project progress history
├── CHANGELOG.md                # Detailed change log
│
├── milestones/                 # Milestone definitions
│   ├── MILESTONE-01.md         # First Playable Local Match
│   └── MILESTONE-02.md         # Stable Multiplayer Foundation
│
├── tasks/                      # Individual task definitions (12 tasks)
│   ├── TASK-01.md through TASK-12.md
│
├── roadmap/                    # Production roadmap
│   └── production_roadmap.md   # Implementation priority order
│
├── ARCHITECTURE/               # Detailed architecture docs
│   └── 00_DECISIONES_TECNICAS.md
│
├── CHANGELOG/                  # Additional changelog info
│   └── CHANGE_LOG.md
│
├── IMPLEMENTATION/             # Implementation guides
│   ├── GAME_SETUP.md
│   └── SPRINT_01_PLAN.md
│
└── REVIEWS/                   # Technical reviews
    └── opus_technical_review.md
```

## Quick Start

1. **New to project?** → Read `PROJECT_OVERVIEW.md` first
2. **Understanding architecture?** → Read `ARCHITECTURE.md`
3. **Current issues?** → Read `known_issues.md`
4. **What needs to be done?** → Read `next_tasks.md` or `roadmap/production_roadmap.md`
5. **Milestone details?** → Read `milestones/MILESTONE-01.md` and `MILESTONE-02.md`

## Key Documents

| Document | Purpose |
|----------|---------|
| `PROJECT_OVERVIEW.md` | Complete project understanding for AI |
| `roadmap/production_roadmap.md` | Implementation priority order |
| `milestones/MILESTONE-01.md` | First playable game definition |
| `known_issues.md` | Current blockers and bugs |
| `opus_technical_review.md` | Senior technical review |

## Repository Info

- **Main Project:** https://github.com/wickd/Explow
- **Documentation:** https://github.com/willdex/ExplowMd (this repo)
- **Engine:** Unity 6000.3.15f1 (2D URP)

## Status

- Project has working MCP integration (HTTP port 8080)
- Turn system implemented with events
- AI opponent exists (MinimaxAI)
- Projectile physics using BallisticsSystem (no Rigidbody2D)
- Terrain as Tilemap with destruction
- Documentation being organized for AI handoff

---

*Last updated: 2026-05-12*