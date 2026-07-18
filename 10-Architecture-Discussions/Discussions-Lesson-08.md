# Discussion Notes – Lesson 8

## Key Discussion 1 – States vs Events

A common misconception is treating them as the same.

State:

A long-lived condition.

Examples:

- Authenticating
- Unlocking
- Failed

Event:

A discrete occurrence.

Examples:

- UnlockButtonPressed
- AuthenticationSucceeded
- BLETimeout

---

## Key Discussion 2 – State Machine Responsibilities

The State Machine determines:

- Valid transitions
- Illegal transitions
- Recovery paths

It does not execute business logic directly.

---

## Key Discussion 3 – Retry Flow

Retry over Cloud should be modeled as a transition.

Not as nested if/else statements.

---

## Architectural Insight

Events trigger transitions.

Transitions produce new states.

States may emit new events.
