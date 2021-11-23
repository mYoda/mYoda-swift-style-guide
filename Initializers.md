## Initializers

For clarity, initializer arguments that correspond directly to a stored property have the same name as the property. Explicit self. is used during assignment to disambiguate them.

**Preferred**:
```swift
public struct Person {
  public let name: String
  public let phoneNumber: String

  // GOOD:
  public init(name: String, phoneNumber: String) {
    self.name = name
    self.phoneNumber = phoneNumber
  }

  // AVOID:
  public init(name otherName: String, phoneNumber otherPhoneNumber: String) {
    name = otherName
    phoneNumber = otherPhoneNumber
  }
}
```

For structs, Swift synthesizes a non-public memberwise init that takes arguments for var properties and for any let properties that lack default values. When that initializer is suitable (that is, a public one is not needed), it is used and no explicit initializer is written.

The initializers declared by the special ExpressibleBy*Literal compiler protocols are never called directly.

```swift 
struct Kilometers: ExpressibleByIntegerLiteral {
  init(integerLiteral value: Int) {
    // ...
  }
}

// GOOD:
let k1: Kilometers = 10  

// ALSO GOOD:                        
let rect: CGRect = .zero
var array: [Int] = []
var closure: (Int) -> Void = { _ in }
```

### * .init(...)

Explicitly calling .init(...) is allowed only when the receiver of the call is a metatype variable. In direct calls to the initializer using the literal type name, .init is omitted. (Referring to the initializer directly by using MyType.init syntax to convert it to a closure is permitted.)

```swift 
// GOOD:
let x = MyType(arguments)

let type = lookupType(context)			// returns `MetaType`
let x = type.init(arguments)

// AVOID:
let x = MyType.init(arguments)
let x = CGSize.init(uniform: 10)
let x = makeValue(factory: MyType.init)
```

### Lazy Initialization
Consider using lazy initialization for finer grained control over object lifetime. This is especially true for UIViewController that loads views lazily. You can either use a closure that is immediately called { }() or call a private factory method. Example:

```swift
lazy var locationManager = makeLocationManager()

private func makeLocationManager() -> CLLocationManager {
  let manager = CLLocationManager()
  manager.desiredAccuracy = kCLLocationAccuracyBest
  manager.delegate = self
  manager.requestAlwaysAuthorization()
  return manager
}
```

> Notes:
> - [unowned self] is not required here. A retain cycle is not created.
> - Location manager has a side-effect for popping up UI to ask the user for permission so fine grain control makes sense here.


### Type Annotation for Empty Arrays and Dictionaries

For empty arrays and dictionaries, use type annotation. (For an array or dictionary assigned to a large, multi-line literal, use type annotation.)

**Preferred**:
```swift
var names: [String] = []
var lookup: [String: Int] = [:]
```

**Not Preferred**:
```swift
var names = [String]()
var lookup = [String: Int]()
```

**NOTE**: Following this guideline means picking descriptive names is even more important than before.


### Syntactic Sugar

Prefer the shortcut versions of type declarations over the full generics syntax.

**Preferred**:
```swift
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```

**Not Preferred**:
```swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```

