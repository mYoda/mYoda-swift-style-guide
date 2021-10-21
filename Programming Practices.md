## Programming Practices
Common themes among the rules in this section are: avoid redundancy, avoid ambiguity, and prefer implicitness over explicitness unless being explicit improves readability and/or reduces ambiguity.

### Compiler Warnings
Code should compile ***without** warnings* when feasible. Any warnings that are able to be removed easily by the author must be removed.

A reasonable exception is deprecation warnings, where it may not be possible to immediately migrate to the replacement API, or where an API may be deprecated for external users but must still be supported inside a library during a deprecation period.

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
---

### for-where Loops
When the entirety of a for loop’s body would be a single if block testing a condition of the element, the test is placed in the where clause of the for statement instead.

Preffered:
```swift
for item in collection where item.hasProperty {
  // ...
}
```

Not Preffered:
```swift
for item in collection {
  if item.hasProperty {
    // ...
  }
}
```


---

*  Prefer `let` to `var` whenever possible.

* Prefer the composition of `map`, `filter`, `reduce`, etc. over iterating when transforming from one collection to another. Make sure to avoid using closures that have side effects when using these methods.

```swift
// GOOD
let stringOfInts = [1, 2, 3].flatMap { String($0) }
// ["1", "2", "3"]

// AVOID
var stringOfInts: [String] = []
for integer in [1, 2, 3] {
    stringOfInts.append(String(integer))
}

// GOOD
let evenNumbers = [4, 8, 15, 16, 23, 42].filter { $0 % 2 == 0 }
// [4, 8, 16, 42]

// AVOID
var evenNumbers: [Int] = []
for integer in [4, 8, 15, 16, 23, 42] {
    if integer % 2 == 0 {
        evenNumbers.append(integer)
    }
}
```
  
>*  **Omit the `return` keyword when not required by the language.** 
  
```swift
// WRONG
  ["1", "2", "3"].compactMap { return Int($0) }

// RIGHT
  ["1", "2", "3"].compactMap { Int($0) }
```

* If a function `returns multiple` values, prefer returning a `tuple` to using `inout` arguments (it’s best to use labeled tuples for clarity on what you’re returning if it is not otherwise obvious). If you use a certain tuple more than once, consider using a `typealias`. If you’re returning 3 or more items in a tuple, consider using a `struct` or `class` instead.

```swift
func pirateName() -> (firstName: String, lastName: String) {
    return ("Guybrush", "Threepwood")
}

let name = pirateName()
let firstName = name.firstName
let lastName = name.lastName
```

* Be wary of retain cycles when creating delegates/protocols for your classes; typically, these properties should be declared `weak`.

> Be careful when calling `self` directly from an escaping closure as this can cause a retain cycle - use a [capture list](https://developer.apple.com/library/ios/documentation/swift/conceptual/Swift_Programming_Language/Closures.html#//apple_ref/doc/uid/TP40014097-CH11-XID_163) when this might be the case:

```swift
myFunctionWithEscapingClosure() { [weak self] (error) -> Void in

    // you can do this if one line only
    self?.doSomething()

    // or you can do this

    guard let self = self else {
        return
    }
    
    self.doSomething()
    self.doAnother()
	...
}
```

* Don't place parentheses around control flow predicates.

```swift
// PREFERRED
if x == y {
    /* ... */
}

// NOT PREFERRED
if (x == y) {
    /* ... */
}
```

* Avoid writing out an `enum` type where possible - use shorthand.

```swift
// PREFERRED
imageView.setImageWithURL(url, type: .person)
imageView.backgroundColor = .white

// NOT PREFERRED
imageView.setImageWithURL(url, type: AsyncImageView.Type.person)
imageView.backgroundColor = UIColor.white
```

* Prefer not writing `self.` unless it is required.

* If you have a function that takes no arguments, has no side effects, and returns some object or value, prefer using a computed property instead.

```swift

var items: [String] = []

// AVOID
private func isNeedToDoSomething() -> Bool {

    return items.count > 0
}

// GOOD
var isNeedToDoSomething: Bool {
    items.count > 0
}
```
