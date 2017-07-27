# -- work in progress --

# Swift Guidelines

This guideline attempts to stir you in what I believe is the right direction for writing code in Swift.
We'll talk about how to style the code and what patterns to use.

## Table of Contents

1. [Spacing and indentation](#spacing-and-indentation)
1. [Patterns](#patterns)

## Spacing and indentation

  - [1]() Tab size
    
    Always use `spaces` instead of `tabs` and use `4` spaces as indentation level.
    
    > Reason:
    > - `spaces` instead of `tabs` makes it consistent across different editors.
    > - `2` spaces is too small and makes it hard to see where a different indentation level begins.
    > - Anything bigger than `4` spaces is not necessary and will just make the code harder to read due to it going to the right side of the screen.
    
  - [2]() File
    
    Leave an empty line in the beginning and end of file.
    
    > Reason: easier to read.
    
  - [3]() `class`, `struct`, `protocol`, `enum`
    
    Leave an empty line in the beginning and end of a class/struct/protocol/enum declaration.
    
    > Reason: to separate code that is not directly linked together (like within a function).
    
    ```swift
    // bad
    struct SomeStruct {
        func someFunction() {
            // ...
        }
    }
    
    // good
    struct SomeStruct {
        
        func someFunction() {
            // …
        }
        
    }
    ```
    
  - [4]() Privates
    
    Leave `private` variables and functions at the end of the `class / struct / ...` declaration. Also add the `MARK: - Private`.
    
    > Reason: when someone is looking at the file to use the implemented `class`, they will start at the beginning of the file (obviously), so they'll only have to scroll down until the public functions and parameters end.
    
    ```swift
    // bad
    struct SomeStruct {
        
        let publicVariable: Int
        private let privateVariable: Int
        
        func publicFunction() {}
        
        private func privateFunction() {}
        
    }
    
    // good
    struct SomeStruct {
        
        let publicVariable: Int
        
        func publicFunction() {}
        
        // MARK: - Private
        
        private let privateVariable: Int
        
        private func privateFunction() {}
        
    }
    ```
    
  - [5]() Function
    
    Do not leave an empty line in the beginning and end of a function.
    
    > Reason: to differentiate from the separation between `class/struct` and `function`.
    
    ```swift
    func someFunction() -> Int {
        let x = 2
        // …
        return y
    }
    ```
    
  - [6]() `if`, `switch`
    
    Just like a function, there should be no empty line in the beginning or end of declaration.
    
    > Reason: the code is as linked together as functions.
    
    ```swift
    // bad
    if condition {
        
        let x = 2
        // ...
        
    }
    
    // good
    if condition {
        let x = 2
        // ...
    }
    
    // good
    switch value {
    case .v1:
        // ...
    case .v2:
        // ...
    default:
        // ...
    }
    ```
    
  - [7]() Function parameter alignment
    
    If you end the line opening something, start a separate line with the same indentation closing that.
    
    > Reason:
    > - Aligning a second parameter with the first that is on the same line as the opening parentheses makes the indentation dependent on the function name.
    > - Not closing parentheses on a separate line makes it harder to see where the declaration ends.
    
    ```swift
    // bad
    func myFunction(x: Int, y: Int
    ) { ... }
    
    // bad
    func myFunction(x: Int,
                    y: Int) { ... }

    // good
    func myFunction(x: Int, y: Int) { ... }
    
    // good
    func myFunction(
        x: Int,
        y: Int
    ) { ... }
    ```
    
  - [8]() Functions, `if/else/...` brackets
    
    Don't leave the opening bracket on a separate line.
    
    > Reason: easier to see what the opening block connects to.
    
    ```swift
    // bad
    if condition
    {
        // ...
    }
    else
    {
        // ...
    }
    
    // good
    if condition {
        // ...
    } else {
        // ...
    }
    ```
    
  - [9]() Indenting Arrays and Dictionaries
    
    Similar to function parameters.
    
    > Reason: same as function parameters.
    
    ```swift
    // bad
    let array = [1,
                 2,
                 3]
    
    // good
    let array = [
        1,
        2,
        3
    ]
    
    // good
    let dictionary = [
        "1": 1,
        "2": 2,
        "3"
    ]
    ```
    
  - [10]() `self`
    
    Use `self` only when necessary, inside closures.
    
    > Reason: to easily distinguish between variables that are being held by a closure and the one that are just being used at the moment.
    
    ```swift
    // bad
    class SomeClass {
        
        let x = 2
        
        func someFunction(i: Int) -> Int {
            return i + self.x
        }
        
    }
    
    // good
    class SomeClass {
        
        let x = 2
        
        func someFunction(i: Int) -> Int {
            return i + x
        }
        
        func otherFunction() -> [Int] {
            return [1, 2, 3].map({ i in
                return i + self.x
            })
        }
        
    }
    ```

## Patterns

  - [1]() Don't use delegates
    
    Never ever use delegates! It's that simple. Use callbacks instead and Observables / Promises to avoid callback hell.
    
    > Reason:
    > - The delegate pattern is hard to follow, when making a web request call you also have to figure out where the delegate is being set and then find where the protocol is being implemented.
    > - In other words, with delegates you keep jumping files / classes to understand the path of the code, while with lambdas it becomes more of a tree structured code, so you either go up or down the tree.
    > - Delegates make you separate the code that is calling from the result.
    > - Delegates make it unreasonable when you need more than one.
    
    ```swift
    // bad
    class SomeViewController: UIViewController {
        
        @IBOutlet weak var tableView: UITableView!
        
        override func viewDidLoad() {
            super.viewDidLoad()
            
            tableView.dataSource = self
            tableView.delegate = self
            
            API.requestItems(callback: { items in
                self.items = items
            })
        }
        
        // MARK: - Private
        
        private var items = [[String]]
        
    }
    
    extension SomeViewController: UITableViewDelegate {
        
        override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
            let item = items[indexPath.section][indexPath.row]
            // ...
        }
        
    }
    
    extension SomeViewController: UITableViewDataSource {
        
        override func numberOfSections(in tableView: UITableView) -> Int {
            return items.count
        }
        
        override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
            return items[section].count
        }
        
        override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
            let cell = tableView.dequeueReusableCell(withIdentifier: "reuseIdentifier", for: indexPath)
            let item = items[indexPath.section][indexPath.row]
            // Configure the cell...
            return cell
        }
        
    }
    
    // good
    class SomeViewController: UIViewController {
        
        @IBOutlet weak var tableView: UITableView!
        
        override func viewDidLoad() {
            super.viewDidLoad()
            
            API.requestItems(callback: { items in
                self.tableView.didSelectRowAt = { indexPath in
                    let item = items[indexPath.section][indexPath.row]
                    // ...
                }
                
                self.tableView.numberOfSections = items.count
                self.tableView.numberOfRowsInSection = { section in
                    return items[section].count
                }
                self.tableView.cellForRowAt = { indexPath in
                    let cell = tableView.dequeueReusableCell(withIdentifier: "reuseIdentifier", for: indexPath)
                    let item = items[indexPath.section][indexPath.row]
                    // Configure the cell...
                    return cell
                }
            })
        }
        
    }
    
    // best
    class SomeViewController: UIViewController {
        
        @IBOutlet weak var tableView: UITableView!
        
        override func viewDidLoad() {
            super.viewDidLoad()
            
            API.requestItems(callback: { items in
                self.tableView.didSelectRow = { item in
                    // ...
                }
                
                self.tableView.data = items // accepts [[T]]
                
                self.tableView.cellForRow = { item in
                    let cell = tableView.dequeueReusableCell(withIdentifier: "reuseIdentifier", for: indexPath)
                    // Configure the cell...
                    return cell
                }
            })
        }
        
    }
    ```
    
  - [2]() Don't use inheritance
    
    Always favor composition over inheritance.
    
    > Reason: Inheritance makes it harder to understand the code, because it forces you to know implementation details of the super class (breaking encapsulation), which, in turn, forces you to coordinate both implementations (current and super class).
    
  - [3]() Don't use `throw`'s / `exception`'s
    
    This is even worse than delegates, never use exceptions. Instead, return a Result monad with the possible errors.
    
    > Reason: Looking at a function signature should be enough for anyone to understand what it does, and never should you be forced to understand it's implementation. Throwing an exception forces you to look which exceptions can be thrown and when in the middle of the code (or forces you to depend on documentation, which might not always be up-to-date).
    
  - [4]() Don't use `for` or `while` loops
    
    Imperative programming is dead (or should be), forward we go with Declarative programming and FP.
    
    > Reason: study FP and it will become obvious, but the short version is, it's harder to follow what's going on when you use `for` and `while` loops instead of `map`, `filter`, `reduce`, etc.
    
  - [5]() Use `RxSwift` (or some kind of `Promise`)
    
    Whenever a future value is needed, `Promise`s are the way to go. One could go with `PromiseKit`, but the syntax for `then` is not very intuitive, since it can act as a `map` and as `forEach`. So Observables are a better, and also more flexible, alternative.
    A single value `Observable` becomes a `Promise` and if you need a stream, you already have the framework at hand.
    
    > Reason: replaces the delegate pattern with a more declarative way of coding.

  - [6]() `let` vs `var`
    
    Always use `let` instead of `var` when you can.
    
    > Reason: `var` means the variable is mutable and we know from FP that mutable is bad.
    
  - [7]() `class` vs `struct`
    
    Always prefer `struct` over `class`. Only use `class` if the framework demands it.
    
    > Reason: value semantics is much easier to understand than reference, therefore, less prone to bugs. For better understanding, study Functional Programming.
    
  - [8]() Early returns
    
    Always prefer early returns rather than nested `if`s.
    
    > Reason: easier to follow the happy path and reduces cyclomatic complexity.
    
    ```swift
    // bad
    func someFunction(optionalBooleanVariable: Bool?) {
        if let booleanVariable = optionalBooleanVariable {
            if booleanVariable {
                // ...
            }
        }
    }
    
    // good
    func someFunction(optionalBooleanVariable: Bool?) {
        guard let booleanVariable = optionalBooleanVariable  else { return }
        
        guard booleanVariable else { return }
        
        // ...
    }
    ```
