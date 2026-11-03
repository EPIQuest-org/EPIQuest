# ADR-0002: Database choice

| | |
|---|---|
| **Status** | Proposed (pending team validation and the spike described in the benchmark) |
| **Date** | 2026-11-02 |
| **Deciders** | <TEAM_MEMBERS_TODO> |
| **Related** | [Database benchmark (Google Drive)](https://docs.google.com/document/d/1q9VGwpDHzgWLgFzS3JA_9dOpqBf2N6jEbdfbfhEdgQ4/edit?tab=t.0) |

## Context

<PRODUCT_NAME> is a virtual tabletop compatible with the 5th edition rules. We need a database that stores:

- classic relational data (users, campaigns, characters, maps, sessions);
- the **SRD rules content** (species, classes, spells, equipment, monsters, conditions), which the brief requires to be **data, not code**: a spell, a class feature or a monster is described in a structured format interpreted by the rules engine, and imported in a replayable, versioned way;
- append-only history (dice rolls, combat log);
- sensitive data (GM layer, hidden rolls, private notes) that must never reach the player view.

Constraints from the brief: the product must run for a new contributor in under 15 minutes, be deployed automatically on a free hosting plan, and work without depending on the external SRD API once the data is imported.

The team shortlisted PostgreSQL and MySQL, and decided that the **fit with our data** is the main selection axis.

## Decision drivers

1. Fit with structured, variable-shape rules data (weight 30%)
2. Data integrity and safe migrations (15%)
3. Search over the bestiary and spells (10%)
4. Support for protecting hidden information (10%)
5. Easy setup for contributors (15%)
6. Free hosting and continuous deployment (10%)
7. Ecosystem and tooling (10%)

## Options considered

1. **PostgreSQL**
2. **MySQL**
3. *Document database (for example MongoDB)*: not shortlisted by the team and not benchmarked. Our relational core (users, campaigns, memberships, characters, constraints) would be handled less naturally, so we did not pursue it. This can be revisited if the team disagrees.

## Decision

We choose **PostgreSQL** (current stable major version at the time of implementation; PostgreSQL 18 at the time of writing).

Why:

- **Best fit with our hybrid data.** `JSONB` with GIN indexing and SQL/JSON path lets us store each spell, monster and class feature as a structured document and query inside it, while joining it to relational tables in the same query. New query needs from the rules engine do not require new generated columns and indexes, as they would in MySQL.
- **Safer migrations.** Transactional DDL means a failed migration rolls back completely, which matters in CI and on the automatically deployed demo.
- **Richer search.** Built-in full-text search plus `pg_trgm` for typo-tolerant bestiary and spell search.
- **Defense in depth for hidden information.** Row-Level Security is available as an extra layer behind the server-side filtering the brief requires.
- **Equivalent contributor setup.** Both options run from an official Docker image, so the 15-minute test is not affected.

The benchmark gives PostgreSQL a weighted score of 4.55 / 5 against 3.30 / 5 for MySQL. These scores are an initial assessment and not measurements.

## Consequences

**Positive**
- Rules content can evolve (new effect types, new fields) without schema changes for each field.
- One engine for relational, document and search needs: fewer moving parts.
- Safer, replayable SRD imports using `INSERT ... ON CONFLICT DO UPDATE` inside transactions.

**Negative / trade-offs**
- PostgreSQL has no native JSON Schema validation, unlike MySQL's `JSON_SCHEMA_VALID()`. We must validate the structured rule definitions in the import pipeline and cover this with tests.
- Free managed hosting offers change often; we depend on current provider conditions (see follow-up).
- The team's real experience with PostgreSQL has not been assessed yet.

**Risks and mitigations**
- *Risk:* JSONB flexibility leads to inconsistent documents. *Mitigation:* a documented JSON format for rules (see the rules data documentation), schema validation at import, and `CHECK` constraints on the key relational fields.
- *Risk:* the free hosting plan pauses or limits the database, breaking the demo during the defense. *Mitigation:* confirm the plan's limits before choosing the host, and keep the one-command Docker Compose setup as a fallback.
- *Risk:* the team is more comfortable with MySQL. *Mitigation:* review this ADR together; the data access layer should go through an ORM or query builder so the cost of a later change stays contained, even though JSONB-specific queries are not fully portable.

## Follow-up

- [ ] Team review of the benchmark scores, especially criterion 7 (team experience).
- [ ] Run the validation spike described in the benchmark (import 20 spells and 20 monsters, run the 5 reference queries, time the Docker Compose start) and record the results.
- [ ] Check current free hosting offers for PostgreSQL and choose one (this will feed the CI/CD ADR).
- [ ] Choose the data access layer (ORM or query builder); this depends on the web/server ADR.
- [ ] Switch the status to **Accepted** once the spike confirms the choice, then update the README tech stack table.