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
