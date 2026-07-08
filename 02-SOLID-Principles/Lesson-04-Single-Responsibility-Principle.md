# Lesson 4 – Single Responsibility Principle (SRP)

> "A module should have one, and only one, reason to change."
>
> — Robert C. Martin

---

# Learning Objectives

After this lesson you should be able to:

- Explain the true meaning of SRP.
- Distinguish responsibilities from implementation.
- Identify multiple reasons for change.
- Decompose a feature into cohesive components.
- Apply SRP to large mobile applications.

---

# Introduction

The Single Responsibility Principle (SRP) is one of the most misunderstood principles in software engineering.

Many developers simplify it as:

> "A class should do one thing."

While intuitive, this definition is incomplete.

The real definition is:

> **A class should have only one reason to change.**

The focus is not on the number of methods or lines of code.

The focus is on **ownership of change**.

---

# Responsibility vs Functionality

Consider this class:

```swift
class SmartArrivalManager {

  func detectLocation()
  
  func validatePermissions()
  
  func validateBattery()
  
  func unlockDoor()
  
  func sendNotification()
  
  func logAnalytics()
  
  func saveAudit()
}
```

At first glance it appears cohesive because everything relates to Smart Arrival.

However, every method answers to a different stakeholder.

This means the class has multiple reasons to change.

---

# Identifying Responsibilities

The Smart Arrival feature can be decomposed into the following responsibilities.

| Component | Responsibility | Reason to Change |
|-----------|----------------|------------------|
| SmartArrivalUseCase | Coordinates the Smart Arrival workflow | Business workflow changes |
| LocationService | Detects user proximity | Location algorithm changes |
| PermissionValidationService | Validates user permissions | Permission policy changes |
| LockValidationService | Validates lock state and battery | Lock validation rules change |
| LockRepository | Executes lock operations | Lock communication or persistence changes |
| AnalyticsService | Records analytics events | Analytics requirements change |
| AuditLogService | Stores audit events | Audit policy changes |
| NotificationService | Sends local notifications | Notification behavior changes |

Each component owns a single responsibility.

---

# Orchestration

An important observation is that decomposing responsibilities does **not** eliminate orchestration.

Someone still needs to coordinate the workflow.

That responsibility belongs to the **SmartArrivalUseCase**.

Example workflow:

```
Location Detected

↓

Validate Permission

↓

Validate Lock

↓

Unlock Door

↓

Analytics

↓

Audit Log

↓

Notification
```

The UseCase coordinates the sequence without performing the individual tasks.

---

# Why SRP Matters

Without SRP:

- Large classes become difficult to understand.
- Multiple developers modify the same file.
- Regression bugs increase.
- Testing becomes harder.
- Reuse decreases.

With SRP:

- Components evolve independently.
- Teams work in parallel.
- Testing becomes simpler.
- Responsibilities become obvious.

---

# Architecture Review

When reviewing a design, avoid asking:

> "Does this class do one thing?"

Instead ask:

> "Who would ask me to change this class?"

If the answer includes:

- Product Team
- UX Team
- Analytics Team
- Security Team

then the class probably violates SRP.

---

# Trade-offs

Applying SRP usually introduces more classes.

Advantages:

- Better maintainability
- Easier testing
- Improved readability
- Independent evolution

Disadvantages:

- More files
- Slightly higher initial complexity
- Requires disciplined naming

Good architecture accepts small upfront complexity to reduce long-term maintenance costs.

---

# Common Mistakes

❌ Creating "Manager" classes that perform unrelated work.

❌ Combining Analytics and Audit.

❌ Putting business rules inside ViewModels.

❌ Treating large classes as acceptable because "everything belongs to one feature."

---

# Architect's Insight

Responsibilities are not discovered by counting methods.

They are discovered by identifying independent reasons to change.

This is why experienced architects organize software around responsibilities instead of screens or frameworks.

---

# Interview Questions

### Q1

Explain SRP using a real project.

### Q2

How do you identify multiple responsibilities?

### Q3

Can a class with one hundred methods still satisfy SRP?

### Q4

Why is orchestration separated from execution?

### Q5

What is the difference between functionality and responsibility?

---

# Key Takeaways

- SRP is about reasons to change.
- Orchestration is itself a responsibility.
- Components should evolve independently.
- Large features should be decomposed into cohesive responsibilities.
- Naming responsibilities correctly is one of an architect's most valuable skills.

---

# Summary

The Single Responsibility Principle encourages software to be organized around stable responsibilities rather than collections of related functions.

Correct application of SRP produces systems that are easier to understand, maintain, test, and extend.
