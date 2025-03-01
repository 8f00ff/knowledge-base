
## Core Concepts

**Entity**: Just an ID or "container" (no data or behavior)  
**Component**: Pure data (no behavior)  
**System**: Logic that processes entities with specific components

## Key Design Principles

### Component Design

- Small, focused components (Position, Velocity, Health)
- Prefer component composition over inheritance
- Make components reusable across entity types
- Single responsibility per component
- Avoid components with behaviors/methods
- Avoid tight coupling between components

### System Design

- Each system does ONE thing well
- Systems operate on queries (specific component combinations)
- Keep systems small and focused
- Break complex behaviors into multiple systems
- Avoid systems accessing global state
- Avoid systems with dependencies on specific entities

### Data Flow

- Entities should be data-driven
- Use events for communication between systems
- Resources for shared/global state
- One-way data flow where possible
- Avoid direct system-to-system communication

## Bevy-Specific Principles

### Component Organization

- Marker components (empty struct) to tag entity types
- Bundle related components together
- Split large components into smaller ones
- Use generics for related components

### Queries & Filters

- Be specific in queries (only request components you need)
- Use filters (`With<T>`, `Without<T>`) to narrow queries
- Query for changed components when possible
- Use `Option<&Component>` for optional components

### System Organization

- Group related systems in plugins
- Control system execution order explicitly
- Use run conditions to toggle systems
- Schedule systems based on data dependencies

### Common Patterns

- Component flagging: Add/remove components to change behavior
- Parent-child for hierarchical entities
- Events for decoupled communication
- Commands for deferred entity changes
- State machines via resource or component states

## Useful Resources

- [Bevy ECS Book](https://bevy-cheatbook.github.io/programming/ecs-intro.html) - Excellent overview of ECS in Bevy
- [ECS Design Patterns](https://github.com/dbartolini/data-oriented-design) - Data-oriented design resources
- [Specs Book](https://specs.amethyst.rs/docs/tutorials/01_intro.html) - Good general ECS concepts
- [Game Programming Patterns](https://gameprogrammingpatterns.com/component.html) - Classic book on game architecture
- [Catlike Coding ECS Tutorial](https://catlikecoding.com/unity/tutorials/basics/game-objects-and-scripts/) - Unity-focused but good principles
- [Data-oriented Design](https://www.dataorienteddesign.com/dodbook/) - Free book on data-oriented programming
- [Bevy Cookbook](https://bevy-cheatbook.github.io) - Practical patterns for Bevy
