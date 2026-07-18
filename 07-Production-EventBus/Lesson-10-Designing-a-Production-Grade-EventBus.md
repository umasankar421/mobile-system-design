# Lesson 10 – Designing a Production-Grade Event Bus

> "A good Event Bus knows everything about events and nothing about the business."

---

# Learning Objectives

After completing this lesson, you should be able to:

- Design an Event Bus suitable for a production iOS application.
- Understand the responsibilities and boundaries of an Event Bus.
- Apply the Single Responsibility Principle to infrastructure services.
- Explain why an Event Bus belongs in the Application layer.
- Understand subscriber lifecycle management.
- Reason about synchronous vs asynchronous event delivery.
- Design for scalability, reliability, and maintainability.

---

# Why This Lesson?

In Lesson 9 we learned **Event-Driven Architecture**.

However, Event-Driven Architecture is only a concept until there is a mechanism that allows publishers and subscribers to communicate.

That mechanism is the **Event Bus**.

The Event Bus is infrastructure.

It is not business logic.

---

# High-Level Architecture

```
                    User

                     │

                     ▼

              UnlockView

                     │

                     ▼

             UnlockViewModel

                     │

                     ▼

             UnlockUseCase

                     │

                     ▼

            LockRepository

                     │

                     ▼

             BLE Transport

                     │

                     ▼

              Lock Hardware

────────────────────────────────────────

UnlockSucceededEvent

                     │

                     ▼

                 EventBus

      ┌─────────┬──────────┬────────────┬────────────┐
      ▼         ▼          ▼            ▼            ▼

 Analytics   Widget   Notification   CloudSync   AIInsights
```

---

# Where Should EventBus Live?

The Event Bus should **not** belong to:

- Presentation
- Domain
- Repository
- Transport
- BLE Layer

Instead, it belongs to the **Application Layer**.

Reason:

It is shared infrastructure used by multiple independent parts of the system.

It plays a role similar to:

- Dependency Injection Container
- Logging Framework
- Configuration Service
- Navigation Coordinator

---

# Responsibilities of EventBus

## 1. Subscriber Registration

Maintain a registry of subscribers and the event types they are interested in.

Example:

```
UnlockSucceededEvent

↓

AnalyticsListener

WidgetListener

NotificationListener
```

---

## 2. Event Publishing

Receive events from publishers.

Example:

```
UnlockSucceededEvent
```

The Event Bus accepts the event but performs no business processing.

---

## 3. Event Routing

Deliver events only to interested subscribers.

Example:

```
UnlockSucceededEvent

↓

AnalyticsListener

NotificationListener

WidgetListener
```

Subscribers interested in unrelated events should never receive it.

---

## 4. Subscription Lifecycle

Manage subscriber registration and unregistration.

Example:

```
ViewModel appears

↓

Subscribe

↓

ViewModel disappears

↓

Unsubscribe
```

This prevents memory leaks.

---

## 5. Thread Dispatching

Listeners may execute on different execution contexts.

Examples:

- Main Thread
- Background Queue

The Event Bus may either:

- Dispatch directly
- Delegate scheduling to another component

---

## 6. Failure Isolation

One listener failing should never prevent other listeners from executing.

```
AnalyticsListener ❌

↓

NotificationListener ✅

↓

WidgetListener ✅
```

---

## 7. Predictable Delivery

Delivery should be deterministic.

The Event Bus should define a predictable dispatch strategy rather than relying on undefined execution order.

---

# Responsibilities the EventBus Should NOT Have

The Event Bus should **not**:

- Know business rules
- Know UnlockUseCase
- Know BLE
- Know Repositories
- Transform business payloads
- Execute business workflows
- Retry network requests
- Log analytics
- Persist domain data

Its only responsibility is communication.

---

# Why EventBus Should Not Know BLE

Imagine replacing BLE with:

- Matter
- UWB
- NFC

Should EventBus change?

No.

Reason:

The Event Bus depends only on events.

It is completely independent of transport technologies.

---

# Why EventBus Should Not Know UnlockUseCase

Imagine these publishers:

- UnlockUseCase
- LoginUseCase
- FirmwareUpdateUseCase
- PaymentUseCase

Should EventBus know about any of them?

No.

Publishers publish.

Subscribers subscribe.

The Event Bus only connects them.

---

# Subscriber Lifecycle

Improper lifecycle management can cause memory leaks.

Bad:

```
ViewModel

↓

Subscribe

↓

ViewModel destroyed

↓

EventBus still retains ViewModel
```

