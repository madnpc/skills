# ADR Format

ADRs live in `docs/adr/` and use sequential numbering: `0001-slug.md`, `0002-slug.md`, and so on.

Create the `docs/adr/` directory lazily, only when the first ADR is needed.

## Template

```md
# {Short title of the decision}

{1–3 sentences: what is the context, what was decided, and why.}
```

That is enough. An ADR can be a single paragraph. Its value is recording that a decision was made and why, not filling out sections.

## Optional sections

Only include these when they add genuine value. Most ADRs will not need them.

- **Status** frontmatter (`proposed | accepted | deprecated | superseded by ADR-NNNN`): useful when decisions are revisited.
- **Considered Options**: only when the rejected alternatives are worth remembering.
- **Consequences**: only when non-obvious downstream effects need to be called out.

## Numbering

Scan `docs/adr/` for the highest existing number and increment it by one.

## When to offer an ADR

All three conditions must be true:

1. **Hard to reverse**: changing the decision later would have a meaningful cost.
2. **Surprising without context**: a future reader would wonder why it was done this way.
3. **The result of a real trade-off**: genuine alternatives existed and one was chosen for specific reasons.

If a decision is easy to reverse, skip it. If it is unsurprising, nobody will need an explanation. If there was no real alternative, there is nothing to record beyond “we did the obvious thing.”

### What qualifies

- **Architectural shape.** For example, choosing a monorepo or using an event-sourced write model with a projected Postgres read model.
- **Integration patterns between contexts.** For example, Ordering and Billing communicating through domain events instead of synchronous HTTP.
- **Technology choices that carry lock-in.** Database, message bus, authentication provider, or deployment target—not every library, only those that would take substantial effort to replace.
- **Boundary and scope decisions.** For example, Customer owning customer data while other contexts reference it only by ID. Explicit exclusions are as valuable as inclusions.
- **Deliberate deviations from the obvious path.** For example, using manual SQL instead of an ORM for a specific reason. These prevent the next engineer from “fixing” an intentional choice.
- **Constraints not visible in code.** For example, a compliance restriction against a cloud provider or a partner contract requiring sub-200 ms responses.
- **Rejected alternatives when the rejection is non-obvious.** Record subtle reasons for choosing REST over GraphQL; skip obvious rejections.
