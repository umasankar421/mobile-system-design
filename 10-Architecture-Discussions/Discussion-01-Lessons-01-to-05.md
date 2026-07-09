# Architecture Discussion Archive #1
>


# Lessons 1–5 (Foundations, Responsibilities, Dependency Rule, SRP & OCP)
This document captures the architectural discussions, reasoning process, design reviews, and insights that occurred during the lessons. Unlike the lesson chapters, this document records why architectural decisions were made and how the design evolved.

## **Objective**
The goal of these discussions was not to memorize design patterns or architecture terminology, but to develop the mindset of an architect by reasoning through real-world product requirements.

The working example throughout these discussions was the Schlage Home Smart Lock application.



## **Discussion 1 – Understanding a Use Case**
Initial Understanding
The Unlock Door feature was initially designed as:

```
SwiftUI View
ViewModel
Repository
BLE Layer
Lock
```

The ViewModel performed business validation and delegated the unlock operation to the repository.

First Architectural Insight
A Use Case represents a business capability, not a communication technology.

The responsibility of UnlockDoorUseCase is:

Coordinate the unlock workflow.
Decide whether unlocking should proceed.
Delegate execution to collaborating components.
It should not perform BLE communication.

## **Discussion 2 – Unlock Repository vs Transport**
Initially the repository was designed as:

```
UnlockRepository

• unlockViaBLE()

• unlockViaCloud()

• unlockViaWiFi()

• unlockViaMatter()

```

**Discussion**
Should one repository own every communication technology?

The answer evolved into a more flexible architecture.

Instead of the business workflow knowing about communication technologies, an abstraction should represent the capability:

```
UnlockTransport

↓

BLE

Cloud

Matter

UWB
```

The business workflow expresses only one intention:

Unlock the door.

How that happens becomes an infrastructure concern.

## Discussion 3 – Firmware Update Architecture
The firmware update feature introduced several components:

```
FirmwareUpdateUseCase
FirmwareValidationService
FirmwareDownloadService
FirmwareTransfer
FirmwareDataConverter
```

Important Insight
Downloading firmware and transferring firmware are different responsibilities.

Downloading retrieves the binary.

Transferring delivers the binary to the lock.

Data conversion prepares packets for transfer.

Each responsibility changes for different reasons.

## **Discussion 4 – Repository Naming**
A question arose:

Why is UnlockRepository acceptable while the firmware feature suggested FirmwareTransferClient?

Conclusion
Names should represent responsibilities.

If a component only transfers bytes, “Transfer” is more precise than “Repository.”

Repositories represent access to domain concepts.

Clients usually represent communication with external systems.

Naming should communicate intent rather than implementation.

## **Discussion 5 – What Is the Domain?**
A recurring question was:

Where does the Domain exist?

The answer:

The Domain represents business concepts.

Examples:
```
Lock
User
Home
Access
Firmware Update
Unlock Door
```

The Domain should not know:
```
BLE
CoreBluetooth
Matter
HTTP
URLSession
```

Technology is an implementation detail.

Business concepts should remain stable.

## **Discussion 6 – Replacing BLE with UWB**
Scenario:

Product decides that nearby unlock should use UWB instead of BLE.

Components that remain stable
```
UnlockDoorUseCase
Lock Domain
Business workflow
Components that evolve
UWB transport implementation
Transport registration
Connectivity layer
Key Insight
```

Business capability remains:

```
Unlock the door.
```

Communication technology changes independently.

## **Discussion 7 – Business Decisions vs Technical Decisions**
One of the most important discussions in the academy.

Business decisions include:

Guest users cannot unlock remotely.
Premium users may use Cloud.
Face ID required after inactivity.
Technical decisions include:

RSSI too weak.
BLE unavailable.
Retry over Wi-Fi.
Matter preferred.
Key Principle
Business knowledge belongs in business layers.

Infrastructure knowledge belongs in infrastructure layers.

## **Discussion 8 – Information Expert**
A key GRASP principle introduced during discussion.

Instead of asking:

Where should this code go?

Ask:

Which component already possesses the knowledge required to make this decision?

Examples:

Business Layer

Permissions
User role
Subscription
Infrastructure

RSSI
Packet loss
Bluetooth availability
This became one of the core decision-making techniques used throughout later lessons.

## **Discussion 9 – Single Responsibility Principle**
Initial observation:

