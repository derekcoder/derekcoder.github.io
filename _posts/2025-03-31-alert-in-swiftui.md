---
title: Alert in SwiftUI
layout: post
category: SwiftUI Views
tag: SwiftUI
---

There are several ways to present an alert in SwiftUI. Let's go through them one by one.

## 1. Alert with Title only

SwiftUI will present an alert when a given condition is `true`, using a text view for the title.

The method definition is below:

```swift
nonisolated
func alert<A>(
    _ title: Text,
    isPresented: Binding<Bool>,
    @ViewBuilder actions: () -> A
) -> some View where A : View

nonisolated
func alert<A>(
    _ titleKey: LocalizedStringKey,
    isPresented: Binding<Bool>,
    @ViewBuilder actions: () -> A
) -> some View where A : View

nonisolated
func alert<S, A>(
    _ title: S,
    isPresented: Binding<Bool>,
    @ViewBuilder actions: () -> A
) -> some View where S : StringProtocol, A : View
```

This is a simplest form for an alert. Let's see an example:

```swift
struct LoginView: View {
    @EnvironmentObject var authStore: AuthStore

    @State private var username = ""
    @State private var password = ""
    @State private var showingErrorAlert = false

    var body: some View {
        VStack {
            // ...

            Button("Log in", action: login)
        }
        .alert("Login failed.", isPresented: $showingErrorAlert) {
            Button("OK", role: .cancel) { }
        }
    }

    private func login() {
        Task {
            do {
                try await authStore.login(username: username, password: password)
            } catch  {
                showingErrorAlert = true
            }
        }
    }
}
```

In the above example, SwiftUI will present an error alert when `showingErrorAlert` is `true`.

<div align="left">
  <img src="/assets/posts/alert_1.png" alt="Alert" width="260"/>
</div>

## 2. Alert with Title and Message

You can also present an alert with a message when a given condition is `true`.

The method definition is as follows:

```swift
nonisolated
func alert<A, M>(
    _ title: Text,
    isPresented: Binding<Bool>,
    @ViewBuilder actions: () -> A,
    @ViewBuilder message: () -> M
) -> some View where A : View, M : View

nonisolated
func alert<A, M>(
    _ titleKey: LocalizedStringKey,
    isPresented: Binding<Bool>,
    @ViewBuilder actions: () -> A,
    @ViewBuilder message: () -> M
) -> some View where A : View, M : View

nonisolated
func alert<S, A, M>(
    _ title: S,
    isPresented: Binding<Bool>,
    @ViewBuilder actions: () -> A,
    @ViewBuilder message: () -> M
) -> some View where S : StringProtocol, A : View, M : View
```

A simple code example:

```swift
struct LoginView: View {
    @EnvironmentObject var authStore: AuthStore

    @State private var username = ""
    @State private var password = ""
    @State private var errorMessage = ""
    @State private var showingErrorAlert = false

    var body: some View {
        VStack {
            // ...

            Button("Log in", action: login)
        }
        .alert("Error", isPresented: $showingErrorAlert) {
            Button("OK", role: .cancel) {}
        } message: {
            Text(errorMessage)
        }
    }

    private func login() {
        Task {
            do {
                try await authStore.login(username: username, password: password)
            } catch  {
                errorMessage = error.localizedDescription
                showingErrorAlert = true
            }
        }
    }
}
```

SwiftUI will present an alert with message when `showingErrorAlert` is `true`.

<div align="left">
  <img src="/assets/posts/alert_2.png" alt="Alert" width="260"/>
</div>

## 3. Alert using Data

We are able to present an alert using the given data to produce the alert's actions.

```swift
nonisolated
func alert<A, T>(
    _ title: Text,
    isPresented: Binding<Bool>,
    presenting data: T?,
    @ViewBuilder actions: (T) -> A
) -> some View where A : View

nonisolated
func alert<A, T>(
    _ titleKey: LocalizedStringKey,
    isPresented: Binding<Bool>,
    presenting data: T?,
    @ViewBuilder actions: (T) -> A
) -> some View where A : View

nonisolated
func alert<S, A, T>(
    _ title: S,
    isPresented: Binding<Bool>,
    presenting data: T?,
    @ViewBuilder actions: (T) -> A
) -> some View where S : StringProtocol, A : View
```

