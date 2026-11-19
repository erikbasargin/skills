# GLOSSARY.md Format

## Structure

```md
# {Context Name}

{One or two sentence description of what this context is and why it exists.}

## Language

**Order**:
{A one or two sentence description of the term}
_Avoid_: Purchase, transaction

**Invoice**:
A request for payment sent to a customer after delivery.
_Avoid_: Bill, payment request

**Customer**:
A person or organization that places orders.
_Avoid_: Client, buyer, account
```

## Rules

- **Be opinionated.** When multiple words exist for the same concept, pick the best one and list the others under `_Avoid_`.
- **Keep definitions tight.** One or two sentences max. Define what it IS, not what it does.
- **Only include terms specific to this project's context.** General programming concepts (timeouts, error types, utility patterns) don't belong even if the project uses them extensively. Before adding a term, ask: is this a concept unique to this context, or a general programming concept? Only the former belongs.
- **Group terms under subheadings** when natural clusters emerge. If all terms belong to a single cohesive area, a flat list is fine.

## Single vs multi-context repos

Put each glossary in the owning module's DocC catalog. In a Swift package with the default target layout, that is `Sources/<Target>/Documentation.docc/GLOSSARY.md`. Follow the target's actual source path and catalog name.

For a single context, use the owning module's glossary. For multiple contexts in different modules, each catalog can have its own `GLOSSARY.md`. If multiple contexts share one catalog, give their articles distinct filenames such as `Ordering-Glossary.md` and `Billing-Glossary.md`.

For multiple contexts, use a repository-level `GLOSSARY-MAP.md` to list the contexts, link to their glossary files with relative Markdown links, and explain how they relate.

```md
# Glossary Map

## Contexts

- [Ordering](./Sources/Ordering/Documentation.docc/GLOSSARY.md) receives and tracks customer orders.
- [Billing](./Sources/Billing/Documentation.docc/GLOSSARY.md) generates invoices and processes payments.
- [Fulfillment](./Sources/Fulfillment/Documentation.docc/GLOSSARY.md) manages warehouse picking and shipping.

## Relationships

- **Ordering → Fulfillment**: Ordering emits `OrderPlaced` events; Fulfillment consumes them to start picking
- **Fulfillment → Billing**: Fulfillment emits `ShipmentDispatched` events; Billing consumes them to generate invoices
- **Ordering ↔ Billing**: Shared types for `CustomerId` and `Money`
```

Read the existing map when available. Otherwise, look for the glossary in the module that owns the relevant domain rules. When the first term is resolved, create the glossary in that module's catalog if needed. If ownership is unclear, ask which context the term belongs to.