The junior engineer created one large SmartArrivalManager.

Responsibilities included:

```
GPS
Validation
Unlocking
Analytics
Audit
Notifications
First decomposition
```

Responsibilities were separated into:

```
LocationService
PermissionValidationService
LockValidationService
AnalyticsService
AuditLogService
NotificationService
LockRepository
```
Important correction

Removing responsibilities does not remove orchestration.

Someone still owns the workflow.

That component became:

SmartArrivalUseCase
The Use Case coordinates.

Collaborators execute.

## **Discussion 10 – Understanding Reasons to Change**
The academy shifted from thinking about classes to thinking about ownership of change.

Instead of asking:

How many methods does this class have?

We started asking:

Who would request a change to this component?

Examples:

Analytics Team

↓

AnalyticsService

Security Team

↓

PermissionValidationService

Connectivity Team

↓

Transport Layer

Product Team

↓

Business Workflow

This became the practical interpretation of the Single Responsibility Principle.

## **Discussion 11 – Notification Ownership**
Scenario:

After 10 PM, Auto Unlock should send a silent notification.

The discussion explored two interpretations.

Interpretation 1
Product policy:

Auto Unlock after 10 PM must be silent.

Business layer decides.

NotificationService executes.

Interpretation 2
Platform policy:

Every notification after 10 PM should be silent.

NotificationService owns the rule.

Lesson
Requirements must be clarified before deciding architectural ownership.

## **Discussion 12 – Open/Closed Principle**
A transport abstraction was introduced.

```
UnlockDoorUseCase

↓

UnlockTransport

↓

BLE

Cloud

Matter

UWB
```


Adding new technologies should require new implementations rather than modifying stable business logic.

This became the practical understanding of the Open/Closed Principle.

## **Discussion 13 – Transport Selection**
A deeper question emerged:

Who chooses the transport?

Possible answers included:

Use Case
Repository
Dedicated Resolver
Final understanding
Business layers choose business policies.

Infrastructure chooses technical routing.

This naturally introduced the concept of a Transport Resolver.

## **Discussion 14 – Thinking Like an Architect**
One of the most valuable mindset shifts.

Developers often ask:

How do I implement this feature?

Architects ask:

What existing code will I have to modify?

Good architecture minimizes changes to stable code.

**Discussion 15 – Multiple Lock Unlock**
Question:

What happens if Product wants to unlock multiple locks simultaneously?

Initial idea:

Create multiple UseCase instances.

Discussion outcome:

A Use Case represents a business capability, not a single lock.

Possible future requirements may introduce:

Parallel execution
Atomic operations
Retry
Cancellation
This demonstrated how new requirements influence abstractions.

Personal Learning Milestones
During these lessons several important mindset shifts occurred.

Initial Thinking
Focus on classes.
Focus on call sequences.
Focus on implementation.
Current Thinking
Responsibilities.
Dependency direction.
Stable vs unstable components.
Reasons to change.
Business vs technical decisions.
Long-term evolution.
This transition represents the beginning of architectural thinking.

# Architect Badges Earned
## **🏅 Architect Badge #1 — Responsibility Thinker**
Awarded for:

Separating business concepts from technology.
Protecting the domain from infrastructure.
Identifying stable dependencies.
## **🏅 Architect Badge #2 — Change Analyst**
Awarded for:

Organizing software around reasons to change.
Identifying responsibility boundaries.
Evaluating ownership of change.
My Personal Notes (Umasankar)
These are the mindset shifts I want to remember throughout this journey:

A Use Case orchestrates; it does not perform the work.
The Domain should never know communication technologies.
Business rules and technical rules belong to different layers.
Responsibilities are discovered through reasons to change.
Good abstractions survive future requirements.
Architecture is about making future changes inexpensive.
Every new requirement should begin with the question: “Who owns the knowledge required to make this decision?”
The best architecture discussions are about trade-offs, not about finding a single “correct” answer.
Looking Ahead

The next phase of the academy will cover:

Dependency Injection
Composition Root
Object Lifetime
Strategy Pattern
Plugin Architecture
Modularization
Concurrency
Large-Scale iOS Architecture
The objective is to evolve from an experienced Lead iOS Engineer into an architect capable of designing systems that remain maintainable as products, technologies, and engineering teams continue to grow.
