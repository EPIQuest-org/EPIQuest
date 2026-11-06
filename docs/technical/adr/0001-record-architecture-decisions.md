# ADR-0001: Record architecture decisions

| | |
|---|---|
| **Status** | Accepted |
| **Date** | 2026-11-05 |
| **Deciders** | Louis Ferrari |
| **Related** | [ADR template](0000-template.md), [CONTRIBUTING.md](../../../CONTRIBUTING.md) |

## Context

The project spans about three months, with three people making technology choices (database, web stack, mobile app, CI/CD and hosting) under time pressure. Choices made in chat or in meetings are quickly forgotten, and a newcomer or a jury member needs to understand why things are the way they are.

The project brief requires the technical documentation to include a log of architecture decisions (context, options, chosen option, consequences), versioned in the repository or published from it.

The team also produces longer working documents, such as benchmarks, that are not required by the brief.

## Decision drivers

1. Meet the brief's requirement for versioned technical documentation
2. Keep decisions traceable, with their history
3. Keep the effort low for a small team
4. Keep long working documents easy to edit and comment on

## Options considered

1. **ADRs in the repository, as Markdown files** (one file per decision).
2. **A decision log on a shared drive or wiki.** Easy to edit, but not versioned with the code and not what the brief asks.
3. **No formal record**, only chat and meeting notes.

## Decision

We record every significant technical decision as an **Architecture Decision Record (ADR)**:

- ADRs are Markdown files in `docs/technical/adr/`, versioned with the code, named `NNNN-short-title.md` with a four-digit number.
- They follow the [template](0000-template.md): status, date, deciders, context, decision drivers, options considered, decision, consequences, follow-up.
- Statuses: Proposed, Accepted, Rejected, Deprecated, or Superseded by ADR-XXXX.
- ADRs are never deleted. A decision that changes gets a new ADR, and the old one is marked as superseded.
- An ADR is proposed through a pull request and reviewed like code. The status moves to Accepted when the team agrees.
- **Benchmarks stay outside the repository**, as documents on the shared drive. An ADR links to its benchmark and summarizes the comparison and the scores, so that it remains understandable on its own.
- What counts as significant: a choice that is costly to reverse, that affects several parts of the product, or that the team debated.

## Consequences

**Positive**
- The decision history is in the repository and meets the brief.
- Newcomers and the jury can understand the reasoning.
- Reviews of decisions happen in pull requests.

**Negative / trade-offs**
- A small writing effort for each decision.
- The benchmark links point to a shared drive, which must stay accessible to the team and the jury. If it is not, the ADR summary must be enough.

## Index

| ADR | Title | Status |
|-----|-------|--------|
| [0001](0001-record-architecture-decisions.md) | Record architecture decisions | Accepted |
| [0002](0002-database.md) | Database choice | Proposed |
| [0003](0003-web-stack.md) | Web stack (front end, back end, real time) | Accepted |
| [0004](0004-mobile-app.md) | Mobile app (platform and technology) | Accepted |
| [0005](0005-ci-cd-and-hosting.md) | CI/CD and hosting | Proposed |

## Follow-up

- [ ] Keep the index above up to date when an ADR is added or its status changes.
- [ ] Write an ADR for the SRD version and the import source.