# Lesson 7 – Designing an Unlock Feature

> "Great architecture begins by understanding responsibilities, not by creating classes."

---

# Learning Objectives

After completing this lesson you should be able to:

- Analyze a product requirement before writing code.
- Identify business responsibilities.
- Separate business capabilities from implementation details.
- Group responsibilities into architectural layers.
- Reason about change frequency.
- Design a feature using architectural thinking rather than framework thinking.

---

# Problem Statement

Design a scalable Unlock feature for a smart lock application with the following requirements:

- Unlock via BLE
- Unlock via Cloud
- Unlock via Matter
- Future support for UWB, Apple Watch, Siri
- Optional Face ID or PIN authentication
- Retry over Cloud if BLE fails
- Analytics logging
- Audit logging
- Local notifications
- Feature flags
- A/B testing
- Future business rule changes

---

# First Principle

Do **not** begin by creating classes.

Architects first understand:

- Responsibilities
- Business workflow
- Ownership
- Reasons to change

Only afterwards do they design components.

---

# Business Workflow

The unlock process can be expressed as a sequence of business responsibilities:

1. User initiates unlock.
2. Determine whether unlocking is allowed.
3. Select the appropriate transport.
4. Execute unlock.
5. Handle failures and retries.
6. Record analytics.
7. Record audit events.
8. Notify the user.
9. Refresh application state.

Thinking in terms of workflow prevents premature implementation decisions.

---

# Responsibilities Identified

## Presentation Layer

### Unlock Screen

Responsible for:

- Displaying unlock controls.
- Receiving user interaction.
- Presenting loading and error states.

---

## Application Layer

### UnlockUseCase

Responsible for orchestrating the complete unlock workflow.

It coordinates collaborators but performs no infrastructure work itself.

---

### UnlockEligibilityPolicy

Responsible for determining whether unlocking may proceed.

Possible inputs include:

- User permissions
- Authentication status
- Lock ownership
- Time restrictions
- Guest access
- Subscription level

Authentication is only one input into this decision.

---

### TransportSelectionStrategy

Responsible for selecting the appropriate communication method.

Possible transports:

- BLE
- Cloud
- Matter
- UWB
- Future technologies

This responsibility changes as product requirements evolve.

---

### RetryPolicy

Responsible for determining retry behavior after failures.

Retry logic should be reusable across features rather than being specific to unlocking.

---

### LockRepository

Represents operations on the Lock domain object.

Possible responsibilities include:

- Unlock
- Lock
- Refresh status
- Retrieve state

The repository hides infrastructure details from the application layer.

---

# Domain Layer

The Domain models business concepts rather than technologies.

Examples include:

- Lock
- UnlockCommand
- UnlockResult
- Transport abstraction

The Domain should remain unaware of:

- BLE SDK
- Matter SDK
- URLSession
- LocalAuthentication
- Analytics SDKs

---

# Infrastructure Layer

Responsible for technical implementation.

Examples:

### Transport Implementations

- BLETransport
- CloudTransport
- MatterTransport
- Future transport implementations

---

### Authentication Gateway

Communicates with platform APIs such as Apple's LocalAuthentication framework.

The gateway performs authentication.

It does not decide whether authentication is required.

---

### Analytics Provider

Responsible for communicating with analytics platforms.

---

### Audit Provider

Responsible for persisting audit information.

---

### Notification Provider

Responsible for delivering user notifications.

Notification delivery should remain independent of business policy.

---

# Responsibility Discovery

One of the key lessons from this exercise was learning to identify responsibilities before identifying classes.

Example:

Instead of immediately creating:

```
UnlockAuthenticationService
```

Think first:

> "Who owns the decision that unlocking is allowed?"

That question leads to:

```
UnlockEligibilityPolicy
```

The implementation may later collaborate with:

```
AuthenticationGateway
```

This separation keeps business policy independent of platform APIs.

---

# Reasons to Change

Understanding why a component changes is more valuable than counting methods.

| Component | Likely Reason to Change |
|-----------|-------------------------|
| UnlockEligibilityPolicy | Product business rules |
| TransportSelectionStrategy | Communication policy |
| RetryPolicy | Retry requirements |
| LockRepository | Lock operations |
| AuthenticationGateway | Apple authentication APIs |
| BLETransport | BLE implementation |
| AnalyticsProvider | Analytics platform |
| NotificationProvider | Notification platform |

---

# Architectural Layers

```
Presentation

↓

Application

↓

Domain

↓

Infrastructure
```

Dependencies always point inward.

Business logic must remain independent of technical implementation.

---

# Architect's Mindset

Rather than asking:

> "Which class should contain this code?"

Ask:

- Who owns this knowledge?
- Who is allowed to change it?
- Which layer should contain it?
- Why?

These questions naturally lead to better architecture.

---

# Common Mistakes

❌ Starting with classes instead of responsibilities.

❌ Mixing business rules with platform APIs.

❌ Allowing infrastructure details to leak into the Domain.

❌ Creating services without clearly defined responsibilities.

❌ Naming components after implementations rather than business concepts.

---

# Interview Questions

### Q1

How would you design a scalable Unlock feature?

### Q2

Why should responsibilities be identified before classes?

### Q3

How do you distinguish business policy from infrastructure?

### Q4

Why should retry logic be reusable?

### Q5

What belongs inside the Application layer?

### Q6

How would you support new transport technologies without modifying business logic?

---

# Key Takeaways

- Responsibilities come before classes.
- Business workflow should be understood before implementation.
- Architecture is organized around ownership of knowledge.
- Policies and gateways are different responsibilities.
- Stable business logic should remain isolated from changing technologies.

---

# Evolution of My Thinking

## Before this lesson

- I tended to think in terms of services and classes first.
- I often named components based on implementation details.
- I focused on where code should be written.

## After this lesson

- I begin by identifying business responsibilities.
- I distinguish policies from gateways and implementations.
- I think about ownership before implementation.
- I evaluate components by their reasons to change.
- I understand that classes emerge naturally from well-defined responsibilities.

---

# Summary

This lesson introduced the process of designing an entire feature from first principles.

Instead of beginning with classes or design patterns, the design started by understanding business responsibilities, grouping them into architectural layers, identifying reasons to change, and separating policies from technical implementations.

This approach leads to software that is easier to evolve, test, and maintain as product requirements continue to grow.
