---
name: domain-modeling
description: Build and sharpen a project's domain model. Use when discussing codebase terminology, writing or editing a GLOSSARY.md, or recording or editing an ADR.
---

# Domain Modeling

Actively build and sharpen the project's domain model as you design. This is the *active* discipline: challenging terms, inventing edge-case scenarios, and writing the glossary and decisions down the moment they crystallise. (Merely *reading* `GLOSSARY.md` for vocabulary is not this skill: that's a one-line habit any skill can do. This skill is for when you're changing the model, not just consuming it.)

## File structure

Keep each glossary in the DocC catalog of the module that owns its domain language. In SwiftPM, a target's catalog sits alongside its source files, under `Sources/<Target>/` by default. Use the project's existing catalog name and target path where they differ. [Swift-DocC guide](https://www.swift.org/documentation/docc/documenting-a-swift-framework-or-package)

For a package with a single target and context:

```
/
├── Package.swift
└── Sources/
    └── Ordering/
        └── Documentation.docc/
            ├── GLOSSARY.md
            └── ADR/
                ├── 0001-event-sourced-orders.md
                └── 0002-postgres-for-write-model.md
```

When contexts are owned by different modules, keep each glossary with its module and use a repository-level `GLOSSARY-MAP.md` to link to them. Keep ADRs under `ADR/` in the catalog designated for those records. The repository-level catalog in this example can be built separately from the target catalogs.

```
/
├── Package.swift
├── GLOSSARY-MAP.md
├── Documentation.docc/
│   └── ADR/
└── Sources/
    ├── Ordering/
    │   └── Documentation.docc/
    │       └── GLOSSARY.md
    ├── Billing/
    │   └── Documentation.docc/
    │       └── GLOSSARY.md
    └── Fulfillment/
        └── Documentation.docc/
            └── GLOSSARY.md
```

Create the relevant glossary when the first term is resolved. Create the designated catalog's `ADR/` directory when the first ADR is needed.

## During the session

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in the relevant glossary, call it out immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y. Which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account': do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible. Which is right?"

### Update the glossary inline

When a term is resolved, update the relevant glossary right there. Don't batch these up: capture them as they happen. Use the format in [GLOSSARY-FORMAT.md](./GLOSSARY-FORMAT.md).

Keep glossary entries focused on domain terminology and definitions.

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse**: the cost of changing your mind later is meaningful
2. **Surprising without context**: a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off**: there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).
