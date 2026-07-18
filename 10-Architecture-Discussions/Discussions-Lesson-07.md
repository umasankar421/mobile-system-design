# Discussion Notes – Lesson 7

## Key Discussion 1 – Where should transport selection happen?

The UseCase should not decide between BLE, Cloud, or Matter.

Instead:

UnlockUseCase

↓

TransportSelectionStrategy

↓

BLE

Cloud

Matter

---

## Key Discussion 2 – Runtime Decision Making

Transport selection depends on runtime conditions.

Examples:

- Bluetooth enabled?
- Internet available?
- User preference?
- Retry policy?

Therefore this logic belongs in a Strategy, not the Composition Root.

---

## Key Discussion 3 – Feature Evolution

As new transports are introduced:

- BLE
- Cloud
- Matter
- Apple Watch
- Siri
- UWB

The UseCase remains unchanged.

Only Strategy implementations evolve.

---

## Architectural Insight

Strategies own runtime decisions.

Composition Roots own construction-time decisions.
