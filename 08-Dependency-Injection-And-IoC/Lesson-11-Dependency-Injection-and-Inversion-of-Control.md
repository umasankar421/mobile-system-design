# Lesson 11 – Dependency Injection & Inversion of Control (IoC)

> "Business objects should solve business problems, not construct object graphs."

---

# Learning Objectives

After completing this lesson, you should be able to:

- Understand the difference between Dependency Injection (DI) and Inversion of Control (IoC).
- Explain why object creation is a separate responsibility from business logic.
- Understand why the Composition Root exists.
- Distinguish construction-time decisions from runtime decisions.
- Explain why Strategy Pattern and DI complement each other.
- Recognize how IoC underpins modern software architectures.

---

# Motivation

Consider the following implementation.

```swift
class UnlockUseCase {

    private let repository = LockRepository()

    func execute() {
        repository.unlock()
    }
}
```

At first glance this looks simple.

However, it introduces an architectural problem.

The UnlockUseCase is responsible for:

- Unlocking the door
- Deciding which repository to use
- Constructing that repository

These are separate responsibilities.

---

# The Core Principle

A class should **use** its dependencies.

It should **not decide** what those dependencies are.

This is the heart of Dependency Injection.

---

# Restaurant Analogy

Imagine hiring a chef.

Bad chef:

- Brings his own kitchen
- Builds the stove
- Installs electricity
- Sets up plumbing

Good chef:

Walks into an existing kitchen and starts cooking.

The kitchen is provided.

The chef focuses only on cooking.

---

Software is the same.

UnlockUseCase should focus on unlocking.

It should not build repositories.

---

# Why Creating Dependencies Is a Problem

Suppose Product evolves.

Week 1:

```
LockRepository
```

Week 2:

```
CachedLockRepository
```

Week 3:

```
EncryptedLockRepository
```

Week 4:

```
OfflineFirstLockRepository
```

If UnlockUseCase creates these objects directly, it changes every time.

The UseCase becomes responsible for implementation selection.

---

# Dependency Injection

Instead of creating dependencies internally, they are provided from outside.

```
Composition Root

↓

Creates Repository

↓

Injects Repository

↓

UnlockUseCase
```

UnlockUseCase only depends on:

```
LockRepositoryProtocol
```

It never knows the concrete implementation.

---

# Inversion of Control (IoC)

Dependency Injection is a technique.

Inversion of Control is the underlying architectural principle.

Traditional control flow:

```
UnlockUseCase

↓

Creates Repository

↓

Creates BLE Client
```

IoC:

```
Composition Root

↓

Creates BLE Client

↓

Creates Repository

↓

Injects

↓

UnlockUseCase
```

Control over object creation has been inverted.

---

# Relationship Between DI and IoC

Dependency Injection is one way to implement Inversion of Control.

IoC is the broader concept.

DI is one implementation technique.

---

# Why Composition Root Exists

The Composition Root is responsible for building the application's object graph.

Responsibilities include:

- Creating services
- Creating repositories
- Creating transports
- Wiring dependencies
- Injecting implementations

It should not contain business rules.

---

# Construction-Time vs Runtime Decisions

One of the most important architectural distinctions.

---

## Construction-Time Decisions

These occur while building the object graph.

Examples:

- Production vs Mock Repository
- BLETransport vs FakeTransport
- Logger implementation
- EventBus implementation
- Analytics provider

These belong in the Composition Root.

---

## Runtime Decisions

These occur while the application is executing.

Examples:

- Bluetooth disabled
- Battery low
- User cancelled Face ID
- Retry over Cloud
- Lock jammed

These belong in:

- Use Cases
- Strategy objects
- State Machines
- Policies

---

# Example: Transport Selection

Requirement:

Premium users use Cloud first.

Free users use BLE first.

The Composition Root should not hardcode this decision.

Instead:

```
Composition Root

↓

Creates:

BLETransport

CloudTransport

↓

Injects

↓

TransportSelectionStrategy
```

At runtime:

