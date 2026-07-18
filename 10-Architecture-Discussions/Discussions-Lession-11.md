# Discussion Notes – Lesson 11

## Key Discussion 1 – The Restaurant Analogy

A chef should cook.

A chef should not build the kitchen.

Likewise:

A UseCase should execute business logic.

It should not create repositories.

---

## Key Discussion 2 – DI vs IoC

Dependency Injection:

A technique.

Inversion of Control:

The architectural principle.

DI is one implementation of IoC.

---

## Key Discussion 3 – Construction-Time vs Runtime Decisions

Construction-time:

- Repository implementation
- Logger
- EventBus
- Analytics provider

Runtime:

- Bluetooth disabled
- Retry over Cloud
- Lock jammed
- Battery low

Construction belongs to the Composition Root.

Runtime decisions belong to Strategies, State Machines, Policies and UseCases.

---

## Key Discussion 4 – Feature Flags

Requirement:

Enable AI transport selection for 5% of users.

Architecture:

Composition Root

↓

Creates strategies

↓

Injects into TransportSelectionStrategy

↓

Runtime evaluation uses:

- RemoteConfigService
- User rollout
- Feature flags

The Composition Root provides available strategies.

The Strategy decides which strategy to execute.

---

## Architectural Insight

The Composition Root answers:

"What components exist?"

The Strategy answers:

"Which component should be used now?"

Business objects execute business behavior.
