## Initializers

For clarity, initializer arguments that correspond directly to a stored property have the same name as the property. Explicit self. is used during assignment to disambiguate them.

**Preferred**:
```swift
public struct Person {
  public let name: String
  public let phoneNumber: String

  // GOOD.
  public init(name: String, phoneNumber: String) {
    self.name = name
    self.phoneNumber = phoneNumber
  }
}
```

**Not Preferred**:
```swift 
public struct Person {
  public let name: String
  public let phoneNumber: String

  // AVOID.
  public init(name otherName: String, phoneNumber otherPhoneNumber: String) {
    name = otherName
    phoneNumber = otherPhoneNumber
  }
}
```
