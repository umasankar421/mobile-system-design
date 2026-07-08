# Lesson 5 – Open/Closed Principle (OCP)

> "Software entities should be open for extension but closed for modification."
>
> — Bertrand Meyer

---

# Learning Objectives

After completing this lesson you should be able to:

- Understand the motivation behind OCP.
- Design extensible systems.
- Separate business capabilities from implementations.
- Apply OCP to communication protocols.
- Understand the relationship between OCP and abstractions.

---

# Introduction

Modern software evolves continuously.

New features should be added with minimal impact on existing, stable code.

The Open-Closed Principle achieves this by encouraging systems to grow through extension rather than modification.

---

# The Problem

Consider an application supporting two communication protocols.

```
BLE

Cloud
```

A naïve implementation might contain:

```
if transport == BLE

else if transport == Cloud

else if transport == Matter

else if transport == UWB

else if transport == NFC
```

Every new protocol requires modification of existing code.

As the system grows, the central decision point becomes increasingly fragile.

---

# Extending Through Abstraction

A better architecture introduces an abstraction.

```
UnlockDoorUseCase

↓

UnlockTransport

↓

BLETransport

CloudTransport

MatterTransport

UWBTransport

NFCTransport
```

Each communication technology implements the same contract.

The business workflow depends only on the abstraction.

---

# Responsibilities

| Component | Responsibility |
|-----------|----------------|
| UnlockDoorUseCase | Coordinates unlock workflow |
| UnlockTransport | Defines unlock capability |
| BLETransport | Unlocks through BLE |
| CloudTransport | Unlocks through Cloud |
| MatterTransport | Unlocks through Matter |
| UWBTransport | Unlocks through UWB |
| NFCTransport | Unlocks through NFC |

---

# Why This Works

When Product introduces a new transport:

```
SatelliteTransport
```

The existing business workflow remains unchanged.

Only a new implementation is added.

The system grows without modifying stable business logic.

---

# Transport Resolution

One important architectural question remains:

Who selects the transport?

A common solution is to introduce a dedicated resolver.

```
UnlockDoorUseCase

↓

TransportResolver

↓

UnlockTransport

↓

BLE

Cloud

Matter
```

The UseCase expresses the business intention:

> Unlock the door.

The resolver determines the appropriate implementation.

This separation prevents technology knowledge from leaking into business logic.

---

# Business Decisions vs Technical Decisions

Business Decisions:

- Guest users cannot unlock remotely.
- Premium users may use Cloud unlock.

Technical Decisions:

- BLE unavailable.
- RSSI too weak.
- Matter preferred.
- Retry over Wi-Fi.

Business decisions belong in the Application layer.

Technical routing belongs in Infrastructure.

---

# Trade-offs

Advantages:

- Easier extension
- Stable business logic
- Independent protocol development
- Better testability

Disadvantages:

- More abstractions
- Additional interfaces
- Increased design effort

---

# Common Mistakes

❌ Long if-else chains.

❌ Switch statements that grow with every feature.

❌ Business logic selecting communication technologies.

❌ Generic interfaces with unclear intent.

---

# Architect's Insight

Whenever a new requirement arrives, ask:

> "Can I implement this by adding new code instead of modifying existing code?"

If the answer is yes, your architecture is probably moving in the right direction.

---

# Looking Beyond OCP

In very large systems, even the transport resolver should not require modification whenever a new protocol is added.

This naturally leads to plugin architectures where new implementations register themselves without changing existing code.

This topic will be explored later in the academy.

---

# Interview Questions

### Q1

Explain OCP using a real mobile application.

### Q2

How would you add Matter support without changing existing business logic?

### Q3

Why are abstractions important for extensibility?

### Q4

Should business logic decide which communication protocol to use?

### Q5

How does OCP improve team scalability?

---

# Key Takeaways

- Extend software rather than modifying stable code.
- Depend on abstractions instead of implementations.
- Separate business decisions from technical routing.
- New communication protocols should require new implementations, not business logic changes.
- OCP enables independent evolution of system components.

---

# Summary

The Open-Closed Principle allows software to evolve safely by protecting stable business logic while enabling new capabilities through extension.

Well-designed abstractions form the foundation of extensible architectures.
