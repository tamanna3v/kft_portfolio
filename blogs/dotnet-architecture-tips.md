# Clean Architecture Tips for .NET Teams

After 14+ years shipping enterprise .NET systems, a few structural principles
show up again and again in the codebases that stay healthy. Here are the ones
that pay off the most.

![Layered architecture](https://images.unsplash.com/photo-1517134191118-9d595e4c8c2b?auto=format&fit=crop&w=1200&q=70)

## 1. Depend Inward, Never Outward

Your **domain** should not know about EF Core, HTTP, or the file system. Push
those concerns to the edges and let the core depend only on abstractions.

```
Api  ->  Application  ->  Domain
                ^              ^
         Infrastructure -------
```

## 2. One Project Per Responsibility

A predictable solution layout removes a whole category of "where does this go?"
debates:

- **Domain** — entities, value objects, domain events
- **Application** — use cases, interfaces, DTOs
- **Infrastructure** — EF Core, messaging, external APIs
- **Api** — controllers, middleware, composition root

## 3. Keep Use Cases Thin

Each use case does one thing. When a handler starts branching heavily, that's a
signal to split it.

> A method that fits on one screen is a method the next engineer can trust.

## 4. Make the Database an Implementation Detail

Interfaces live in **Application**; implementations live in **Infrastructure**.
Swapping SQL Server for PostgreSQL should never ripple into your business logic.

| Layer | Knows about DB? |
| --- | --- |
| Domain | No |
| Application | Only the interface |
| Infrastructure | Yes |

## 5. Automate the Boring Guardrails

- Analyzers and `.editorconfig` for consistent style
- Architecture tests (e.g. NetArchTest) to enforce dependency rules
- CI that fails fast on a broken build or red test

---

Clean architecture isn't about ceremony — it's about making the **right thing
the easy thing** for every engineer who touches the code after you.
