# Duck Test Library

A testing framework for Duck-lang that builds on `honk` assertions. Run multiple tests, catch failures gracefully, and get beautiful output.

## Installation

```bash
goose install konacodes/test v0.1.0
```

## Quick Start

```duck
quack [migrate "git+konacodes/test" as t]

-- Group tests by feature
quack [t.describe "Math operations"]

quack [t.test "adds numbers correctly" [] => [
  quack [honk 1 + 1 == 2]
  quack [honk 5 + 5 == 10]
]]

quack [t.test "multiplies numbers" [] => [
  quack [honk 3 * 4 == 12]
]]

-- Another group
quack [t.describe "String operations"]

quack [t.test "concatenates strings" [] => [
  quack [honk "hello" + " world" == "hello world"]
]]

-- Run all tests
quack [t.run()]
```

Output:
```
Running tests...

  Math operations
    ✓ adds numbers correctly
    ✓ multiplies numbers
  String operations
    ✓ concatenates strings

  3 passing
```

## API Reference

### Test Registration

#### `describe(suite-name)`

Set the current test suite name. All tests registered after this call will be grouped under this name.

```duck
quack [t.describe "User Authentication"]
```

#### `test(name, fn)` / `it(name, fn)`

Register a test. The function should contain `honk` assertions.

```duck
quack [t.test "validates email format" [] => [
  quack [honk is-valid-email("test@example.com")]
]]

-- "it" is an alias for "test"
quack [t.it "rejects invalid emails" [] => [
  quack [honk is-valid-email("not-an-email") == false]
]]
```

#### `run()`

Run all registered tests and print results. Returns `true` if all tests passed, `false` otherwise.

```duck
quack [let success be t.run()]
quack [if success then
  quack [print "All tests passed!"]
]
```

#### `clear()`

Clear all registered tests. Useful if running multiple test files.

```duck
quack [t.clear()]
```

### Assertion Helpers

While you can use `honk` directly, these helpers provide clearer error messages:

| Function | Description |
|----------|-------------|
| `equals(actual, expected)` | Assert two values are equal |
| `not-equals(actual, expected)` | Assert two values are different |
| `is-true(value)` | Assert value is `true` |
| `is-false(value)` | Assert value is `false` |
| `is-nil(value)` | Assert value is `nil` |
| `is-not-nil(value)` | Assert value is not `nil` |
| `is-greater(actual, expected)` | Assert actual > expected |
| `is-less(actual, expected)` | Assert actual < expected |
| `has(list, item)` | Assert list contains item |
| `includes(string, substring)` | Assert string contains substring |

Example:

```duck
quack [t.test "user age validation" [] => [
  quack [let age be 25]
  quack [t.is-greater age 18]
  quack [t.is-less age 100]
]]

quack [t.test "list operations" [] => [
  quack [let items be list(1, 2, 3)]
  quack [t.has items 2]
  quack [t.equals len(items) 3]
]]
```

## Test Organization

### Single File

For small projects, put all tests in one file:

```duck
-- tests.duck
quack [migrate "git+konacodes/test" as t]

quack [t.describe "Feature A"]
quack [t.test "..." [] => [...]]

quack [t.describe "Feature B"]
quack [t.test "..." [] => [...]]

quack [t.run()]
```

### Multiple Files

For larger projects, organize tests by feature:

```
tests/
├── run-all.duck      <- Runs everything
├── math-tests.duck
├── string-tests.duck
└── api-tests.duck
```

Each test file:
```duck
-- math-tests.duck
quack [migrate "git+konacodes/test" as t]

quack [t.describe "Math"]
quack [t.test "..." [] => [...]]
-- Don't call t.run() here
```

The runner:
```duck
-- run-all.duck
quack [migrate "git+konacodes/test" as t]
quack [migrate "./math-tests.duck"]
quack [migrate "./string-tests.duck"]
quack [migrate "./api-tests.duck"]

quack [t.run()]
```

## Tips

1. **Keep tests focused** - Each test should verify one thing
2. **Use descriptive names** - "validates email format" > "test1"
3. **Group related tests** - Use `describe` to organize by feature
4. **Test edge cases** - Empty strings, zero, nil, negative numbers
5. **Run tests often** - Catch bugs early

## Example: Testing a Calculator

```duck
quack [migrate "git+konacodes/test" as t]

-- The code we're testing
quack [define add taking [a, b] as
  quack [return a + b]
]

quack [define divide taking [a, b] as
  quack [if b == 0 then
    quack [return nil]
  ]
  quack [return a / b]
]

-- The tests
quack [t.describe "Calculator"]

quack [t.test "adds positive numbers" [] => [
  quack [t.equals add(2, 3) 5]
]]

quack [t.test "adds negative numbers" [] => [
  quack [t.equals add(-2, -3) -5]
]]

quack [t.test "adds mixed numbers" [] => [
  quack [t.equals add(-2, 5) 3]
]]

quack [t.test "divides numbers" [] => [
  quack [t.equals divide(10, 2) 5]
]]

quack [t.test "handles division by zero" [] => [
  quack [t.is-nil divide(10, 0)]
]]

quack [t.run()]
```

## License

MIT - Test freely!
