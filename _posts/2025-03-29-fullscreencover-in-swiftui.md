---
title: fullScreenCover in SwiftUI
layout: post
category: SwiftUI Views
tag: SwiftUI
---

Similar to **Sheet in SwiftUI**, there are two ways to present a **full screen modal view** in SwiftUI as well.

> To learn more about Sheet, take a look at my [“Sheet in SwiftUI”](/posts/sheet-in-swiftui/) post.

## 1. Binding to a Boolean value

In this way, SwiftUI will present a modal view that covers as much of the screen as possible when a binding to a Boolean value that you provide is `true`.

The official method definition is below:

```swift
nonisolated
func fullScreenCover<Content>(
    isPresented: Binding<Bool>,
    onDismiss: (() -> Void)? = nil,
    @ViewBuilder content: @escaping () -> Content
) -> some View where Content : View
```

Let's use an example to illustrate how to use it.

```swift
struct TimelineListView: View {
    @EnvironmentObject var timelineStore: TimelineStore
    @State private var showingSettings = false

    var body: some View {
        NavigationView {
            List(timelineStore.timelines) { timeline in 
                TimelineRow(timeline: timeline)
            }
            .navigationBarTitle("Timelines")
            .toolbar {
                ToolbarItem(placement: .navigationBarLeading) {
                    Button(action: { showingSettings = true }) {
                        Image(systemName: "gear")
                    }
                }
            }
            .fullScreenCover(isPresented: $showingSettings, onDismiss: didDismiss) {
                SettingsView()
            }
        }
    }

    private func didDismiss() {
        // Handle the dismissing action.
    }
}
```

<div align="left">
  <img src="/assets/posts/timeline_list.png" alt="Timeline List" width="260"/>
  <img src="/assets/posts/settings_view_fullscreen.png" alt="Settings View" width="260"/>
</div>

In the above example, SwiftUI will present `SettingsView` when `showingSettings` is `true`.

## 2. Using data source item

Another way to present a full screen modal view is using the given item as a data source for the modal view's content.

The method definition is as follows:

```swift
nonisolated
func fullScreenCover<Item, Content>(
    item: Binding<Item?>,
    onDismiss: (() -> Void)? = nil,
    @ViewBuilder content: @escaping (Item) -> Content
) -> some View where Item : Identifiable, Content : View
```

A simple example:

```swift
struct TimelineDetailView: View {
    let timeline: Timeline
    @State private var showingEventToEdit: Event? = nil

    var body: some View {
        List(timeline.events) { event in 
            EventRow(event: event)
                .contentShape(Rectangle())
                .onTapGesture {
                    showingEventToEdit = event
                }
        }
        .navigationTitle(timeline.title)
        .fullScreenCover(isPresented: $showingEventToEdit) { event in
            EventEditView(timeline: timeline, event: event)
        }
    }
}
```

SwiftUI will present a `EventEditView` when `showingEventToEdit` has value.

## Multiple modal views

Sometimes, we may need to present multiple full screen modal views in the same view. How to achieve this in a concise way? 

We can create an enum like `ModalView` to combine all modal views together.

```swift
struct TimelineDetailView: View {
    enum ModalView: Identifier {
        case createEvent
        case editEvent(Event)

        var id: String {
            switch self {
            case .createEvent: return "createEvent"
            case .editEvent(let event): return "editEvent-\(event.id)"
            }
        }
    }

    let timeline: Timeline
    @State private var showingModalView: ModalView? = nil

    var body: some View {
        List(timeline.events) { event in 
            EventRow(event: event)
                .contentShape(Rectangle())
                .onTapGesture {
                    showingModalView = .editEvent(event)
                }
        }
        .navigationTitle(timeline.title)
        .toolbar {
            ToolbarItem(placement: .navigationBarTrailing) {
                Button {
                    showingModalView = .createEvent
                } label: {
                    Image(systemName: "plus")
                }
            }
        }
        .fullScreenCover(item: $showingModalView) { modalView in
            switch modalView {
            case .createEvent: 
                EventCreateView(timeline: timeline)
            case .editEvent(let event): 
                EventEditView(timeline: timeline, event: event)
            }
        }
    }
}
```

<div align="left">
  <img src="/assets/posts/timeline_detail.png" alt="Timeline Detail" width="260"/>
  <img src="/assets/posts/event_create_view_fullscreen.png" alt="Event Create View" width="260"/>
  <img src="/assets/posts/event_edit_view_fullscreen.png" alt="Event Edit View" width="260"/>
</div>

Using this way, we can simply combine all full screen modal views together without multiple state definitions and fullScreenCover modifiers.

## References

- [fullScreenCover(isPresented:onDismiss:content:)](https://developer.apple.com/documentation/swiftui/view/fullscreencover(ispresented:ondismiss:content:))
- [fullScreenCover(item:onDismiss:content:)](https://developer.apple.com/documentation/swiftui/view/fullscreencover(item:ondismiss:content:))