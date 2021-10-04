## Optional Types
> Optional is used to convey a non-error result that is either a value or the absence of a value. For example, when searching a collection for a value, not finding the value is **still a valid and expected** outcome, not an error.

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
