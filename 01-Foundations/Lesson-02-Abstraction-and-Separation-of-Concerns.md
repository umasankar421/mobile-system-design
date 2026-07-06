# Lesson 2 – Abstraction & Separation of Concerns

## Learning Objectives

After completing this lesson, you should be able to:

- Explain Separation of Concerns.
- Explain Abstraction.
- Understand Coupling and Cohesion.
- Identify responsibility boundaries.
- Design components with a single responsibility.

---

# Separation of Concerns

Definition:

A software system should be divided into components where each component owns one well-defined responsibility.

The goal is not to create many classes.

The goal is to create clear ownership.

---

# Single Responsibility

Ask one question:

> Can this component be described in one sentence?

Examples:

UnlockDoorUseCase

> Coordinates the unlock workflow.

BLEClient

> Communicates with the lock over BLE.

TransportSelector

> Chooses the appropriate communication mechanism.

If a component cannot be described in one sentence, it probably has too many responsibilities.

---

# Abstraction

Abstraction means hiding implementation details while exposing only the behavior required by the consumer.

Example:

Instead of exposing:

- Connect
- Discover Services
- Discover Characteristics
- Write Characteristic

Expose:

unlock(lockID)

The caller does not need to understand BLE.

---

# Coupling

Coupling measures how dependent one component is on another.

Goal:

Low Coupling

Example:

ViewModel

↓

LockRepositoryProtocol

Good.

Example:

ViewModel

↓

BLEManager

Poor.

---

# Cohesion

Cohesion measures how related the responsibilities inside a component are.

Goal:

High Cohesion

Good:

BLEClient

- Scan
- Connect
- Disconnect
- Write

Poor:

BLEManager

- Connect
- Login
- Analytics
- Cache Images

---

# High Cohesion + Low Coupling

This is one of the most important principles in software architecture.

Architects constantly optimize for:

- High Cohesion
- Low Coupling

---

# Business vs Infrastructure

Business Components:

- Use Cases
- Domain Services
- Validators

Infrastructure Components:

- BLE
- Networking
- Databases
- Analytics
- Logging

Business logic should never depend directly on technology.

---

# Firmware Update Example

A possible decomposition:

FirmwareUpdateUseCase

↓

FirmwareValidationService

↓

FirmwareDownloadService

↓

FirmwareTransferService

↓

FirmwareTransport

↓

BLE / Wi-Fi / Matter

Additional responsibilities:

- Progress Tracking
- Integrity Validation
- Retry Policy
- State Machine
- Session Management

---

# Common Mistakes

❌ Massive ViewModels

❌ Massive Repositories

❌ Technology leaking into business logic

❌ Classes with multiple unrelated responsibilities

---

# Best Practices

Every component should:

- Have one reason to change.
- Hide implementation details.
- Depend on abstractions.
- Avoid unnecessary knowledge of other layers.

---

# Key Takeaways

Architecture is responsibility assignment.

Abstraction protects the system from implementation changes.

Separation of Concerns improves maintainability.

High Cohesion and Low Coupling should guide every design decision.

Business capabilities should remain independent from infrastructure technologies.

---

# What's Next

Lesson 3:

The Dependency Rule

This lesson explains why dependency direction is the foundation of Clean Architecture.
