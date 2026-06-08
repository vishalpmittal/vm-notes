# 12 OOP Concepts

A reference of the twelve object-oriented programming concepts every developer should know — class/object basics through the four kinds of relationship between classes.

## Key Takeaways

- The four pillars are still the canonical foundation: **encapsulation, abstraction, inheritance, polymorphism** — every other concept rests on these
- The four kinds of class-to-class relationship (**association, aggregation, composition, dependency**) distinguish "knows-about," "has-a-but-can-survive-alone," "has-a-and-owns," and "temporarily uses"
- **Interfaces define contracts, classes provide implementations** — realization is the relationship between them
- Composition vs aggregation matters most for **lifecycle ownership** — if the parent dies, do the parts die with it?
- Modern languages blur these distinctions (traits, mixins, protocols, type classes), but the underlying concepts remain useful for reasoning about design

## Foundations (1-3)

### 1. Classes
A blueprint that defines the structure and behavior of objects.
```
class User {
  username: string
  email: string
  role: Role
}
```

### 2. Objects
A concrete instance of a class with actual values.
```
alice = new User("alice", "alice@x.com", Role.Admin)
bob   = new User("bob",   "bob@x.com",   Role.User)
```

### 3. Interfaces
A contract defining required methods without implementation details.
```
interface PaymentGateway {
  charge(amount: Money): Result
  refund(txn: TransactionId): Result
}
```

## The Four Pillars (4-7)

### 4. Encapsulation
Bundling data and methods together while restricting direct access to internal state. Example: a `RateLimiter` with private counters mutated only through `tryAcquire()` — callers can't corrupt the internal state.

### 5. Abstraction
Hiding unnecessary complexity and exposing only what the user needs. Example: a `CloudStorage` base class that simplifies the differences between S3, GCS, and Azure Blob `upload()` calls — callers don't need to know which backend they're using.

### 6. Inheritance
A new class derives from an existing class, inheriting its fields and methods.
```
class DomainEvent { timestamp, eventId, ... }
class UserRegisteredEvent extends DomainEvent { userId, email }
```

Use cautiously — composition is often a better fit (see #10).

### 7. Polymorphism
Objects of different types treated through a common interface, each providing its own behavior.
```
NotificationChannel channels = [EmailChannel, SlackChannel, SmsChannel]
for (channel of channels) channel.send(msg)  // each sends differently
```

The classic LSP/Liskov rule applies: subtypes should be substitutable for their supertype without surprising the caller.

## Relationship Types (8-11)

The "what's the relationship between Class A and Class B?" axes — distinguished by ownership and lifetime.

### 8. Association
A "knows-about" relationship where two objects reference each other but exist independently. Example: a `Developer` and a `Repository` — the developer can leave, the repository remains; the repository can be archived, the developer remains.

### 9. Aggregation
A "has-a" relationship where parts exist independently of the whole. Example: a `Team` contains `Microservice` instances, but those microservices can be reassigned to a different team. Team-disbanded ≠ services-deleted.

### 10. Composition
A strong "has-a" where the whole owns the parts entirely. If the whole is destroyed, the parts are too. Example: an `Order` contains `LineItem`s — cancel the order, the line items cease to exist (no other order owns them).

### 11. Dependency
A temporary "uses-a" connection through method parameters or local variables. Example: a `DeploymentService.deploy(client: HttpClient)` — the service uses an `HttpClient` for the duration of the call but doesn't own one.

### Relationship Strength Hierarchy

```
Dependency  <  Association  <  Aggregation  <  Composition
(weak, transient)  →  →  →  →  →  →  →  →  →  →  (strong, owns lifecycle)
```

## The Class-Interface Link (12)

### 12. Realization
The relationship between an interface and its implementing class. Example: `RedisCache` implementing the `CacheStore` contract. The implementation has to satisfy every method the contract requires.

## Why These Still Matter

Modern languages have blurred OOP boundaries:
- **Traits / mixins** (Rust, Scala, Python) — multiple "inheritance" without the diamond problem
- **Protocols** (Swift) — interfaces with default implementations
- **Type classes** (Haskell) — polymorphism without inheritance

But the underlying *concepts* — encapsulation as data hiding, polymorphism as substitutability, composition as lifecycle ownership — remain the vocabulary for reasoning about design. SOLID principles (single responsibility, open/closed, Liskov, interface segregation, dependency inversion) are essentially these concepts applied as design rules.

## Related

- (No related notes yet — `programming/` is a new subfolder; expect future additions on SOLID, design patterns, language-specific paradigms)

---

**Source:** https://blog.algomaster.io/p/12-oop-concepts-every-developer-should-know
**Date:** 2026-06-05
**Tags:** oop, object-oriented-programming, classes, interfaces, inheritance, polymorphism, encapsulation, abstraction, composition, aggregation, solid, programming-fundamentals
