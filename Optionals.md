## Optional Types
Optional is used to convey a non-error result that is either a value or the absence of a value. For example, when searching a collection for a value, not finding the value is **still a valid and expected** outcome, not an error.

```swift
// GOOD:
if let index = index(of: thing, in: lotsOfThings) {
  print("Found it")
} else {
  print("Didn't find it")
}

// AVOID:
let index = index(of: thing, in: lotsOfThings)
if index != -1 {
  print("Found it")
} else {
  print("Didn't find it")
}
```
---
Conditional statements that test that an Optional is non-nil but do not access the wrapped value are written as comparisons to nil. The following example is clear about the programmer’s intent:
```swift
// GOOD:
if value != nil {
  print("value was not nil")
}
```
This example, while taking advantage of Swift’s pattern matching and binding syntax, obfuscates the intent by appearing to unwrap the value and then immediately throw it away.
```swift
// AVOID:
if let _ = value {
  print("value was not nil")
}
```

### Force Unwrapping and Force Casts
Force-unwrapping and force-casting are often code smells and are strongly discouraged. Unless it is extremely clear from surrounding code why such an operation is safe, a comment should be present that describes the invariant that ensures that the operation is safe. For example,
```swift
let value = getSomeInteger()

// ...

// GOOD as exeption with a comment:

/* 
This force-unwrap is safe because `value` is guaranteed to fall within the
valid enum cases because it came from some data source that only permits
those raw values.
*/
return SomeEnum(rawValue: value)!
```
> Exception: Force-unwraps are allowed in unit tests and test-only code without additional documentation. This keeps such code free of unnecessary control flow. In the event that nil is unwrapped or a cast operation is to an incompatible type, the test will fail which is the desired result.

### Implicitly Unwrapped Optionals
Implicitly unwrapped optionals are inherently **unsafe** and **should be avoided** whenever possible in favor of non-optional declarations or regular Optional types. Exceptions are described below.


### Nesting and Namespacing
Swift allows enums, structs, and classes to be nested, so nesting is preferred (instead of naming conventions) to express scoped and hierarchical relationships among types when possible. For example, flag enums or error types that are associated with a specific type are nested in that type.

Preffered:
```swift
class Parser {
  enum Error: Swift.Error {
    case invalidToken(String)
    case unexpectedEOF
  }

  func parse(text: String) throws {
    // ...
  }
}
```
Not Preffered:
```swift
class Parser {
  func parse(text: String) throws {
    // ...
  }
}

enum ParseError: Error {
  case invalidToken(String)
  case unexpectedEOF
}
```

Declaring an enum without cases is the canonical way to define a “namespace” to group a set of related declarations, such as constants or helper functions. This enum automatically has no instances and does not require that extra boilerplate code be written to prevent instantiation.
```swift
// GOOD:
enum Dimensions {
  static let tileMargin: CGFloat = 8
  static let tilePadding: CGFloat = 4
  static let tileContentSize: CGSize(width: 80, height: 64)
}
// AVOID:
struct Dimensions {
  private init() {}

  static let tileMargin: CGFloat = 8
  static let tilePadding: CGFloat = 4
  static let tileContentSize: CGSize(width: 80, height: 64)
}
```
---

### **guards** for Early Exits
A guard statement, compared to an if statement with an inverted condition, provides visual emphasis that the condition being tested is a special case that causes early exit from the enclosing scope.

Furthermore, guard statements improve readability by eliminating extra levels of nesting (the “pyramid of doom”); failure conditions are closely coupled to the conditions that trigger them and the main logic remains flush left within its scope.

This can be seen in the following examples; in the first, there is a clear progression that checks for invalid states and exits, then executes the main logic in the successful case. In the second example without guard, the main logic is buried at an arbitrary nesting level and the thrown errors are separated from their conditions by a great distance.

Preffered:
```swift
func discombobulate(_ values: [Int]) throws -> Int {
  guard let first = values.first else {
    throw DiscombobulationError.arrayWasEmpty
  }
  guard first >= 0 else {
    throw DiscombobulationError.negativeEnergy
  }

  var result = 0
  for value in values {
    result += invertedCombobulatoryFactory(of: value)
  }
  return result
}
```

Not Preffered:
```swift
func discombobulate(_ values: [Int]) throws -> Int {
  if let first = values.first {
    if first >= 0 {
      var result = 0
      for value in values {
        result += invertedCombobulatoryFactor(of: value)
      }
      return result
    } else {
      throw DiscombobulationError.negativeEnergy
    }
  } else {
    throw DiscombobulationError.arrayWasEmpty
  }
}
```
