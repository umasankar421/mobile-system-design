# Lesson 9 – Event-Driven Architecture (EDA)

> "Good architectures don't ask 'Who should I call?'. They ask 'Who might be interested that this happened?'"

---

# Learning Objectives

After completing this lesson you should be able to:

- Understand the motivation behind Event-Driven Architecture.
- Differentiate Commands from Events.
- Design loosely coupled systems using Events.
- Understand Event Listeners and their responsibilities.
- Know when Event-Driven Architecture is appropriate.
- Recognize the trade-offs introduced by EDA.
- Design scalable mobile architectures where new features can be added without modifying existing business logic.

---

# Motivation

Consider the Unlock Door feature.

Initially, the UnlockUseCase only performs the unlock operation.

Later Product requests additional functionality after a successful unlock.

Requirements:

- Log Analytics
- Save Audit Logs
- Refresh Widget
- Send Notification
- Refresh Battery
- Cloud Sync
- Update Apple Watch
- Start Camera
- AI Insights
- Email Notification

A naïve implementation keeps adding direct calls inside UnlockUseCase.

Example:

```swift
unlock()

analytics.log()

audit.save()

notification.show()

widget.refresh()

cloud.sync()

email.send()
```

As the product evolves, UnlockUseCase becomes increasingly coupled to unrelated responsibilities.

This violates the Single Responsibility Principle.

---

# The Core Idea

Instead of directly calling every dependent service, the UseCase publishes a business event.

```
UnlockSucceeded
```

Interested components subscribe to that event.

The UseCase does not know who is listening.

---

# Traditional Architecture

```
UnlockUseCase

↓

Analytics

↓

Notification

↓

Widget

↓

Cloud Sync

↓

Audit Log
```

The UseCase depends on every downstream service.

This creates tight coupling.

---

# Event-Driven Architecture

```
UnlockUseCase

↓

Publish UnlockSucceeded

↓

Event Bus

↓

AnalyticsListener

AuditLogListener

NotificationListener

WidgetListener

CloudSyncListener
```

The UseCase knows only about the Event Bus.

Each listener is independent.

---

# What Is an Event?

An Event represents a business fact that has already occurred.

Examples:

- UnlockSucceeded
- DoorLocked
- FirmwareDownloaded
- BatteryLowDetected
- BLEDisconnected
- UserSignedOut

Events should generally be expressed in the past tense because they describe completed facts.

---

# Commands vs Events

## Command

Represents an intention.

Examples:

- UnlockDoor
- LockDoor
- DownloadFirmware
- RetryUnlock

Commands answer:

> "What should happen?"

---

## Event

Represents something that already happened.

Examples:

- DoorUnlocked
- FirmwareDownloaded
- UnlockFailed
- AuthenticationSucceeded

Events answer:

> "What happened?"

---

# Benefits of Event-Driven Architecture

## Loose Coupling

UnlockUseCase does not know about:

- Analytics
- Notifications
- Widgets
- AI
- Cloud Sync
- Email

These become independent listeners.

---

## Open/Closed Principle

When Product introduces a new requirement:

Example:

```
AIInsightsListener
```

No changes are required inside UnlockUseCase.

Only a new listener is added.

The business workflow remains unchanged.

---

## Better Separation of Responsibilities

Each listener owns exactly one concern.

Examples:

AnalyticsListener

Responsible only for analytics.

CloudSyncListener

Responsible only for synchronization.

WidgetListener

Responsible only for updating widgets.

Each listener can evolve independently.

---

# Example Listeners for UnlockSucceeded

Potential listeners include:

- AnalyticsListener
- AuditLogListener
- NotificationListener
- WidgetListener
- DynamicIslandListener
- AppleWatchListener
- EmailNotificationListener
- CloudSyncListener
- BatteryRefreshListener
- HomeAutomationListener
- AIInsightsListener
- AIAnomalyDetectionListener
- SiriSuggestionListener
- AppReviewPromptListener
- AutoLockSchedulerListener

Each listener reacts independently to the same business event.

---

# Listener Categories

As systems grow, listeners should be classified by criticality.

## Critical Business Listeners

Examples:

- AuditLog
- CloudSync
- LockStateUpdate

Typically synchronous or guaranteed delivery.

---

## User Experience Listeners

Examples:

- Widget
- Dynamic Island
- Notification
- Apple Watch

Often asynchronous and UI-oriented.

---

## External Integrations

Examples:

- Email
- Home Automation
- Siri

Usually asynchronous with retry policies.

---

## Analytics & Monitoring

Examples:

- Analytics
- AI Insights
- Metrics

Fire-and-forget asynchronous processing.

---

## Experimental Features

Examples:

