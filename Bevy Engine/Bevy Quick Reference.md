_Lean reference for Bevy's core features with links to official docs._

## Core ECS Concepts

### Components

Components are just data attached to entities.

``` rust
#[derive(Component)]
struct Position(Vec2);

#[derive(Component)]
struct Player; // marker component
```

**Breakdown:**

- Use empty structs as markers (`Player`, `Enemy`)
- Single-value components are common (Position, Velocity, Health)
- Add `#[derive(Component)]` to all component structs
- Can be inserted/removed dynamically with `commands.entity().insert()`/`remove()`

[Official Docs: Components](https://bevyengine.org/learn/book/getting-started/ecs/)

### Systems

Systems are functions that process entities with specific components.

``` rust
fn move_system(time: Res<Time>, mut query: Query<(&mut Position, &Velocity)>) {
    for (mut pos, vel) in &mut query {
        pos.0 += vel.0 * time.delta_seconds();
    }
}
```

**Breakdown:**

- Function signature defines what entities it processes
- System params: `Commands`, `Query<...>`, `Res<...>`, `ResMut<...>`, `EventReader`/`EventWriter`
- Return type must be `()` or `SystemResult`

[Official Docs: Systems](https://bevyengine.org/learn/book/getting-started/ecs/#systems)

### App Setup

Configure your app with plugins, systems, and resources.

``` rust
fn main() {
    App::new()
        .add_plugins(DefaultPlugins)
        .add_systems(Startup, setup)
        .add_systems(Update, (move_system, collision_system))
        .run();
}
```

**Breakdown:**

- `add_plugins()` - Register plugin groups
- `add_systems(Schedule, system)` - Run systems at specific times
    - `Startup` - Once when app starts
    - `Update` - Every frame
    - `FixedUpdate` - At fixed intervals
- `insert_resource()` - Add global data
- `add_event<T>()` - Register event types
- `run()` - Start the game loop

[Official Docs: App](https://bevyengine.org/learn/book/getting-started/ecs/#app)

## Queries

Queries select entities with specific components to process.

``` rust
// Basic query with filters
fn system(
    // Entities with Position AND Velocity AND Player, but NOT Enemy
    query: Query<(&Position, &mut Velocity), (With<Player>, Without<Enemy>)>,
    // Only entities whose Health changed this frame
    changed_query: Query<&Health, Changed<Health>>,
    // PowerUp is optional
    optional_query: Query<(&Position, Option<&PowerUp>)>,
) {
    // Basic iteration
    for (pos, mut vel) in &mut query {
        // Work with components
    }
    
    // Get a single entity (errors if not exactly one match)
    if let Ok((pos, mut vel)) = query.get_single_mut() {
        // Work with the only matching entity
    }
}
```

**Breakdown:**

- Tuple of component references to access: `&Component` (read), `&mut Component` (write)
- Filter with: `With<T>`, `Without<T>`, `Changed<T>`, `Added<T>`, `Or<(A, B)>`
- `Option<&T>` for components that might not exist on entity
- Query methods: `.iter()`, `.get(entity)`, `.get_single()`, `.is_empty()`

[Official Docs: Queries](https://bevyengine.org/learn/book/programming-patterns/queries/)

## Resources

Global data shared across systems.

``` rust
// Define a resource
#[derive(Resource)]
struct Score(u32);

// Add to app
app.insert_resource(Score(0));

// Use in systems
fn system(time: Res<Time>, mut score: ResMut<Score>) {
    score.0 += 1;
}
```

**Breakdown:**

- `Res<T>` - Read-only access
- `ResMut<T>` - Mutable access
- Common built-in resources: `Time`, `Input<KeyCode>`, `AssetServer`, `Windows`
- Can check if changed: `if score.is_changed() { ... }`

[Official Docs: Resources](https://bevyengine.org/learn/book/programming-patterns/resources/)

## Events

Loose coupling between systems via message passing.

``` rust
// Define an event
#[derive(Event)]
struct CollisionEvent { entity: Entity, position: Vec3 }

// Register in app
app.add_event::<CollisionEvent>();

// Send events
fn send_system(mut collision_events: EventWriter<CollisionEvent>) {
    collision_events.send(CollisionEvent { 
        entity: entity, 
        position: position 
    });
}

// Receive events
fn receive_system(mut collision_events: EventReader<CollisionEvent>) {
    for event in collision_events.read() {
        // Handle event
    }
}
```

**Breakdown:**

- Events only last one frame by default
- `EventWriter<T>` - Send events with `.send()` or `.send_batch()`
- `EventReader<T>` - Receive with `.read()` or `.clear()`
- Great for: collisions, input actions, game state changes

[Official Docs: Events](https://bevyengine.org/learn/book/programming-patterns/events/)

## Assets

Load and manage external files.

``` rust
// Load assets
fn setup(mut commands: Commands, asset_server: Res<AssetServer>) {
    let texture: Handle<Image> = asset_server.load("sprites/player.png");
    let font: Handle<Font> = asset_server.load("fonts/roboto.ttf");
    
    commands.spawn(SpriteBundle {
        texture,
        ..default()
    });
}
```

**Breakdown:**

- `asset_server.load()` returns a `Handle<T>`, not the asset itself
- Common asset types: `Image`, `Font`, `AudioSource`, `Scene`
- Assets load asynchronously
- Check status with `asset_server.get_load_state(&handle)`
- Create asset collections with `#[derive(Resource)] struct GameAssets { ... }`

[Official Docs: Assets](https://bevyengine.org/learn/book/programming-patterns/assets/)

## Parent-Child

Create hierarchical relationships between entities.

``` rust
fn setup(mut commands: Commands) {
    // Parent entity
    let parent = commands.spawn(SpriteBundle { /* ... */ }).id();
    
    // Child entities
    let child1 = commands.spawn(SpriteBundle { /* ... */ }).id();
    let child2 = commands.spawn(SpriteBundle { /* ... */ }).id();
    
    // Create relationship
    commands.entity(parent).push_children(&[child1, child2]);
}
```

**Breakdown:**

- Children inherit transforms from parents
- Query parents with `Parent` component
- Query children with `Children` component
- Despawn entire hierarchy with `commands.entity(parent).despawn_recursive()`

[Official Docs: Hierarchy](https://bevyengine.org/learn/book/programming-patterns/hierarchies/)

## States

Manage game states (menus, gameplay, etc.).

``` rust
// Define states
#[derive(States, Clone, Debug, PartialEq, Eq, Hash, Default)]
enum GameState { 
    #[default] MainMenu, 
    Playing, 
    Paused 
}

// In app setup
app.add_state::<GameState>()
   // Run when entering a state
   .add_systems(OnEnter(GameState::Playing), setup_game)
   // Run while in a state
   .add_systems(Update, player_movement.run_if(in_state(GameState::Playing)))
   // Run when exiting a state
   .add_systems(OnExit(GameState::MainMenu), cleanup_menu);

// Change state
fn button_system(mut next_state: ResMut<NextState<GameState>>) {
    next_state.set(GameState::Playing);
}
```

**Breakdown:**

- `OnEnter` - Run once when entering state
- `OnExit` - Run once when exiting state
- `run_if(in_state())` - Conditional system execution
- `NextState<T>` - Change state with `.set()`
- Can have multiple state types for different aspects of game

[Official Docs: States](https://bevyengine.org/learn/book/programming-patterns/states/)

## Plugins

Organize systems and components into reusable modules.

``` rust
// Create a plugin
pub struct PlayerPlugin;

impl Plugin for PlayerPlugin {
    fn build(&self, app: &mut App) {
        app.add_systems(Startup, spawn_player)
           .add_systems(Update, player_movement)
           .register_type::<Player>()
           .add_event::<PlayerEvent>();
    }
}

// Use plugin
app.add_plugins(DefaultPlugins)  // Bevy's built-in plugins
   .add_plugins(PlayerPlugin);   // Your custom plugin
```

**Breakdown:**

- Group related functionality (player, enemies, UI)
- `DefaultPlugins` includes: rendering, input, audio, etc.
- `MinimalPlugins` for headless apps
- Create plugin groups with `PluginGroup` trait

[Official Docs: Plugins](https://bevyengine.org/learn/book/programming-patterns/plugins/)

## Common Snippets

### Input Handling

``` rust
fn input_system(
    keys: Res<Input<KeyCode>>,
    buttons: Res<Input<MouseButton>>,
    mut query: Query<&mut Transform, With<Player>>,
) {
    // Keyboard input
    if keys.just_pressed(KeyCode::Space) { /* Just pressed this frame */ }
    if keys.pressed(KeyCode::W) { /* Currently held down */ }
    if keys.just_released(KeyCode::E) { /* Just released this frame */ }
    
    // Mouse input
    if buttons.pressed(MouseButton::Left) { /* Mouse button pressed */ }
}
```

### Timer Example

``` rust
#[derive(Component)]
struct SpawnTimer(Timer);

fn spawn_system(time: Res<Time>, mut query: Query<&mut SpawnTimer>) {
    let mut timer = query.single_mut();
    
    // Update and check timer
    if timer.0.tick(time.delta()).just_finished() {
        // Timer completed! Do something...
        println!("Timer finished!");
    }
}

// Create with:
commands.spawn(SpawnTimer(Timer::from_seconds(2.0, TimerMode::Repeating)));
```

### Bundle Example

``` rust
#[derive(Bundle)]
struct PlayerBundle {
    sprite: SpriteBundle,
    player: Player,
    health: Health,
    speed: Speed,
}

// Use in spawn:
commands.spawn(PlayerBundle {
    sprite: SpriteBundle {
        texture: asset_server.load("player.png"),
        ..default()
    },
    player: Player,
    health: Health(100.0),
    speed: Speed(250.0),
});
```

## Useful Resources

- [Bevy Website](https://bevyengine.org/)
- [Bevy GitHub](https://github.com/bevyengine/bevy)
- [Bevy Cheatbook](https://bevy-cheatbook.github.io/)
- [Bevy Examples](https://github.com/bevyengine/bevy/tree/latest/examples)
- [Bevy Assets (Community Resources)](https://bevyengine.org/assets/)