Result:

Memory leak.

Correct approach:

The Event Bus stores weak references (or equivalent lifecycle-safe subscriptions).

---

# Supporting the Open/Closed Principle

Product introduces:

100 new events.

Should EventBus change?

No.

Only:

- New Event Types
- New Subscribers

The infrastructure remains unchanged.

This is another application of the Open/Closed Principle.

---

# Event Dispatch Strategies

## Synchronous

```
Publish

↓

Listener A

↓

Listener B

↓

Listener C
```

Advantages:

- Predictable
- Easier debugging

Disadvantages:

- Slow listeners block the publisher.

---

## Asynchronous

```
Publish

↓

Return Immediately

↓

Background Processing

↓

Listeners
```

Advantages:

- Better responsiveness
- Better scalability

Disadvantages:

- Ordering becomes more complex.

---

# Event Queue

One of the most important production decisions.

Problem:

A listener publishes another event while the current event is still being processed.

Bad:

Recursive event dispatch.

```
UnlockSucceeded

↓

AnalyticsCompleted

↓

DashboardUpdated

↓

AnotherEvent

↓

AnotherEvent
```

Potential problems:

- Stack growth
- Unpredictable ordering

Better:

Queue events.

```
Queue

↓

UnlockSucceeded

↓

AnalyticsCompleted

↓

DashboardUpdated

↓

NotificationSent
```

Benefits:

- Predictable processing
- Simpler debugging
- Better reliability

---

# Snapshot Dispatch

Problem:

A subscriber unsubscribes while an event is being dispatched.

Bad:

Modify subscriber collection while iterating.

Better:

Take a snapshot of current subscribers before dispatch.

```
Subscribers Snapshot

↓

Dispatch to Snapshot

↓

Apply subscription changes afterwards
```

This guarantees stable iteration.

---

# EventBus Design Principles

A production Event Bus should:

- Maintain subscribers
- Route events
- Isolate failures
- Manage subscriber lifecycle
- Support predictable dispatch
- Remain independent of business logic

---

# Architectural Boundaries

The Event Bus should know only:

```
Publisher

↓

Event

↓

Subscriber
```

It should know nothing about:

- Unlock
- Firmware
- Login
- BLE
- Cloud APIs
- Analytics

This makes the infrastructure reusable across the application.

---

# Relationship with Previous Lessons

```
Command

↓

State Machine

↓

Use Case

↓

Repository

↓

Transport

↓

Infrastructure

↓

Business Event

↓

Event Bus

↓

Listeners
```

Every lesson builds upon the previous one.

---

# Common Mistakes

❌ Allowing EventBus to know business rules.

❌ Storing strong references to subscribers.

❌ Executing all listeners synchronously.

❌ Recursive event dispatch.

❌ Letting listeners depend on one another.

❌ Mixing payload transformation with event routing.

---

# Interview Questions

### Q1

Why should an Event Bus belong to the Application layer?

---

### Q2

What responsibilities should an Event Bus have?

---

### Q3

What responsibilities should it avoid?

---

### Q4

How do you prevent memory leaks?

---

### Q5

Why should EventBus not know about BLE or UnlockUseCase?

---

### Q6

Why is an event queue preferable to recursive event publishing?

---

### Q7

What is snapshot dispatch, and why is it useful?

---

# Key Takeaways

- The Event Bus is infrastructure, not business logic.
- It connects publishers and subscribers without knowing the business.
- Subscriber lifecycle management is essential on iOS.
- Queue-based dispatch is generally safer than recursive dispatch.
- Snapshot dispatch prevents collection mutation during delivery.
- Adding new events should never require modifying the Event Bus.
- The Event Bus supports the Open/Closed Principle by remaining stable while the application evolves.

---

# Evolution of My Thinking

## Before this lesson

- I thought an Event Bus simply forwarded events.
- I focused on "making events work."

## After this lesson

- I understand that an Event Bus is an infrastructure component with clearly defined responsibilities and boundaries.
- I know why it belongs in the Application layer.
- I understand the importance of subscriber lifecycle management.
- I recognize why recursive event publishing can become problematic.
- I understand why queue-based dispatch and snapshot dispatch improve reliability.
- I know that the Event Bus should remain completely independent of business concepts.

---

# Summary

A production-grade Event Bus is responsible for transporting events—not interpreting them.

Its value lies in decoupling publishers from subscribers while remaining reusable, scalable, and independent of business logic.

A well-designed Event Bus supports long-term system evolution without becoming a source of coupling.
