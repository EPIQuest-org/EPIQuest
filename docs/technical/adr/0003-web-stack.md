# ADR-0003: Web stack (front end, back end, real time)

| | |
|---|---|
| **Status** | Accepted (to be confirmed by the validation spike described in the benchmark) |
| **Date** | 2026-11-03 |
| **Deciders** | <TEAM_MEMBERS_TODO> |
| **Related** | [Website benchmark (Google Drive)](https://docs.google.com/document/d/1geVRCE_Q3QPV8GgI31jkKPwL12Q55jHwzMFTqmfufkQ/edit?tab=t.0), [ADR-0002: Database choice](0002-database.md) |

## Context

The web product of <PRODUCT_NAME> must provide:

- two live, synchronized views (GM view and player view), where the player view never receives hidden information, even in the data received by the browser;
- a server that is the authority for dice rolls, combat actions and permissions;
- a map editor with layers, walls, doors, fog of war, line of sight and area templates;
- accessibility at WCAG 2.2 AA level, including a textual alternative to the map;
- a data-driven rules engine that the mobile app (Flutter) must also use;
- a setup that lets a new contributor run everything in under 15 minutes, tests in one command, CI on every pull request and automatic deployment of an online demo.

The team's initial idea was "React or Node". These are not alternatives: React is a front-end library and Node.js is a server runtime. The benchmark therefore compared complete stacks and their building blocks.

## Decision drivers

1. Real-time synchronization and per-view filtering (15%)
2. Accessibility ecosystem (15%)
3. Interactive map and canvas capability (12%)
4. Code sharing and type safety between client and server (12%)
5. Server fit and security (10%)
6. Contributor setup, testability, CI (8%)
7. Ecosystem and longevity (8%)
8. Team skills and learning curve (8%)
9. Performance (5%)
10. Hosting and continuous deployment (7%)

## Options considered

| ID | Stack | Weighted score (out of 5) |
|----|-------|---------------------------|
| A | React (Vite single-page app) + Node.js (TypeScript) + WebSocket | 4.55 |
| F | React single-page app + non-JavaScript back end (ASP.NET Core, Spring Boot, FastAPI, Go) | 4.22 |
| B | Next.js full stack + separate WebSocket server | 4.15 |
| D | Vue 3 / Nuxt + Node.js | 4.04 |
| C | SvelteKit | 3.94 |
| E | Angular + NestJS | 3.76 |
| G | Elixir Phoenix LiveView | 2.92 |

The scores are an initial assessment, not measurements. The full analysis is in the benchmark.

## Decision

We choose **stack A**:

- **Front end:** React as a single-page app built with Vite, written in TypeScript.
- **Back end:** Node.js (a supported LTS line, major version pinned in the repository and in CI) with TypeScript.
- **Real time:** WebSocket, with one room per campaign and two kinds of channels: a GM channel and a player channel.
- **Rules and dice engines on the server**, exposed through the API. The Flutter app uses the same engines through the API, which satisfies the "same rules engine on web and mobile" requirement and the "server is the authority" requirement.
- **Code organization:** one monorepo, with a shared TypeScript package (dice-notation parser, schemas of the structured rules format, API message types) used by the web client and the server.
- **Per-view projection:** the server builds a view-specific projection of the game state for each channel, and only that projection is sent. Nothing is filtered in the browser.
- **Tests:** Vitest for unit tests, Playwright for end-to-end tests (two browser contexts, GM and player, to check the hidden-information filter).

Why: it scores best on the criteria that carry the most weight, namely the accessible-component ecosystem, the map and canvas tooling, shared TypeScript code between client and server, and the size of the community.

### Choices left open

These choices are not decided by this ADR and will be settled during the validation spike, then recorded here or in a new ADR:

- Back-end framework: Fastify or NestJS.
- Map rendering: Konva or PixiJS (with a parallel DOM view for accessibility in both cases).
- Data access layer (depends on ADR-0002): Drizzle, Kysely or Prisma.

## Consequences

**Positive**
- One language (TypeScript) for the web client and the server, with shared types and shared dice and rules schemas.
- Access to the largest ecosystem of accessible components and canvas wrappers.
- Hidden-information protection is built into the architecture, and can be tested with a single end-to-end test.

**Negative / trade-offs**
- The mobile app (Dart) cannot share code with the server, so the rules engine must stay behind the API and the mobile app depends on network access for rule validation.
- Many technology choices must be made (router, state management, forms, server framework). Choices are listed in this ADR and in the follow-up to avoid changing tools during the project.
- JavaScript dependencies carry supply-chain risk.

**Risks and mitigations**
- *Risk:* hidden information leaks to the player view. *Mitigation:* a single projection function with dedicated unit tests, plus an end-to-end test comparing GM and player data.
- *Risk:* the map is not usable with a keyboard or screen reader. *Mitigation:* design the parallel DOM view and the ARIA live region from the start.
- *Risk:* the free real-time host sleeps or limits connections. *Mitigation:* check free plan limits for persistent connections before choosing the host, and keep the Docker Compose setup as a fallback.
- *Risk:* the team is much more comfortable with another language. *Mitigation:* review this ADR together; the benchmark shows that stack F (React with another back-end language) is the closest alternative.
- *Risk:* supply-chain attacks through dependencies. *Mitigation:* committed lockfile, pinned versions, automated dependency updates, and a minimum of dependencies.

## Follow-up

- [ ] Run the validation spike (monorepo skeleton, real-time loop with a hidden token, Konva and PixiJS comparison with 100 tokens, DOM view with a screen reader, a Playwright test with two contexts) and record the results in the benchmark.
- [ ] Choose the back-end framework, the map rendering library and the data access layer, then update this ADR.
- [ ] Check free hosting options that keep WebSocket connections open (feeds the CI/CD ADR).
- [ ] Update the README tech stack table and project structure.
- [ ] Write ADR-0004 (mobile) with the constraint that the app uses the server's rules engine through the API.