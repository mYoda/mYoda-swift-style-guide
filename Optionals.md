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

When accessing an optional value, use optional chaining if the value is only accessed once or if there are many optionals in the chain:
```swift
textContainer?.textLabel?.setNeedsDisplay()
```

When naming optional variables and properties, avoid naming them like optionalString or maybeView since their optional-ness is already in the type declaration.

---
### Optional binding

For **optional binding**, shadow the original name whenever possible rather than using names like unwrappedView or actualLabel.

```swift
var subview: UIView?
var volume: Double?

// later on...

// GOOD:
if let subview = subview, let volume = volume {
  // do something with unwrapped subview and volume
}

// AVOID:
if let unwrappedSubview = subview {
  if let realVolume = volume {
    // do something with unwrappedSubview and realVolume
  }
}
```

Another example:
```swift
// GOOD:
resource.request().onComplete { [weak self] response in
  guard let self = self else { return }
  let model = self.updateModel(response)
  self.updateUI(model)
}

// AVOID:
// another example
UIView.animate(withDuration: 2.0) { [weak self] in
  guard let strongSelf = self else { return }
  strongSelf.alpha = 1.0
}
```
---
### Conditional statements

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
