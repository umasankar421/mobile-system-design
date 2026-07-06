# Lesson 3 – The Dependency Rule

> "Protect business rules from technology."

---

# Learning Objectives

After completing this lesson, you should be able to:

- Understand what a dependency is.
- Distinguish between control flow and dependency flow.
- Apply the Dependency Rule.
- Understand why abstractions are introduced.
- Separate business decisions from technical decisions.
- Protect the domain from infrastructure changes.

---

# Introduction

One of the biggest misconceptions in software architecture is that Dependency Injection and the Dependency Rule are the same concept.

They are not.

Dependency Injection is an object creation technique.

The Dependency Rule is an architectural principle.

Understanding this distinction is essential for designing systems that remain maintainable as technology evolves.

---

# What is a Dependency?

A component depends on another component when it cannot perform its responsibility without it.

Example:

```swift
class UnlockDoorUseCase {
private let repository: LockRepository
}
```

The `UnlockDoorUseCase` depends on the `LockRepository`.

A useful test is:

> If component B disappears and component A stops working, then A depends on B.

---

# Control Flow vs Dependency Flow

These two concepts are frequently confused.

## Control Flow

Control flow describes the runtime execution of the application.

```
User

↓

View

↓

ViewModel

↓

UnlockDoorUseCase

↓

LockRepository

↓

Transport

↓

Lock
```

This is how requests travel through the system.

---

## Dependency Flow

Dependency flow describes compile-time knowledge.

```
ViewModel

↓

UnlockDoorUseCase (Protocol)

↓

LockRepository (Protocol)

↓

Transport (Protocol)

↓

BLE / Wi-Fi / Matter
```

Good architecture keeps dependencies pointing toward stable abstractions rather than unstable implementations.

---

# Stable vs Unstable Components

Some components change frequently.

Examples:

- CoreBluetooth
- URLSession
- Networking libraries
- Analytics SDKs
- Cloud providers
- Database implementations

These are implementation details.

Other components are relatively stable.

Examples:

- Lock
- User
- Home
- Unlock Door
- Share Access
- Update Firmware

These represent business concepts.

Good architecture protects stable business concepts from unstable technologies.

---

# The Dependency Rule

**Dependencies should point toward stable business policies, not toward changing technologies.**

Business logic should not know:

- BLE
- Matter
- HTTP
- SQLite
- Firebase
- CoreBluetooth

Instead, it should depend on abstractions that represent business capabilities.

---

# Business Decisions vs Technical Decisions

One of the most important architectural insights is that not all decisions belong in the same layer.

## Business Decisions

Examples:

- Guests cannot unlock remotely.
- Premium users may use Cloud unlock.
- Firmware updates require administrator permission.
- Face ID is required after 10 minutes of inactivity.

These decisions belong in the Application or Domain layer.

---

## Technical Decisions

Examples:

- BLE signal is weak.
- RSSI dropped below threshold.
- Matter is unavailable.
- Retry over Wi-Fi.
- Packet fragmentation.
- Connection timeout.

These belong in the Infrastructure layer.

Business logic should not know technical details such as RSSI or packet fragmentation.

Infrastructure should not know business concepts such as Premium Users or Guest Access.

---

# Separating Decision Layers

Instead of one component making every decision, architecture should distribute decisions according to knowledge.

```
UnlockDoorUseCase
│
│ Business Policy
▼
LockRepository
│
│ Technical Routing
▼
TransportSelector
│
▼
BLE
Wi-Fi
Matter
```

Each layer makes decisions only about information it understands.

---

# Information Expert Principle (GRASP)

A responsibility should be assigned to the component that possesses the information required to fulfill it.

Instead of asking:

> Where should this code go?

Ask:

> Which component already has the knowledge required to make this decision?

Examples:

Business knowledge:

- User Role
- Subscription
- Permissions
- Authentication

Infrastructure knowledge:

- RSSI
- Packet Loss
- Network Latency
- Connection Quality

This principle naturally leads to better separation of concerns.

---

# Example: Replacing BLE with UWB

Suppose the product changes from BLE to UWB.

The following components should remain unchanged:

- Lock
- UnlockDoorUseCase
- FirmwareValidationService
- ShareAccessUseCase

The components that should change are:

- BLETransport
- Transport registration
- UWBTransport implementation

The business capability remains:

> Unlock the door.

Only the communication technology changes.

---

# Architecture Insight

Technology changes frequently.

Business capabilities evolve much more slowly.

A well-designed architecture isolates technology changes so that business logic remains stable.

---

# Common Mistakes

❌ Business logic depends directly on CoreBluetooth.

❌ UseCases know HTTP endpoints.

❌ Domain entities reference networking frameworks.

❌ UI makes transport decisions.

❌ Infrastructure contains business rules.

---

# Best Practices

- Depend on abstractions instead of implementations.
- Keep business rules technology-independent.
- Separate business decisions from technical decisions.
- Assign responsibilities to the component with the required knowledge.
- Design for change, not just today's requirements.

---

# Architect's Eye 👀

Whenever a new requirement arrives, ask:

> "Who naturally owns the knowledge required to make this decision?"

This question often reveals the correct architectural boundary.

Examples:

Business Team changes policy:

→ Business Layer

Connectivity Team changes transport:

→ Infrastructure Layer

---

# Architecture Decision Record (ADR)

## Decision

Business workflows will depend only on abstractions representing communication capabilities.

Transport-specific implementations (BLE, Wi-Fi, Matter, UWB) remain replaceable infrastructure components.

## Consequences

Advantages:

- Easier to introduce new transports.
- Reduced coupling.
- Stable business logic.
- Better testability.

Trade-offs:

- More abstractions.
- Slightly higher initial complexity.
- Requires disciplined dependency management.

---

# Interview Questions

### Q1

What is the difference between Dependency Injection and the Dependency Rule?

### Q2

Explain the difference between control flow and dependency flow.

### Q3

Why should business logic avoid depending on infrastructure frameworks?

### Q4

If BLE is replaced with UWB, which layers should change?

### Q5

How do you decide where a responsibility belongs?

---

# Key Takeaways

- Dependency direction is more important than folder structure.
- Business logic should remain independent of technology.
- Separate business policy from technical policy.
- Assign responsibilities to the Information Expert.
- Good architecture isolates change.

---

# Summary

The Dependency Rule is one of the foundational principles of software architecture.

It encourages dependencies to point toward stable business concepts instead of changing technologies.

When correctly applied, technology choices become implementation details rather than architectural constraints.

This principle enables systems to evolve while keeping business logic stable, testable, and maintainable.

---

# Next Lesson

Lesson 4 – SOLID Principle #1: Single Responsibility Principle (SRP)

We will discover why SRP is much deeper than "one class, one job" and learn how architects use reasons to change—not line counts or class sizes—to determine responsibilities.
