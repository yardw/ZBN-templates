# Engine Architecture and Modding Systems

## Executive Summary

Paradox's technical foundation consists of two proprietary engines: **Clausewitz** and **Europa**, designed from the ground up to support extensive modification and community content creation. The engine architecture prioritizes **openness, scriptability, and real-time editing capabilities**.

## Engine Evolution and Architecture

### Clausewitz Engine (Primary Platform)
**Generations:**
- Clausewitz 1.0: Europa Universalis III (2007)
- Clausewitz 2.0: Hearts of Iron III, Victoria II
- Clausewitz 2.5: Europa Universalis IV, Crusader Kings II
- Clausewitz-Jomini: Imperator: Rome, Crusader Kings III, Victoria 3

**Core Architecture:**
```
Clausewitz Engine
├── Rendering Layer (DirectX 11+)
├── Game Logic Engine
├── Script Interpreter
├── Event System
├── Database Layer
└── Modding Interface
```

### Jomini Integration (Modern Architecture)
**Two-Part Engine System:**
- **Clausewitz**: Core engine functionality and rendering
- **Jomini**: Game logic layer and modding tools
- **Integration**: Seamless cooperation between both components

## Technical Design Principles

### 1. Text-Based Configuration
**Everything-as-Data Approach:**
- Game mechanics defined in text files
- No hardcoded game logic in engine
- Runtime interpretation of game rules

**Example Structure:**
```
game_data/
├── technologies/
├── events/
├── decisions/
├── countries/
├── cultures/
└── religions/
```

### 2. Real-Time Modification
**Live Editing Capabilities:**
- Hot-reloading of text files during development
- Real-time map painting and editing
- Instant preview of changes without restart

**Development Workflow:**
```
Edit Text File → Engine Detects Change → Reload Data → Update Game State
```

### 3. Layered Modification System
**Override Hierarchy:**
```
Base Game Files (Lowest Priority)
├── DLC Files (Medium Priority)
├── Mod Files (High Priority)
└── User Overrides (Highest Priority)
```

## Modding Architecture

### Script-Based Modding
**Primary Languages:**
- **PDXScript**: Custom scripting language for game logic
- **Text Configuration**: For data definition
- **Lua**: For complex scripting (some titles)

**PDXScript Example:**
```pdxscript
technology = {
    tech_industrial_revolution = {
        cost = 1000
        time = 365
        
        effects = {
            add_building_construction_speed = 0.25
            add_goods_produced = 0.15
        }
        
        prerequisites = {
            tech_steam_engine
            tech_factory_system
        }
    }
}
```

### File-Based Override System
**Mod Structure:**
```
mod_folder/
├── descriptor.mod          # Mod metadata
├── common/                 # Game mechanics
│   ├── technologies/
│   ├── buildings/
│   └── events/
├── history/               # Starting conditions
├── gfx/                   # Graphics assets
├── localisation/          # Text translations
└── map/                   # Map modifications
```

### Dynamic Loading System
**Load Order Management:**
```cpp
class ModManager {
    void loadMods() {
        loadBaseGame();
        for (DLC dlc : enabledDLCs) {
            loadDLCContent(dlc);
        }
        for (Mod mod : enabledMods) {
            loadModContent(mod);
        }
        validateConfiguration();
    }
}
```

## Advanced Modding Features

### 1. Total Conversion Support
**Complete Game Overhaul Capabilities:**
- Replace all game assets and mechanics
- Custom map generation
- New gameplay systems
- Alternative historical periods

### 2. Cross-Game Compatibility
**Converter System:**
- Save game transfer between titles
- Standardized data formats
- Community-developed conversion tools

### 3. Scripting Extensibility
**Advanced Scripting Features:**
```pdxscript
# Complex event chains
country_event = {
    id = revolution.1
    title = "The Revolution Begins"
    
    trigger = {
        stability = { min = -3 }
        NOT = { government = republic }
        any_owned_province = {
            base_tax = 3
            culture_group = owner
        }
    }
    
    option = {
        name = "Crush the rebels!"
        add_stability = -1
        spawn_rebels = {
            type = revolutionary_rebels
            size = 3
        }
    }
}
```

## Development Tools Integration

### Visual Editing Tools
**Map Editor:**
- Real-time terrain painting
- Province boundary editing
- Trade route visualization
- Climate and geography tools

**Event Editor:**
- Visual event chain creation
- Condition and effect builders
- Localization integration
- Testing and validation tools

### Community Tool Support
**External Tool Ecosystem:**
- **CWTools**: IDE integration for VS Code
- **GitHub Actions**: Automated mod building
- **Validator Tools**: Syntax and logic checking
- **Asset Converters**: Graphics and audio processing

## Performance and Optimization

### Efficient Data Structures
**Optimized for Modification:**
- Hash-based lookups for dynamic content
- Lazy loading of unused assets
- Memory-efficient string handling
- Cached parsing of frequently accessed files

### Parallel Processing
**Multi-threaded Architecture:**
```cpp
class GameEngine {
    void updateFrame() {
        parallel_for(provinces) { updateProvince(); }
        parallel_for(characters) { updateCharacter(); }
        parallel_for(armies) { updateArmy(); }
        synchronizeChanges();
    }
}
```

## Quality Assurance for Modding

### Automated Validation
- **Syntax Checking**: Validate script files on load
- **Reference Validation**: Ensure all references exist
- **Balance Testing**: Automated gameplay testing
- **Performance Profiling**: Identify mod performance impacts

### Error Handling
```cpp
class ScriptLoader {
    bool loadScript(string filename) {
        try {
            parseFile(filename);
        } catch (ParseError e) {
            logError("Mod Error in " + filename + ": " + e.message);
            useDefaultValue();
            return false;
        }
        return true;
    }
}
```

## Community Integration

### Steam Workshop Integration
- **One-Click Installation**: Seamless mod deployment
- **Automatic Updates**: Mods update with game versions
- **Dependency Management**: Handle mod requirements
- **Rating and Discovery**: Community-driven quality assessment

### Paradox Mods Platform
- **In-Game Browser**: Browse mods without leaving game
- **Cross-Platform Support**: Console mod support
- **Creator Tools**: Integrated development environment
- **Revenue Sharing**: Monetization for quality creators

This architecture enables Paradox to maintain a thriving modding ecosystem that extends the lifespan of their games far beyond traditional single-player experiences, creating sustainable communities around each title.