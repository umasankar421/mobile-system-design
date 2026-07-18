# Discussion Notes – Lesson 10

## Key Discussion 1 – EventBus Responsibilities

The Event Bus should:

- Register subscribers
- Route events
- Dispatch events
- Manage subscriber lifecycle
- Isolate failures
- Support predictable delivery

---

## Key Discussion 2 – Responsibilities It Should Avoid

The Event Bus should never:

- Know BLE
- Know UnlockUseCase
- Apply business rules
- Transform payloads
- Retry network requests

---

## Key Discussion 3 – Recursive Event Publishing

Problem:

A listener publishes another event while handling the current event.

Solution:

Queue events.

Avoid recursive dispatch.

---

## Key Discussion 4 – Snapshot Dispatch

Before dispatching:

Take a snapshot of subscribers.

Dispatch against the snapshot.

This avoids mutating collections during iteration.

---

## Architectural Insight

A good Event Bus knows everything about events and nothing about the business.
