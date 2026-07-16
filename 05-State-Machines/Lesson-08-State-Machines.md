# Lesson 8 – State Machines: Modeling Behavior Instead of If-Else

> "Objects model data. State Machines model behavior."

---

# Learning Objectives

After completing this lesson you should be able to:

- Understand why State Machines are used.
- Differentiate between States, Events, Transitions and Failure Reasons.
- Design business workflows using states instead of boolean flags.
- Prevent state explosion.
- Build scalable workflows for complex product requirements.
- Understand how State Machines complement Use Cases.

---

# Motivation

As products evolve, business workflows become increasingly complex.

Using boolean flags to model behavior often leads to impossible combinations and unreadable code.

Example:

```swift
if isDownloading &&
   battery > 20 &&
   !isPaused &&
   !isCancelled {
}
```

As new requirements are introduced, conditional logic grows exponentially.

State Machines solve this problem by modeling the workflow explicitly.

---

# Core Idea

A workflow is always in exactly one business state.

Instead of multiple flags:

```swift
isAuthenticating
isUnlocking
isCompleted
```

Model a single state.

```
Idle

↓

Authenticating

↓

Unlocking

↓

Completed
```

Only one state exists at any point in time.

---

# Four Building Blocks

Every State Machine consists of four concepts.

## 1. State

Represents where the workflow currently is.

Examples:

- Idle
- Validating
- Authenticating
- SelectingTransport
- Unlocking
- Completed
- Failed

States answer:

> "Where is the workflow right now?"

---

## 2. Event

Represents something that happened.

Examples:

- UnlockRequested
- ValidationSucceeded
- ValidationFailed
- AuthenticationSucceeded
- AuthenticationFailed
- TransportSelected
- UnlockSucceeded
- UnlockFailed

Events answer:

> "What happened?"

Events do not decide anything.

They simply describe facts.

---

## 3. Transition

Represents movement between states.

Example:

```
Idle

-- UnlockRequested -->

Validating
```

Another example:

```
Authenticating

-- AuthenticationSucceeded -->

SelectingTransport
```

Transitions describe how the workflow evolves.

---

## 4. Failure Reason

Failure Reasons explain why an operation failed.

Examples:

- BluetoothDisabled
- BluetoothPermissionDenied
- LockJammed
- BatteryLow
- AlreadyUnlocked
- Timeout
- InvalidRequest

Failure Reasons are **data**, not states.

Example:

```
Failed(reason: .bluetoothDisabled)
```

instead of

```
BluetoothDisabledState
```

---

# Unlock Workflow

The unlock feature can be represented by the following business states.

```
Idle
      │
      ▼
Validating
      │
      ▼
Authenticating
      │
      ▼
SelectingTransport
      │
      ▼
Unlocking
      │
 ┌────┴────┐
 ▼         ▼
Completed  Failed(reason)
```

The workflow remains stable even when additional transport technologies are introduced.

---

# Why This Design Scales

Suppose Product later introduces:

- Matter
- UWB
- Apple Watch
- Siri
- NFC

The business workflow remains unchanged.

Only transport implementations evolve.

The State Machine remains stable because it models business behavior rather than implementation details.

---

# State Explosion

One common mistake is creating implementation-specific states.

Poor design:

```
UnlockingViaBLE

UnlockingViaCloud

UnlockingViaMatter

UnlockingViaUWB

RetryingViaCloud

RetryingViaMatter
```

This rapidly becomes unmanageable.

Better design:

```
Unlocking
```

The active transport becomes internal data rather than a separate state.

---

# Commands vs Events

An important architectural distinction.

## Commands

Represent intent.

Examples:

- UnlockRequested
- RetryRequested
- CancelRequested

Commands answer:

> "What does someone want to happen?"

---

## Events

Represent facts.

Examples:

- ValidationSucceeded
- AuthenticationSucceeded
- UnlockSucceeded
- UnlockFailed

Events answer:

> "What actually happened?"

Commands request work.

Events describe completed work.

---

# Architect's Mental Model

When designing workflows, ask three questions.

## Question 1

Where is the workflow?

Answer:

State.

---

## Question 2

What happened?

Answer:

Event.

---

## Question 3

What should happen next?

Answer:

Transition.

These three questions define almost every workflow in software systems.

---

# Relationship with Previous Lessons

The architecture now contains several independent layers of responsibility.

```
User Intent

↓

Command

↓

State Machine

↓

Use Case

↓

Strategy

↓

Repository

↓

Transport

↓

Infrastructure
```

Each layer answers a different architectural question.

This separation makes the system easier to evolve.

---

# State Machine vs Strategy

State Machine answers:

> Where is the workflow?

Strategy answers:

> Which algorithm should be used?

Example:

```
Current State

↓

Unlocking
```

Strategy decides:

```
BLE

Cloud

Matter

UWB
```

The workflow remains in the same business state while the strategy changes.

---

# State Machine vs Use Case

Use Case orchestrates business responsibilities.

State Machine models business behavior over time.

The Use Case often owns or collaborates with the State Machine.

---

# Hierarchical State Machines

Large systems often contain nested State Machines.

Example:

```
Unlocking
    │
    ├── Connecting
    ├── Sending Command
    ├── Waiting For Response
    └── Processing Result
```

This approach avoids creating one giant State Machine with hundreds of states.

Hierarchical State Machines are common in:

- Bluetooth stacks
- Firmware updates
- Automotive software
- Aerospace systems
- Media players

---

# Common Mistakes

❌ Modeling failure reasons as states.

❌ Creating one state per implementation.

❌ Using dozens of boolean flags.

❌ Mixing Commands and Events.

❌ Allowing impossible state combinations.

---

# Interview Questions

### Q1

Why are State Machines preferable to multiple boolean flags?

---

### Q2

What is the difference between a State and an Event?

---

### Q3

Why shouldn't failure reasons be modeled as states?

---

### Q4

What is state explosion and how can it be avoided?

---

### Q5

How do State Machines and Strategy Pattern complement each other?

---

### Q6

Where should a State Machine live in Clean Architecture?

---

# Key Takeaways

- State Machines model behavior, not data.
- A workflow should exist in one business state at a time.
- Events describe facts.
- Commands express intent.
- Failure Reasons are data, not states.
- State Machines remain stable while implementations evolve.
- Fewer, richer business states are preferable to many implementation-specific states.

---

# Evolution of My Thinking

## Before this lesson

- I modeled workflows using multiple flags and many implementation-specific states.
- I mixed events, states and error conditions together.
- I thought every failure condition should become a separate state.

## After this lesson

- I understand that States represent where the workflow currently is.
- I understand that Events represent facts that trigger evaluation.
- I understand that Failure Reasons are data carried by a failure outcome rather than separate states.
- I know how to avoid state explosion by modeling business states instead of implementation details.
- I now think in terms of behavior over time rather than sequences of if-else statements.

---

# Summary

State Machines provide a structured way to model business workflows by representing the system as a finite set of business states connected by transitions.

Rather than relying on boolean flags and nested conditional logic, State Machines separate States, Events, Transitions and Failure Reasons, resulting in software that is easier to understand, test, extend and maintain.

This lesson also established the relationship between Commands, Events, Use Cases, Strategy Pattern and State Machines, forming another foundational building block in designing production-grade software architectures.
