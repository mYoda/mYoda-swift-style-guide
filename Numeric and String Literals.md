
### Numeric and String Literals
Integer and string literals in Swift do not have an intrinsic type. For example, 5 by itself is not an Int; it is a special literal value that can express any type that conforms to ExpressibleByIntegerLiteral and only becomes an Int if type inference does not map it to a more specific type. Likewise, the literal "x" is neither String nor Character nor UnicodeScalar, but it can become any of those types depending on its context, falling back to String as a default.

Thus, when a literal is used to initialize a value of a type other than its default, and when that type cannot be inferred otherwise by context, specify the type explicitly in the declaration or use an as expression to coerce it.

Best practice:
```swift
// Without a more explicit type, x1 will be inferred as type Int.
let x1 = 50

// These are explicitly type Int32.
let x2: Int32 = 50

// Without a more explicit type, y1 will be inferred as type String.
let y1 = "a"

// These are explicitly type Character.
let y2: Character = "a"

// These are explicitly type UnicodeScalar.
let y4: UnicodeScalar = "a"

func writeByte(_ byte: UInt8) {
  // ...
}
// Inference also occurs for function arguments, so 50 is a UInt8 without
// explicitly coercion.
writeByte(50)
```
<br>
<br>

The compiler will emit errors appropriately for invalid literal coercions if, for example, a number does not fit into the integer type or a multi-character string is coerced to a character. So while the following examples emit errors, they are “good” because the errors are caught at compile-time and for the right reasons.

**Preffered:**

```swift
// error: integer literal '9223372036854775808' overflows when stored into 'Int64'
let a: Int64 = 0x8000_0000_0000_0000 

// error: cannot convert value of type 'String' to type 'Character' in coercion
let b: Character = "ab"
```

Using initializer syntax for these types of coercions can lead to misleading compiler errors, or worse, hard-to-debug runtime errors.

**Not Preffered:**
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
