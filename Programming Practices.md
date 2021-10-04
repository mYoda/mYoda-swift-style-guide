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

### Numeric and String Literals
Integer and string literals in Swift do not have an intrinsic type. For example, 5 by itself is not an Int; it is a special literal value that can express any type that conforms to ExpressibleByIntegerLiteral and only becomes an Int if type inference does not map it to a more specific type. Likewise, the literal "x" is neither String nor Character nor UnicodeScalar, but it can become any of those types depending on its context, falling back to String as a default.

Thus, when a literal is used to initialize a value of a type other than its default, and when that type cannot be inferred otherwise by context, specify the type explicitly in the declaration or use an as expression to coerce it.

Best practice:
```swift
// Without a more explicit type, x1 will be inferred as type Int.
let x1 = 50

// These are explicitly type Int32.
let x2: Int32 = 50
let x3 = 50 as Int32

// Without a more explicit type, y1 will be inferred as type String.
let y1 = "a"

// These are explicitly type Character.
let y2: Character = "a"
let y3 = "a" as Character

// These are explicitly type UnicodeScalar.
let y4: UnicodeScalar = "a"
let y5 = "a" as UnicodeScalar

func writeByte(_ byte: UInt8) {
  // ...
}
// Inference also occurs for function arguments, so 50 is a UInt8 without
// explicitly coercion.
writeByte(50)
```
The compiler will emit errors appropriately for invalid literal coercions if, for example, a number does not fit into the integer type or a multi-character string is coerced to a character. So while the following examples emit errors, they are “good” because the errors are caught at compile-time and for the right reasons.

```swift
// error: integer literal '9223372036854775808' overflows when stored into 'Int64'
let a = 0x8000_0000_0000_0000 as Int64

// error: cannot convert value of type 'String' to type 'Character' in coercion
let b = "ab" as Character
```

Using initializer syntax for these types of coercions can lead to misleading compiler errors, or worse, hard-to-debug runtime errors.

```swift
// This first tries to create an `Int` (signed) from the literal and then
// convert it to a `UInt64`. Even though this literal fits into a `UInt64`, it
// doesn't fit into an `Int` first, so it doesn't compile.
let a1 = UInt64(0x8000_0000_0000_0000)

// This invokes `Character.init(_: String)`, thus creating a `String` "a" at
// runtime (which involves a slow heap allocation), extracting the character
// from it, and then releasing it. This is significantly slower than a proper
// coercion.
let b = Character("a")

// As above, this creates a `String` and then `Character.init(_: String)`
// attempts to extract the single character from it. This fails a precondition
// check and traps at runtime.
let c = Character("ab")
```
