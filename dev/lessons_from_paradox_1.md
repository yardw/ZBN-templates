# review report of @dev/ref/paradox/...
To improve @dev/FEATURES.md :
## features good to have
- emergent story-telling @ref/paradox/00-analysis-summary.md#1. Framework-First Design Philosophy
    - event-driven simulation
- additive non-breaking expansions @ref/paradox/00-analysis-summary.md#2. Modular DLC Architecture
- maintain an automatic wiki documentation system

## features to avoid
- graphical UI related features
    - focus on plain text interface @dev/FEATURES.md #Overview
- dependencies on third-party libraries, databases, or frameworks
    - use pure typescript
    - store formatted data in plain text files
- hardcoded "ecosystem" framework
    - please abstract possible elements, which is not limited to a specific game or simulation
    - that is -- focus on a more generic engine architecture
        which can be applied for precise simulation of "theoretical physics resercher's life"
- multi-layered simulation on different timescales
    

## additional features
- create dlc-like extensions inside the engine
