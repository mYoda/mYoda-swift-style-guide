## Classes and Structures
<br>

* **Default classes to `final`.**

If a class needs to be overridden, the author should opt into that functionality by omitting the `final` keyword.

  ```swift
  // AVOID
  class SettingsRepository {
    // ...
  }

  // GOOD
  final class SettingsRepository {
    // ...
  }
  ```
<br>
<br>

* **Prefer structs over classes**

Unless you require functionality that can only be provided by a class (like identity or deinitializers), implement a struct instead.

Note that inheritance is (by itself) usually _not_ a good reason to use classes, because polymorphism can be provided by protocols, and implementation reuse can be provided through composition.

For example, this class hierarchy:

```swift
class Vehicle {
    let numberOfWheels: Int

    init(numberOfWheels: Int) {
        self.numberOfWheels = numberOfWheels
    }

    func maximumTotalTirePressure(pressurePerWheel: Float) -> Float {
        return pressurePerWheel * Float(numberOfWheels)
    }
}

class Bicycle: Vehicle {
    init() {
        super.init(numberOfWheels: 2)
    }
}

class Car: Vehicle {
    init() {
        super.init(numberOfWheels: 4)
    }
}
 ```

could be refactored into these definitions:

```swift
protocol Vehicle {
    var numberOfWheels: Int { get }
}

func maximumTotalTirePressure(vehicle: Vehicle, pressurePerWheel: Float) -> Float {
    return pressurePerWheel * Float(vehicle.numberOfWheels)
}

struct Bicycle: Vehicle {
    let numberOfWheels = 2
}

struct Car: Vehicle {
    let numberOfWheels = 4
}
```

> Value types are simpler, easier to reason about, and behave as expected with the `let` keyword.
<br>
<br>

* **Default type methods to `static`.**


If a method needs to be overridden, the author should opt into that functionality by using the `class` keyword instead.

  ```swift
  // AVOID
  class Fruit {
    class func eatFruits(_ fruits: [Fruit]) { ... }
  }

  // GOOD
  class Fruit {
    static func eatFruits(_ fruits: [Fruit]) { ... }
  }
  ```
