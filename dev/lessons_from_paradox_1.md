# review report of @dev/ref/paradox/00-analysis-summary.md
To improve @dev/FEATURES.md :
## features good to have
- emergent story-telling
    - Use flexible event systems for dynamic behavior
- text-based configuration, Game mechanics defined in scriptable text files, Keep game logic in easily modifiable files
- comprehensive modding tools with multi-channel content delivery
    - Dynamic enabling/disabling based on available content
    - Hot-reloading and live editing capabilities
    - automatic wiki documentation system for mod content
    - Implement clear override hierarchies
- robust, cross-platform
    - version control, dependency management, and content validation
    - use typescript scripts only

## features to avoid
- graphical UI related features
    - focus on plain text interface @dev/FEATURES.md #Overview
- dependencies on third-party libraries, databases, or frameworks
    - scripts should be self-contained
    - scripts should be put into a single folder
- data-storage: store formatted data in plain text files
- hardcoded "ecosystem" framework
    - please abstract possible elements, which is not limited to a specific game or simulation
    - that is -- focus on a more generic engine architecture
        which can be applied for precise simulation of "theoretical physics resercher's life"
- multi-layered simulation on different timescales
    - the engine can be turn-based, rather than real-time
- use "DLC" term as "developers' content" and "MOD" term as "community content"
    - use "extensions" instead, which are created by the user
    - extensions should be created inside the engine, i.e. all extensions should be created in the same way as the core engine
    - extensions can have dependencies on other extensions, but not on the core engine
- merchandising and monetization strategies
    - focus on the engine architecture, not on the business model
    - no need to implement subscription models or DLC monetization features
- care about community
    - it is not intended to be published


