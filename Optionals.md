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

* The only time you should be using implicitly unwrapped optionals is with `@IBOutlet`s. In every other case, it is better to use a non-optional or regular optional property. Yes, there are cases in which you can probably "guarantee" that the property will never be `nil` when used, but it is better to be safe and consistent. Similarly, don't use force unwraps.

* Don't use `as!` or `try!`.

* If you have an identifier `foo` of type `FooType?` or `FooType!`, don't force-unwrap it to get to the underlying value (`foo!`) if possible.

```swift
// DON'T DO THIS:

if let some = optionals {
    optionals!.doSomething()
}

// OR THIS:

if let url = URL(string: data.url) {
    model.doSomething(with: URL(string: data.url)!)
}

```


Instead, prefer this:

```swift
if let foo = foo {
    // Use unwrapped `foo` value in here
    foo.doSomething()

} else {
    // If appropriate, handle the case where the optional is nil
}
```

```swift
guard let url = URL(string: data.url) else { return }
    model.doSomething(with: url)
}
```

Alternatively, you might want to use Swift's Optional Chaining in some of these cases, such as:

```swift
// Call the function if `foo` is not nil. If `foo` is nil, ignore we ever tried to make the call
foo?.callSomethingIfFooIsNotNil()
```

* If you don't plan on actually using the value stored in an optional, but need to determine whether or not this value is `nil`, explicitly check this value against `nil` as opposed to using `if let` syntax.

```swift
// PREFERRED
if someOptional != nil {
    // do something
}

// NOT PREFERRED
if let _ = someOptional {
    // do something
}
```
* When unwrapping optionals, use the same name for the unwrapped constant or variable where appropriate.

```swift
guard let myValue = myValue else {
    return
}
```
