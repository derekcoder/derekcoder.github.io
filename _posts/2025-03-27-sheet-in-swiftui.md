---
title: Sheet in SwiftUI
layout: post
category: SwiftUI Views
tag: SwiftUI
---

There are two ways to present a sheet in SwiftUI.

## 1. Binding to a Boolean value

In this way, SwiftUI will present a sheet when a binding to a Boolean value that you provide is `true`.

The official method definition is below:

```swift
nonisolated
func sheet<Content>(
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
            .sheet(isPresented: $showingSettings, onDismiss: didDismiss) {
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
  <img src="/assets/posts/settings_view.png" alt="Settings View" width="260"/>
</div>

In the above example, SwiftUI will present `SettingsView` when `showingSettings` is `true`.

## 2. Using Data Source Item

Another way to present a sheet is using the given item as a data source for the sheet's content.

The method definition is as follows:

```swift
nonisolated
func sheet<Item, Content>(
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
        .sheet(isPresented: $showingEventToEdit) { event in
            EventEditView(timeline: timeline, event: event)
        }
    }
}
```

SwiftUI will present a `EventEditView` when `showingEventToEdit` has value.

## Multiple Sheets

Sometimes, we may need to present multiple sheets in the same view. How to achieve this in a concise way? 

We can create an enum like `Sheet` to combine all sheet cases together.

```swift
struct TimelineDetailView: View {
    enum Sheet: Identifier {
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
    @State private var showingSheet: Sheet? = nil

    var body: some View {
        List(timeline.events) { event in 
            EventRow(event: event)
                .contentShape(Rectangle())
                .onTapGesture {
                    showingSheet = .editEvent(event)
                }
        }
        .navigationTitle(timeline.title)
        .toolbar {
            ToolbarItem(placement: .navigationBarTrailing) {
                Button {
                    showingSheet = .createEvent
                } label: {
                    Image(systemName: "plus")
                }
            }
        }
        .sheet(item: $showingSheet) { sheet in
            switch sheet {
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
  <img src="/assets/posts/event_create_view.png" alt="Event Create View" width="260"/>
  <img src="/assets/posts/event_edit_view.png" alt="Event Edit View" width="260"/>
</div>

Using this way, we can simply combine all sheets together without multiple state definitions and sheet modifiers.

## References

- [sheet(isPresented:onDismiss:content:)](https://developer.apple.com/documentation/swiftui/view/sheet(ispresented:ondismiss:content:))
- [sheet(item:onDismiss:content:)](https://developer.apple.com/documentation/swiftui/view/sheet(item:ondismiss:content:))