For example:

```swift
struct LoginView: View {
    @EnvironmentObject var authStore: AuthStore

    @State private var username: String = ""
    @State private var password: String = ""
    @State private var error: Error? = nil
    @State private var showingErrorAlert = false

    var body: some View {
        VStack {
            // ...

            Button("Log in", action: login)
        }
        .alert("Login failed.", isPresented: $showingErrorAlert, presenting: error) { error in
            if error.needRetry {
                Button("Retry") { // ... }
            }
            Button("OK", role: .cancel) {}
        }
    }

    private func login() {
        Task {
            do {
                try await authStore.login(username: username, password: password)
            } catch  {
                self.error = error
                showingErrorAlert = true
            }
        }
    }
}
```

SwiftUI will present the alert when `showingErrorAlert` is `true` and call `actions` ViewBuilder when `error` is not `nil`. After the alert is dismissed, the `showingErrorAlert` will be set to `false`, however, `error` will remain unchanged.

<div align="left">
  <img src="/assets/posts/alert_3.png" alt="Alert" width="260"/>
</div>

## 4. Alert with Title and Message using Data

We are able to present an alert with title and message using the given data to produce the alert's actions and message as well.

```swift
nonisolated
func alert<A, M, T>(
    _ title: Text,
    isPresented: Binding<Bool>,
    presenting data: T?,
    @ViewBuilder actions: (T) -> A,
    @ViewBuilder message: (T) -> M
) -> some View where A : View, M : View

nonisolated
func alert<A, M, T>(
    _ titleKey: LocalizedStringKey,
    isPresented: Binding<Bool>,
    presenting data: T?,
    @ViewBuilder actions: (T) -> A,
    @ViewBuilder message: (T) -> M
) -> some View where A : View, M : View

nonisolated
func alert<S, A, M, T>(
    _ title: S,
    isPresented: Binding<Bool>,
    presenting data: T?,
    @ViewBuilder actions: (T) -> A,
    @ViewBuilder message: (T) -> M
) -> some View where S : StringProtocol, A : View, M : View
```

The code example:

```swift
struct LoginView: View {
    @EnvironmentObject var authStore: AuthStore

    @State private var username: String = ""
    @State private var password: String = ""
    @State private var error: Error? = nil
    @State private var showingErrorAlert = false

    var body: some View {
        VStack {
            // ...

            Button("Log in", action: login)
        }
        .alert("Error", isPresented: $showingErrorAlert, presenting: error) { error in
            if error.needRetry {
                Button("Retry") { // ... }
            }
            Button("OK", role: .cancel) {}
        } message: { error in 
            Text(error.localizedDescription)
        }
    }

    private func login() {
        Task {
            do {
                try await authStore.login(username: username, password: password)
            } catch  {
                self.error = error
                showingErrorAlert = true
            }
        }
    }
}
```

<div align="left">
  <img src="/assets/posts/alert_4.png" alt="Alert" width="260"/>
</div>

## References

- <a href="{{ 'https://developer.apple.com/documentation/swiftui/view/alert(_:ispresented:actions:)'}}">alert(_:isPresented:actions:)</a>
- <a href="{{ 'https://developer.apple.com/documentation/swiftui/view/alert(_:ispresented:actions:message:)'}}">alert(_:isPresented:actions:message:)</a>
- <a href="{{ 'https://developer.apple.com/documentation/swiftui/view/alert(_:ispresented:presenting:actions:)'}}">alert(_:isPresented:presenting:actions:)</a>
- <a href="{{ 'https://developer.apple.com/documentation/swiftui/view/alert(_:ispresented:presenting:actions:message:)'}}">alert(_:isPresented:presenting:actions:message:)</a>