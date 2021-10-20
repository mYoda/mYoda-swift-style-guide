## Naming


Descriptive and consistent naming makes software easier to read and understand. Use the Swift naming conventions described in the [API Design Guidelines](https://swift.org/documentation/api-design-guidelines/). Some key takeaways include:

- striving for clarity at the call site
- prioritizing clarity over brevity
- using `camelCase` (not `snake_case`)
- using `UpperCamelCase` for types and protocols, `lowerCamelCase` for everything else
- using `UpperCamelCase` for Folders and FileNames in Project's structure or for Folders in Assets
- including all needed words while omitting needless words

```swift
extension List {
  public mutating func remove(at position: Index) -> Element
}
// GOOD:
employees.remove(at: x)

// AVOID:
employees.remove(x) // unclear: are we removing x?
```
---
```swift
public mutating func remove(_ member: Element) -> Element?

// GOOD:
allViews.remove(cancelButton) // clear

// AVOID:
allViews.removeElement(cancelButton)
```
---  
### using names based on roles, not types. 

**Not Preferred**:
```swift
var string = "Hello"

protocol ViewController {
  associatedtype ViewType : View
}
class ProductionLine {
  func restock(from widgetFactory: WidgetFactory)
}
```
**Preferred**:
```swift
var greeting = "Hello"

protocol ViewController {
  associatedtype ContentView : View
}
class ProductionLine {
  func restock(from supplier: WidgetFactory)
}
```

**Name booleans like `isSpaceship`, `hasSpacesuit`, etc.** This makes it clear that they are booleans and not other types.
---  

- Prefer method and function names that make use sites form grammatical English phrases.

```swift
// GOOD:
x.insert(y, at: z)          “x, insert y at z”
x.subViews(havingColor: y)  “x's subviews having color y”
x.capitalizingNouns()       “x, capitalizing nouns”

// AVOID:
x.insert(y, position: z)
x.subViews(color: y)
x.nounCapitalize()
```
- naming methods for their side effects
  - verb methods follow the -ed, -ing rule for the non-mutating version

| Mutating  | Nonmutating  |
|:----------|:----------|
| x.sort()    | z = x.sorted()    |
| x.append(y)    | z = x.appending(y)    |


  - noun methods follow the formX rule for the mutating version
  - boolean types should read like assertions
  - protocols that describe _what something is_ should read as nouns
  - protocols that describe _a capability_ should end in _-able_ or _-ible_
- using terms that don't surprise experts or confuse beginners
- generally avoiding abbreviations
- using precedent for names
- preferring methods and properties to free functions
- casing acronyms and initialisms uniformly up or down
- giving the same base name to methods that share the same meaning
- avoiding overloads on return type
- choosing good parameter names that serve as documentation
- preferring to name the first parameter instead of including its name in the method name, except as mentioned under Delegates
- labeling closure and tuple parameters
- taking advantage of default parameters
---
**Global Constants**

Like other variables, global constants are lowerCamelCase. Hungarian notation, such as a 
leading g or k, is not used. 
```swift 
// GOOD:
let secondsPerMinute = 60

// AVOID:
let SecondsPerMinute = 60
let kSecondsPerMinute = 60
let gSecondsPerMinute = 60
let SECONDS_PER_MINUTE = 60
```

**Acronyms in names (e.g. `URL`, `ID`) should be all-caps except when it’s the start of a name that would otherwise be *lowerCamelCase*, in which case it should be uniformly lower-cased.**

  ```swift
  // WRONG
  class UrlValidator {

    func isValidUrl(_ URL: URL) -> Bool {
      // ...
    }

    func isProfileUrl(_ URL: URL, for userID: String) -> Bool {
      // ...
    }
  }

  let URLValidator = UrlValidator()
  let isProfile = URLValidator.isProfileUrl(URLToTest, userId: IDOfUser)

  // RIGHT
  class URLValidator {

    func isValidURL(_ url: URL) -> Bool {
      // ...
    }

    func isProfileURL(_ url: URL, for userId: String) -> Bool {
      // ...
    }
  }
```

**Names should be written with their most general part first and their most specific part last.** The meaning of "most general" depends on context, but should roughly mean "that which most helps you narrow down your search for the item you're looking for." Most importantly, be consistent with how you order the parts of your name.


  ```swift
  // WRONG
  let rightTitleMargin: CGFloat
  let leftTitleMargin: CGFloat
  let bodyRightMargin: CGFloat
  let bodyLeftMargin: CGFloat

  // RIGHT
  let titleMarginRight: CGFloat
  let titleMarginLeft: CGFloat
  let bodyMarginRight: CGFloat
  let bodyMarginLeft: CGFloat

  let urlValidator = URLValidator()
  let isProfile = urlValidator.isProfileURL(urlToTest, userId: idOfUser)
  ```

**Include a hint about type in a name of a UI elements or objects if it would otherwise be ambiguous.**

 ```swift
  // WRONG
  let text: UITextField
  let cancel: UIButton

  // RIGHT
  let textField: UITextField
  let cancelButton: UIButton
  ```
**Avoid Objective-C-style acronym prefixes.** This is no longer needed to avoid naming conflicts in Swift.

 ```swift
  // WRONG
  class AIRAccount {
    // ...
  }

  // RIGHT
  class Account {
    // ...
  }
  ```
  
  
