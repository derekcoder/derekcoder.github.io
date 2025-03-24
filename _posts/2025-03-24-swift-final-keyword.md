---
title: The final keyword in Swift
layout: post
category: Swift Language Features
tag: Swift
---

In Swift, we have a `final` keyword, which can prevent a **class**, **method**, **property**, or **subscript** from being overridden.

For example, if we want to prevent a **class** from being overridden. We can do like this:

```swift
final class BankAccount {
    let accountNumber: String
    private(set) var balance: Double

    init(accountNumber: String, initialBalance: Double) {
        self.accountNumber = accountNumber
        self.balance = initialBalance
    }

    func deposit(amount: Double) {
        balance += amount
    }

    func withdraw(amount: Double) {
        if amount <= balance {
            balance -= amount
        } else {
            print("Insufficient account balance")
        }
    }
}

class SavingAccount: BankAccount { } // Compile-time error
```

We can also only restrict the specific **methods**, **properties**, or **subscripts** from being overridden.

```swift
class ColorPalette {
    // A final method that returns the hexadecimal color code for a given color name.
    final func hexColor(for name: String) -> String {
        let colorDict = [
            "red": "#FF0000",
            "green": "#00FF00",
            "blue": "#0000FF",
        ]
        return colorDict[name] ?? defaultColor
    }

    // A final computed property that returns the default color (white).
    final var defaultColor: String {
        return "#FFFFFF"
    }

    // A final subscript that returns a color name from a predefined list based on the index.
    final subscript(index: Int) -> String {
        let colors = ["red", "green", "blue", "yellow", "purple"]
        if index >= 0 && index < colors.count {
            return colors[index]
        } else {
            return "unknown"
        }
    }

    // A non-final computed property that can be overriden.
    var favoriteColor: String {
        return "red"
    }
}

let palette = ColorPalette()
print("Hex for red: \(palette.hexColor(for: "red"))")  // Expected Output: "#FF0000"
print("Default color: \(palette.defaultColor)")  // Expected Output: "#FFFFFF"
print("Color at index 2: \(palette[2])")  // Expected Output: "blue"
print("Color at index 5: \(palette[5])")  // Expected Output: "unknown"

class CustomColorPalette: ColorPalette {
    override func hexColor(for name: String) -> String { ... } // Compile-time error
    override var defaultColor: String { ... } // Compile-time error
    override subscript(index: Int) -> String { ... } // Compile-time error

    // Okay
    override var favoriteColor: String {
        return "green"
    }
}
```

Methods, properties, or subscripts that you add to a **class extension** can also be marked as `final` to prevent from being overridden.

```swift
class Vehicle {
    let make: String
    let model: String

    init(make: String, model: String) {
        self.make = make
        self.model = model
    }
}

extension Vehicle {
    @objc var fullDescription: String {
        "\(make) \(model)"
    }

    @objc final func honk() {
        print("Honk! Honk! - \(fullDescription)")
    }
}

class Car: Vehicle {
    // Okay
    override var fullDescription: String {
        return "Car: \(make) \(model)"
    }

    // Compile-time error
    override func honk() {
        print("Beep beep!")
    }
}
```

## References
- [Swift Programming Language: Inheritance](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/inheritance#Preventing-Overrides)
- [Which classes should be declared as final?](https://www.hackingwithswift.com/quick-start/understanding-swift/which-classes-should-be-declared-as-final)
- [The ‘final’ keyword](https://www.swiftbysundell.com/tips/the-final-keyword/)