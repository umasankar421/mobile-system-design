# Lesson 12 – Who Really Controls the System?

> "Architecture is fundamentally about deciding who owns control."

---

# Learning Objectives

After completing this lesson, you should be able to:

- Explain what Inversion of Control (IoC) really means.
- Differentiate Dependency Injection from IoC.
- Recognize IoC in Apple's frameworks.
- Explain the Hollywood Principle.
- Understand control transfer in Event-Driven systems.
- Identify which component owns execution flow.

---

# Why This Lesson Matters

Many engineers define IoC as:

> "The framework calls your code."

Although correct, this definition is incomplete.

A better definition is:

> A component voluntarily gives up control over **when** or **how** its behavior executes to another component.

This definition applies to far more than UI frameworks.

---

# The Hollywood Principle

> "Don't call us. We'll call you."

Instead of your application driving the framework:

```
Application

↓

Framework
```

Modern frameworks work like this:

```
Framework

↓

Application
```

The framework owns the execution lifecycle.

Your code plugs into predefined extension points.

---

# Examples of IoC in iOS

## SwiftUI

```swift
.onAppear {

}
```

SwiftUI decides when the closure executes.

---

## UIKit

```swift
override func viewDidLoad() {

}
```

UIKit controls the lifecycle.

---

## UITableView

```swift
func tableView(
    _ tableView: UITableView,
    cellForRowAt indexPath: IndexPath
)
```

UITableView requests cells when required.

The ViewController does not decide.

---

## URLSession

```swift
URLSession.shared.dataTask(...)
```

The completion handler executes when URLSession decides.

---

## NotificationCenter

```swift
NotificationCenter.default.addObserver(...)
```

Observers register interest.

NotificationCenter invokes them later.

---

## TestNG / JUnit

Annotations such as:

```
@BeforeMethod

@Test

@AfterMethod
```

are executed by the framework.

---

## Delegates

Examples include:

- UITableViewDelegate
- UICollectionViewDelegate
- CLLocationManagerDelegate
- CBCentralManagerDelegate
- WKNavigationDelegate
- AVCaptureVideoDataOutputSampleBufferDelegate

The framework owns the lifecycle.

---

# The Common Pattern

Every example follows the same architecture:

```
Framework

↓

Your Code
```

Your code never decides when callbacks execute.

---

# Event-Driven IoC

Consider:

```swift
eventBus.publish(
    UnlockSucceeded(...)
)
```

Execution initially follows:

```
ViewModel

↓

UnlockUseCase

↓

EventBus.publish()
```

After publication:

```
EventBus

↓

Subscriber A

Subscriber B

Subscriber C
```

Control transfers to the EventBus.

The publisher no longer controls execution.

---

# Control Transfer

Execution can be divided into two phases.

## Phase 1

Traditional method invocation.

```
View

↓

ViewModel

↓

UseCase

↓

publish()
```

---

## Phase 2

Event-driven execution.

```
EventBus

↓

Listeners
```

The publisher no longer owns the execution flow.

---

# Publish Intent, Not Consequences

A UseCase should express:

```
UnlockSucceeded
```

It should not orchestrate:

- Analytics
- Notifications
- Widgets
- Cloud Sync
- AI
- HomeKit
- Siri
- Apple Watch
- Email

Those concerns belong to independent subscribers.

---

# One Event, Unlimited Extensions

```
UnlockSucceeded

↓

Analytics

Audit

Cloud Sync

Widget

Live Activity

Apple Watch

AI

Notification

HomeKit
```

Adding new capabilities should require adding new subscribers rather than modifying the publisher.

---

# IoC Across Previous Lessons

Lesson 6

Composition Root owns object creation.

---

Lesson 7

Strategy owns runtime behavior selection.

---

Lesson 8

State Machine owns valid transitions.

---

Lesson 9

EventBus owns event distribution.

---

Lesson 10

Subscribers own post-event behavior.

---

Lesson 11

Dependency Injection inverts object creation.

---

Lesson 12

Frameworks and infrastructure own execution flow.

---

# Architectural Insight

Every design pattern answers one question:

> Who owns this decision or this control?

Examples:

| Component | Owns |
|-----------|------|
| Composition Root | Object creation |
| Strategy | Runtime decisions |
| State Machine | State transitions |
| EventBus | Event routing |
| Framework | Lifecycle |
| URLSession | Network callbacks |
| NotificationCenter | Observer notification |
| UnlockUseCase | Business workflow |

---

# Common Mistakes

❌ Calling every callback manually.

❌ Treating IoC as synonymous with constructor injection.

❌ Allowing publishers to orchestrate subscribers.

❌ Making business logic responsible for lifecycle management.

❌ Assuming the caller always owns execution.

---

# Interview Questions

### Q1

What is Inversion of Control?

---

### Q2

How is Dependency Injection related to IoC?

---

### Q3

Why is SwiftUI considered an IoC framework?

---

### Q4

Give five examples of IoC from Apple's frameworks.

---

### Q5

What happens architecturally after EventBus.publish()?

---

### Q6

Why should publishers not know their subscribers?

---

### Q7

Explain the Hollywood Principle.

---

### Q8

Why is NotificationCenter an example of IoC?

---

# Key Takeaways

- IoC is broader than Dependency Injection.
- Frameworks own lifecycle execution.
- Event-driven systems transfer control after publishing.
- Publishers express intent, not consequences.
- Subscribers independently react to business events.
- Modern software architecture is fundamentally about assigning ownership of control.

---

# Evolution of My Thinking

## Before this lesson

- I associated IoC mostly with constructor injection.
- I saw callbacks as framework features rather than architectural concepts.
- I didn't recognize the common principle behind delegates, notifications and publishers.

## After this lesson

- I understand that IoC is about transferring control to another component.
- I recognize the Hollywood Principle throughout Apple's frameworks.
- I understand that EventBus introduces a deliberate control transfer.
- I can identify who owns execution flow in a system.
- I understand that architecture is largely about assigning responsibility and control ownership.

---

# Architect's Mental Model

Whenever you encounter a new framework, pattern or library, ask:

1. Who owns the lifecycle?
2. Who decides when my code executes?
3. Who owns this decision?
4. Has control been transferred?
5. Is this construction-time control or runtime control?

These questions reveal the underlying architecture more reliably than focusing on APIs or implementation details.
