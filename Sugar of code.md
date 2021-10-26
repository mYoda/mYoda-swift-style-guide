## Sugar of code

* Do & Then

Super sweet syntactic sugar for Swift initializers.

Use `then` to set properties of an Objects and `return` it to a variable.

```swift
// MyLabel.swift

let label = UILabel().then {
  $0.textAlignment = .center
  $0.textColor = .black
  $0.text = "Hello, World!"
}
```

This is equivalent to:

```swift
// MyLabel.swift

let label: UILabel = {
  let label = UILabel()
  label.textAlignment = .center
  label.textColor = .black
  label.text = "Hello, World!"
  return label
}()
```

Use `do` to add multi-changes to the Object's properties:

```swift
// MyLabel.swift
// later in somewhere
label.do {
  $0.textAlignment = .center
  $0.textColor = .black
  $0.text = "Ciao, World!"
}
```
