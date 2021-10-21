
### Access Modifiers

* Write the access modifier keyword first if it is needed.

```swift
// PREFERRED
private static let myPrivateNumber: Int

// NOT PREFERRED
static private let myPrivateNumber: Int
```

* The access modifier keyword should not be on a line by itself - keep it inline with what it is describing.

```swift
// PREFERRED
open class Pirate {
    /* ... */
}

// NOT PREFERRED
open
class Pirate {
    /* ... */
}
```

* In general, do not write the `internal` access modifier keyword since it is the `by default`.


* Prefer `private` to `fileprivate` where possible.

* When choosing between `public` and `open`, prefer `open` if you intend for something to be subclassable outside of a given module and `public` otherwise. Note that anything `internal` and above can be subclassed in tests by using `@testable import`, so this shouldn't be a reason to use `open`. In general, lean towards being a bit more liberal with using `open` when it comes to libraries, but a bit more conservative when it comes to modules in a codebase such as an app where it is easy to change things in multiple modules simultaneously.
