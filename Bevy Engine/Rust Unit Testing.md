## Test Organization

```
project/
  src/
    lib.rs           # Library code with unit tests
    components/
      energy.rs      # Component code with unit tests
    systems/
      movement.rs    # System code with unit tests
  tests/             # Integration tests
    energy_tests.rs  # Tests energy from outside
    game_tests.rs    # Tests whole game systems
```

- **Unit tests**: Inside source files, test private implementation
- **Integration tests**: In `/tests` directory, test public API

[Docs: Test Organization](https://doc.rust-lang.org/book/ch11-03-test-organization.html)

## Unit Test Basics

```rust
// Inside any .rs file
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;
    
    #[test]
    fn test_add() {
        assert_eq!(add(2, 2), 4);
    }
}
```

- `#[cfg(test)]`: Only compile for tests
- `#[test]`: Mark function as a test
- Tests pass if they don't panic

[Docs: Writing Tests](https://doc.rust-lang.org/book/ch11-01-writing-tests.html)

## Assertions

```rust
// Basic assertions
assert!(value);              // Must be true
assert_eq!(left, right);     // Must be equal
assert_ne!(left, right);     // Must not be equal

// With custom error message
assert!(value, "Value should be true");
assert_eq!(left, right, "Values should be equal: {} vs {}", left, right);

// Float comparison (approximate equality)
assert!((left - right).abs() < 0.001);
```

[Docs: Assertions](https://doc.rust-lang.org/book/ch11-01-writing-tests.html#checking-results-with-the-assert-macro)

## Advanced Test Options

```rust
// Test expected panic
#[test]
#[should_panic(expected = "divide by zero")]
fn test_divide_by_zero() {
    divide(10, 0);
}

// Test returning Result
#[test]
fn test_parse() -> Result<(), ParseError> {
    let num = parse_number("123")?;
    assert_eq!(num, 123);
    Ok(())
}

// Ignore slow tests
#[test]
#[ignore]
fn expensive_test() {
    // Long-running test
}
```

[Docs: Test Control](https://doc.rust-lang.org/book/ch11-02-running-tests.html)

## Setting Up Tests

```rust
// Simple setup function
fn setup() -> TestData {
    TestData {
        value: 42,
        name: "test".to_string()
    }
}

#[test]
fn test_with_setup() {
    let data = setup();
    // Use data for test
}

// Shared test fixture (with once_cell)
use once_cell::sync::Lazy;

static TEST_DATA: Lazy<TestData> = Lazy::new(|| {
    TestData {
        value: 42,
        name: "test".to_string()
    }
});

#[test]
fn test_with_fixture() {
    assert_eq!(TEST_DATA.value, 42);
}
```

[Docs: Test Modules](https://doc.rust-lang.org/rust-by-example/testing/unit_testing.html)

## Running Tests

```bash
# Run all tests
cargo test

# Run specific test
cargo test test_name

# Run tests in a specific file
cargo test --test integration_test

# Run ignored tests
cargo test -- --ignored

# Show output from tests
cargo test -- --nocapture

# Run tests with a specific feature
cargo test --features "feature_name"
```

[Docs: Running Tests](https://doc.rust-lang.org/book/ch11-02-running-tests.html)

## Testing with Bevy

```rust
// Basic Bevy system test
#[test]
fn test_movement_system() {
    // Create minimal test app
    let mut app = App::new();
    
    // Add minimal plugins and systems
    app.add_plugins(MinimalPlugins)
       .add_systems(Update, movement_system);
    
    // Spawn test entity
    let entity = app.world.spawn((
        Transform::from_xyz(0.0, 0.0, 0.0),
        Velocity(Vec2::new(1.0, 0.0))
    )).id();
    
    // Run one frame
    app.update();
    
    // Check result
    let transform = app.world.entity(entity).get::<Transform>().unwrap();
    assert_eq!(transform.translation.x, 1.0);
}

// Test setup for Bevy
fn setup_test_app() -> App {
    let mut app = App::new();
    app.add_plugins(MinimalPlugins)
       .add_systems(Update, (
           movement_system,
           collision_system
       ));
    app
}

#[test]
fn test_collision() {
    let mut app = setup_test_app();
    // Test specific things...
}
```

## Testing Patterns for Games

```rust
// Parameterized tests
#[test]
fn test_damage_calculation() {
    // Test cases as tuples: (attack, defense, expected_damage)
    let test_cases = [
        (10, 5, 5),   // normal case
        (10, 10, 1),  // minimum damage
        (0, 5, 0),    // no attack
    ];
    
    for (attack, defense, expected) in test_cases {
        let damage = calculate_damage(attack, defense);
        assert_eq!(damage, expected);
    }
}

// Random testing
#[test]
fn test_health_system() {
    use rand::Rng;
    let mut rng = rand::thread_rng();
    
    for _ in 0..10 {
        let max_health = rng.gen_range(50.0..200.0);
        let mut health = Health { current: max_health, maximum: max_health };
        
        // Test percentage-based damage
        let damage_percent = rng.gen_range(10.0..50.0);
        let expected = (max_health * (1.0 - damage_percent / 100.0)).max(0.0);
        
        health.damage_percent(damage_percent);
        assert!((health.current - expected).abs() < 0.001);
    }
}
```

## GitHub Actions CI

GitHub Actions automatically runs your tests when code is pushed, ensuring nothing breaks while you develop. It gives you instant feedback and those sweet green badges for your README!

```yaml
# .github/workflows/rust-tests.yml
name: Rust Tests

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    container:
      image: rust:alpine
    steps:
    - uses: actions/checkout@v3
    
    - name: Install dependencies
      run: |
        apk add --no-cache libasound2-dev libudev-dev
    
    - name: Run tests
      run: cargo test --verbose
      
    - name: Run test coverage
      run: |
        cargo install cargo-tarpaulin
        cargo tarpaulin --out Xml
```

**Setup Steps:**

1. Create `.github/workflows` directory in your repo
2. Add `rust-tests.yml` file with the content above
3. Push to GitHub - tests run automatically on push/PR

**Adding a Badge to README.md:**

```markdown
![Rust Tests](https://github.com/username/repo-name/actions/workflows/rust-tests.yml/badge.svg)
```

**Benefits:**

- Catches test failures before they reach production
- Gives immediate feedback on pull requests
- Shows project health at a glance with badges
- Runs tests consistently in a clean environment
- Alpine containers keep things lightweight and fast
- Helps maintain code quality as your project grows

[Docs: GitHub Actions for Rust](https://github.com/actions-rs/example)

## Useful Resources

- [Rust Test Documentation](https://doc.rust-lang.org/book/ch11-00-testing.html) - Official testing guide
- [Bevy Test Examples](https://github.com/bevyengine/bevy/tree/main/tests) - Examples from Bevy itself
- [Test Doubles in Rust](https://blog.logrocket.com/test-doubles-rust-mockall-expectations/) - Mocking in Rust
- [Rust Testing Strategies](https://blog.logrocket.com/rust-testing-strategies/) - Advanced testing approaches
- [Rust By Example: Testing](https://doc.rust-lang.org/rust-by-example/testing.html) - More examples
- [Bevy Testing Guide](https://bevy-cheatbook.github.io/pitfalls/testing.html) - Bevy-specific testing tips
- [Proptest](https://github.com/AltSysrq/proptest) - Property-based testing for Rust
- [GitHub Actions for Rust](https://github.com/actions-rs/meta) - Official Rust actions for GitHub
- [Codecov Rust Guide](https://about.codecov.io/blog/getting-started-with-code-coverage-for-rust/) - Setting up test coverage
- [Rust CI Template](https://github.com/rust-github/template) - Template repo with CI setup