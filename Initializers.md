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

Explicitly calling .init(...) is allowed only when the receiver of the call is a metatype variable. In direct calls to the initializer using the literal type name, .init is omitted. (Referring to the initializer directly by using MyType.init syntax to convert it to a closure is permitted.)

```swift 
// GOOD:
let x = MyType(arguments)

let type = lookupType(context)			// returns Type
let x = type.init(arguments)

// AVOID:
let x = makeValue(factory: MyType.init)
let x = MyType.init(arguments)
```
## Properties
The get block for a read-only computed property is omitted and its body is directly nested inside the property declaration.

```swift 
// GOOD:
var totalCost: Int {
  return items.sum { $0.cost }
}

// AVOID:
var totalCost: Int {
  get {
    return items.sum { $0.cost }
  }
}
```
