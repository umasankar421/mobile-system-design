# Discussion Notes – Lesson 9

## Key Discussion 1 – Why Event-Driven Architecture?

As Product adds features:

Analytics

Notifications

Widgets

Cloud Sync

AI

Audit Logs

The UseCase should not continue growing.

Instead:

Publish:

UnlockSucceeded

Listeners react independently.

---

## Key Discussion 2 – Commands vs Events

Command:

Represents intent.

Example:

UnlockDoor

Event:

Represents something that already happened.

Example:

UnlockSucceeded

---

## Key Discussion 3 – Many Listeners

Question:

"What happens if we have thirty-five listeners?"

Answer:

Classify listeners.

Examples:

Critical

- Cloud Sync
- Audit

User Experience

- Widget
- Notification

Analytics

- Metrics

Experimental

- AI
- Marketing

---

## Architectural Insight

Adding a new capability should typically require adding a new listener rather than modifying the UseCase.
