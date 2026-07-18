# Discussion Notes – Lesson 12

## Key Discussion 1 – IoC Beyond Dependency Injection

Dependency Injection is a technique.

Inversion of Control is the broader architectural principle.

IoC exists whenever one component gives up control over when or how behavior executes.

---

## Key Discussion 2 – Hollywood Principle

"Don't call us. We'll call you."

Examples discussed:

- SwiftUI `.onAppear`
- UIKit lifecycle methods
- UITableView delegate/data source
- URLSession completion handlers
- NotificationCenter
- TestNG annotations

Common pattern:

The framework owns execution.

---

## Key Discussion 3 – EventBus Control Transfer

Question:

Who controls execution after:

```swift
eventBus.publish(...)
```

Answer:

Initially:

ViewModel

↓

UseCase

↓

EventBus

After publication:

EventBus

↓

Subscribers

Control ownership transfers to the EventBus.

---

## Key Discussion 4 – Publishers Don't Own Consequences

The UnlockUseCase should publish:

```
UnlockSucceeded
```

It should not know about:

- Analytics
- Notifications
- Widgets
- Apple Watch
- AI
- HomeKit
- Email
- Live Activities

New functionality should be implemented by adding subscribers rather than modifying the publisher.

---

## Key Discussion 5 – Architecture Is About Control

Every pattern we've studied answers:

> Who owns this responsibility or decision?

Examples:

- Composition Root → Object creation
- Strategy → Runtime decisions
- State Machine → Valid transitions
- EventBus → Event routing
- Framework → Lifecycle
- UseCase → Business workflow

This became the unifying principle across Lessons 6–12.

---

## Architect's Insight

A mature architecture minimizes components that own multiple unrelated decisions.

Each component should own one category of control while collaborating with others through clear abstractions.
