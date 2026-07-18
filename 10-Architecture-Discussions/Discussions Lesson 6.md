# Discussion Notes – Lesson 6

## Key Discussion 1 – Why not create dependencies inside UseCases?

Creating dependencies inside business objects introduces multiple problems:

- Tight coupling to implementations
- Difficult unit testing
- Reduced reusability
- Violation of the Open/Closed Principle
- Business logic becomes responsible for object creation

The deeper architectural insight:

> Business objects should solve business problems, not construct object graphs.

---

## Key Discussion 2 – Does Composition Root violate the Open/Closed Principle?

Question:

"If I modify the Composition Root when requirements change, isn't that violating OCP?"

Answer:

No.

The Composition Root is expected to evolve because its responsibility is assembling the application.

It is intentionally one of the most change-prone parts of the system.

Business components should remain stable while the Composition Root changes.

---

## Key Discussion 3 – Multiple ViewModels

Question:

"If the application contains twenty ViewModels, should there be one huge Composition Root?"

Answer:

No.

Split the Composition Root into feature modules.

Example:

AppCompositionRoot

↓

UnlockModuleCompositionRoot

FirmwareModuleCompositionRoot

SettingsModuleCompositionRoot

Each feature owns its dependency graph.

---

## Key Discussion 4 – Multiple Transport Implementations

Requirement:

Support BLE and Matter simultaneously.

Instead of changing the UseCase:

Introduce:

TransportSelectionStrategy

The Composition Root provides all transport implementations.

The Strategy chooses which one to execute.

---

## Architectural Insight

The Composition Root answers:

"What components exist?"

Business objects answer:

"What should happen?"