- App Review Prompt
- A/B Testing
- Marketing Campaigns

Optional listeners that should never impact business execution.

---

# Architectural Problems Introduced by Many Listeners

Event-Driven Architecture introduces new challenges.

---

## 1. Ordering

Some operations may require a specific sequence.

Example:

CloudSync

↓

LockStateUpdated

↓

WidgetRefresh

Solution:

Introduce a Workflow Coordinator rather than relying on listener execution order.

---

## 2. Failure Isolation

If AnalyticsListener fails:

NotificationListener should still execute.

Each listener should fail independently.

---

## 3. Performance

Slow listeners (Email, AI processing) should not block the unlock flow.

Solution:

Asynchronous execution.

---

## 4. Event Storms

One event may trigger dozens of additional events.

Without discipline this can become difficult to understand.

Guideline:

Publish business events, not implementation details.

---

## 5. Observability

With many listeners debugging becomes harder.

Introduce:

- Correlation IDs
- Structured Logging
- Distributed Tracing
- Metrics

This allows engineers to trace the complete lifecycle of an event.

---

# Event Bus

The Event Bus acts as the communication mechanism between publishers and subscribers.

```
UnlockUseCase

↓

Publish UnlockSucceeded

↓

EventBus

↓

Subscribers
```

The publisher knows only about the Event Bus.

Subscribers know only about the events they care about.

---

# Event Contracts

Every event should define a stable contract.

Example:

```swift
UnlockSucceededEvent

lockId

userId

timestamp

transportUsed

correlationId
```

Stable contracts reduce coupling between teams.

---

# When NOT to Use Event-Driven Architecture

EDA is not suitable for every workflow.

Example:

Payment

↓

Reserve Inventory

↓

Create Order

↓

Send Confirmation

These operations require strict sequencing and transactional guarantees.

A Workflow Orchestrator is a better fit than independent listeners.

---

# Event-Driven Architecture in Unlock Flow

```
User

↓

UnlockView

↓

UnlockViewModel

↓

UnlockUseCase

↓

LockRepository

↓

BLE Transport

↓

Lock Hardware

────────────────────────────

UnlockSucceeded

↓

EventBus

↓

AnalyticsListener

AuditLogListener

NotificationListener

WidgetListener

CloudSyncListener

AIInsightsListener

EmailListener
```

Business logic remains stable while new capabilities are added independently.

---

# Relationship with Previous Lessons

The architecture has evolved significantly.

```
User Intent

↓

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

Each layer has a clearly defined responsibility.

---

# Common Mistakes

❌ Calling every service directly from the UseCase.

❌ Publishing implementation details as events.

❌ Making listeners depend on one another.

❌ Blocking the user while non-critical listeners execute.

❌ Assuming all listeners require the same execution guarantees.

---

# Interview Questions

### Q1

What is Event-Driven Architecture?

---

### Q2

What is the difference between a Command and an Event?

---

### Q3

How does Event-Driven Architecture support the Open/Closed Principle?

---

### Q4

Why should UnlockUseCase not directly call Analytics or Notification services?

---

### Q5

What are the challenges introduced by Event-Driven Architecture?

---

### Q6

How would you classify listeners in a production system?

---

### Q7

When would you avoid using Event-Driven Architecture?

---

# Key Takeaways

- Events represent business facts.
- Commands represent intentions.
- Event-Driven Architecture reduces coupling.
- New functionality is typically added by creating listeners rather than modifying business workflows.
- Listeners should be classified by business criticality.
- Failure isolation, ordering, retries and observability become important in production systems.
- Event-Driven Architecture improves extensibility but introduces new operational concerns that architects must address.

---

# Evolution of My Thinking

## Before this lesson

- I would have called Analytics, Notification, Widget and CloudSync directly from UnlockUseCase.
- Adding new functionality meant modifying existing business logic.
- I viewed the mobile application as the center of the system.

## After this lesson

- I understand that business workflows should publish business events rather than directly invoking unrelated components.
- I understand the distinction between Commands (intent) and Events (facts).
- I know how Event-Driven Architecture supports the Open/Closed Principle by allowing new listeners to be added without modifying existing UseCases.
- I understand that listeners should be classified based on criticality rather than treated equally.
- I recognize that Event-Driven Architecture introduces architectural responsibilities such as ordering, failure isolation, retries and observability.

---

# Summary

Event-Driven Architecture allows software systems to communicate through business events rather than direct method calls.

This reduces coupling, improves extensibility, and enables independent evolution of business capabilities.

Rather than modifying existing UseCases whenever Product introduces new requirements, architects add new listeners that react to existing business events.

This architectural style is widely used in modern distributed systems and can also provide significant benefits in large-scale mobile applications when applied thoughtfully.
