# Lesson 6 – Dependency Injection & Composition Root

> "The object that uses a dependency should not create that dependency."

---

# Learning Objectives

After completing this lesson you should be able to:

- Understand why objects should not create their own dependencies.
- Explain Dependency Injection beyond framework-specific terminology.
- Understand the role of the Composition Root.
- Differentiate between business behavior and application configuration.
- Understand how Composition Root works with feature assemblies.
- Recognize the relationship between Dependency Injection and SOLID.

---

# Introduction

As software systems grow, object creation becomes increasingly complex.

Without a clear strategy for assembling dependencies, business objects become tightly coupled to concrete implementations, making the system difficult to evolve, test, and maintain.

Dependency Injection solves this problem by separating **object behavior** from **object creation**.

---

# The Problem

Consider the following implementation.

```swift
class LockViewModel {

    private let unlockUseCase = UnlockDoorUseCase()

}

class UnlockDoorUseCase {

    private let repository = LockRepository()

}

class LockRepository {

    private let transport = BLETransport()

}
```

Although this code compiles and works, it introduces several architectural problems.

---

# Problems With Self-Created Dependencies

## 1. Tight Coupling

Each object is permanently coupled to a concrete implementation.

Changing BLE to Matter requires modifying existing classes.

---

## 2. Difficult Unit Testing

Replacing dependencies with mocks becomes difficult because the object controls its own collaborators.

---

## 3. Poor Reusability

Business components cannot easily operate with different implementations.

Example:

- BLETransport
- MatterTransport
- CloudTransport

---

## 4. Hidden Object Graph

Every object creates the next object.

```
ViewModel

↓

UseCase

↓

Repository

↓

Transport
```

No single place describes how the application is assembled.

This makes large applications difficult to understand.

---

# Dependency Injection

Dependency Injection is often misunderstood as a framework or a specific coding technique.

The architectural principle is much simpler.

> **An object should receive its dependencies instead of creating them.**

For example:

```swift
class UnlockDoorUseCase {

    init(repository: LockRepositoryProtocol) {
        self.repository = repository
    }

}
```

The UseCase no longer knows:

- who created the repository
- which implementation was selected
- how the repository was configured

It only collaborates with it.

---

# Object Creation vs Business Logic

Business objects should focus on business behavior.

They should not know:

- how collaborators were created
- where dependencies came from
- which implementation was selected

Object creation is a separate responsibility.

---

# Composition Root

The Composition Root is responsible for assembling the application's object graph.

Its responsibility is **application assembly**, not business logic.

Example:

```
App Launch

↓

AppCompositionRoot

↓

LockAssembly

↓

UnlockDoorUseCase

↓

LockRepository

↓

BLETransport
```

Every dependency is wired together before business execution begins.

---

# One Composition Root

An independently running application should have **one Composition Root**.

Examples:

| Executable | Composition Root |
|------------|------------------|
| iOS App | AppCompositionRoot |
| Widget Extension | WidgetCompositionRoot |
| Watch App | WatchCompositionRoot |
| Siri Extension | SiriCompositionRoot |

Large applications should not create multiple roots.

Instead, the Composition Root delegates assembly to feature-specific assemblies.

---

# Feature Assemblies

As the application grows, feature assembly should be delegated.

Example:

```
AppCompositionRoot

↓

AuthenticationAssembly

↓

LockAssembly

↓

FirmwareAssembly

↓

AnalyticsAssembly
```

Each assembly knows how to build its feature.

The Composition Root coordinates them.

---

# Composition Root vs Business Policy

One of the most important distinctions.

## Composition Root owns:

- object creation
- dependency wiring
- application configuration
- environment-specific implementations

Examples:

Development

↓

MockTransport

Production

↓

BLETransport

---

## Business Layer owns:

- Premium users
- Guest permissions
- Subscription rules
- Unlock policies
- Feature behavior

The Composition Root must never implement business rules.

---

# Configuration vs Business Logic

Configuration:

```
Development Build

↓

MockTransport
```

Production Build

↓

BLETransport

This is configuration.

Business Policy:

```
Premium User

↓

Matter

Guest User

↓

BLE
```

This is business logic.

Business rules belong inside the Application layer, not the Composition Root.

---

# Relationship with SOLID

## Single Responsibility Principle

The Composition Root has one reason to change:

> The application's dependency graph changes.

---

## Open/Closed Principle

Business objects remain closed for modification.

Only the Composition Root changes when application wiring changes.

This does **not** violate OCP because configuration is expected to evolve.

---

# Relationship with Strategy Pattern

During this lesson we naturally discovered the need for Strategy.

Scenario:

```
50% Users

↓

BLE

50% Users

↓

Matter
```

The new responsibility is:

> Transport Selection

Instead of modifying business logic, a dedicated strategy can determine which transport should be used.

This demonstrates how design patterns emerge from responsibility analysis.

---

# Architect's Insights

## Insight 1

Business objects should not know how they came into existence.

---

## Insight 2

Good architecture does not eliminate change.

It localizes change.

---

## Insight 3

There should be one Composition Root per independently executable application.

---

## Insight 4

The Composition Root assembles objects.

It does not implement business behavior.

---

# Common Mistakes

❌ ViewModels creating UseCases.

❌ UseCases creating repositories.

❌ Repositories creating transports.

❌ Business logic inside the Composition Root.

❌ Multiple Composition Roots inside the same application.

---

# Interview Questions

### Q1

Explain Dependency Injection without mentioning any framework.

### Q2

Why shouldn't business objects create their own dependencies?

### Q3

What is a Composition Root?

### Q4

Does modifying the Composition Root violate the Open/Closed Principle?

### Q5

Why should there be only one Composition Root per application?

### Q6

What is the difference between application configuration and business policy?

---

# Key Takeaways

- Objects should receive dependencies instead of creating them.
- Object creation and business behavior are separate responsibilities.
- The Composition Root assembles the application.
- Feature assemblies help organize large applications.
- Configuration belongs to the Composition Root.
- Business policy belongs to business objects.
- Dependency Injection is an architectural principle, not a framework feature.

---

# Summary

Dependency Injection is the practice of separating object creation from object behavior.

The Composition Root serves as the application's assembly point, wiring together dependencies while allowing business components to remain independent of implementation details.

By localizing object creation and preserving stable business logic, Dependency Injection creates systems that are easier to test, extend, maintain, and reason about.
