# Lesson 1 – What is System Design?

## Learning Objectives

After completing this lesson, you should be able to:

- Understand what System Design actually means.
- Differentiate software architecture from feature implementation.
- Identify the five logical layers of a mobile application.
- Understand the responsibilities of each layer.
- Start thinking like an architect rather than an implementer.

---

# What is System Design?

System Design is the process of organizing software components so that they work together to satisfy both functional and non-functional requirements.

Most developers think System Design means designing backend services.

In reality, System Design applies to every software system, including mobile applications.

A feature is considered complete only when it satisfies quality attributes such as:

- Scalability
- Reliability
- Security
- Performance
- Maintainability
- Testability
- Observability

---

# Thinking Like an Architect

Developers usually ask:

> How do I implement this feature?

Architects ask:

- Where should this responsibility live?
- What happens if a dependency fails?
- How will this feature evolve?
- Can this design support future requirements?
- Is the system easy to test?
- Can another team extend it?

Architecture is the art of assigning responsibilities to the correct components.

---

# Five Layers of a Mobile Application

## 1. Presentation Layer

Responsible for user interaction.

Responsibilities:

- Display UI
- Receive user input
- Show loading indicators
- Display errors
- Render state

Should not know:

- BLE
- HTTP
- Database
- Business rules

---

## 2. Business Layer

Responsible for business workflows.

Examples:

- Unlock Door
- Lock Door
- Share Access
- Update Firmware

Typical components:

- Use Cases
- Domain Services
- Validators

Should not know:

- SwiftUI
- UIKit
- CoreBluetooth
- URLSession

---

## 3. Data Layer

Responsible for providing data to the business layer.

Examples:

- Lock Repository
- User Repository
- Home Repository

Responsibilities:

- Retrieve data
- Persist data
- Coordinate different data sources

---

## 4. Infrastructure Layer

Responsible for technical implementation.

Examples:

- BLE
- Networking
- Database
- Analytics
- Logging
- Feature Flags
- Crash Reporting

---

## 5. External Systems

Everything outside the application.

Examples:

- Smart Lock
- Cloud
- APNs
- Authentication Server

---

# Unlock Door Example

A simplified architecture:

User taps Unlock

↓

Presentation Layer

↓

UnlockDoorUseCase

↓

LockRepository

↓

UnlockTransport

↓

BLE / Cloud / Wi-Fi

↓

Smart Lock

---

# Responsibilities

The Presentation Layer should only express user intent.

Example:

"The user wants to unlock the door."

It should never decide:

- Which transport to use.
- Whether BLE is connected.
- How packets are constructed.

Those are responsibilities of lower layers.

---

# Quality Attributes

Every architecture decision should be evaluated against:

- Maintainability
- Reliability
- Performance
- Security
- Scalability
- Observability
- Testability
- Cost

---

# Key Principles

- Separate responsibilities.
- Keep business rules independent of technology.
- Design for future change.
- Think in workflows instead of individual classes.
- Protect business logic from infrastructure changes.

---

# Common Mistakes

❌ Putting business logic inside ViewModels.

❌ Letting Views communicate directly with BLE.

❌ Mixing analytics, networking and UI.

❌ Tight coupling between layers.

---

# Key Takeaways

System Design is not backend design.

Architecture is responsibility management.

Every component should have a clear purpose.

Business logic should remain independent of technology.

Technology changes frequently.

Business capabilities change much more slowly.

---

# What's Next

Lesson 2 explores the two most fundamental architecture concepts:

- Abstraction
- Separation of Concerns