```
TransportSelectionStrategy

↓

Evaluate user state

↓

Choose transport
```

The Composition Root provides available options.

The Strategy decides which option to use.

---

# Feature Flags

Requirement:

Enable AI transport selection for 5% of users.

Architecture:

```
Composition Root

↓

Creates:

BLEStrategy

AIStrategy

RemoteConfigService

↓

Injects

↓

TransportSelectionStrategy
```

Runtime:

```
TransportSelectionStrategy

↓

RemoteConfigService

↓

AI Enabled?

↓

User in rollout?

↓

Choose Strategy
```

---

# Responsibility Separation

## Composition Root

Answers:

> What components exist?

---

## Strategy

Answers:

> Which component should be used right now?

---

## Remote Configuration

Answers:

> What configuration values are currently available?

---

# Evolution of Transport Selection

Simple:

```
BLE
```

↓

Product adds Cloud.

↓

BLE + Cloud

↓

Product adds Matter.

↓

BLE + Cloud + Matter

↓

Product adds AI.

↓

BLE + Cloud + Matter + AI

The Composition Root evolves by creating additional implementations.

The Strategy evolves by refining runtime selection rules.

The UseCase remains unchanged.

---

# Architectural Connections

This lesson connects nearly every previous lesson.

```
Composition Root

↓

Dependency Injection

↓

Use Case

↓

Strategy

↓

Repository

↓

Transport

↓

State Machine

↓

Event Bus
```

Every component receives its collaborators from outside.

None of them construct their own object graphs.

---

# Common Mistakes

❌ Business objects creating repositories.

❌ UseCases deciding concrete implementations.

❌ Mixing object construction with business logic.

❌ Confusing construction-time decisions with runtime decisions.

❌ Putting feature-flag logic inside the Composition Root.

---

# Interview Questions

### Q1

What is the difference between Dependency Injection and Inversion of Control?

---

### Q2

Why shouldn't business objects construct their own dependencies?

---

### Q3

What responsibilities belong to the Composition Root?

---

### Q4

What is the difference between construction-time and runtime decisions?

---

### Q5

Why is Strategy Pattern often used together with Dependency Injection?

---

### Q6

How would you support gradual rollout of a new transport selection algorithm?

---

### Q7

Should the Composition Root decide which transport to use at runtime?

Why or why not?

---

# Key Takeaways

- Dependency Injection is a technique.
- Inversion of Control is the architectural principle.
- Business objects should focus on behavior, not object creation.
- The Composition Root owns object construction.
- Construction-time decisions belong in the Composition Root.
- Runtime decisions belong in business policies, strategies or state machines.
- Feature flag services provide configuration—they do not make business decisions.
- Strategy objects consume runtime information to choose behavior.

---

# Evolution of My Thinking

## Before this lesson

- I associated Dependency Injection mainly with unit testing.
- I focused on constructor injection syntax.
- I often mixed object creation with business behavior.

## After this lesson

- I understand that Dependency Injection separates object construction from business behavior.
- I understand that Inversion of Control is the broader architectural principle.
- I can distinguish construction-time decisions from runtime decisions.
- I know that the Composition Root builds object graphs while business components execute business logic.
- I recognize how Strategy Pattern and Dependency Injection work together.
- I understand why object creation should remain outside business objects.

---

# Summary

Dependency Injection is not primarily about testing.

It is about separating **what the system does** from **how the system is assembled**.

The Composition Root assembles the application.

Business objects execute business behavior.

Strategies make runtime decisions.

Feature configuration provides data.

Each component owns a single kind of responsibility, making the architecture scalable, maintainable and adaptable as requirements evolve.

---

# Architect's Mental Model

Whenever a new requirement arrives, ask yourself these questions:

1. Is this a **construction-time** decision or a **runtime** decision?
2. Who should own this decision?
3. Does this component need to know this information?
4. Am I creating an object, or am I using one?
5. Can I satisfy this requirement without modifying existing business logic?

These five questions form a practical checklist for assigning responsibilities and designing maintainable architectures.
