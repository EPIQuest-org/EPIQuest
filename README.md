<div align="center">

# <PRODUCT_NAME>

**A virtual tabletop for 5th edition compatible tabletop role-playing games.**
Characters, maps, monsters, dice and combat, on two synchronized screens.

[![CI](https://github.com/<ORG>/<REPO>/actions/workflows/ci.yml/badge.svg)](https://github.com/<ORG>/<REPO>/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/license-<LICENSE>-blue.svg)](LICENSE)
[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg)](https://www.conventionalcommits.org)

[User documentation](docs/user/) · [Technical documentation](docs/technical/) · [Contributing](CONTRIBUTING.md)

</div>

> **Status:** early development. Everything marked `TODO` or written between `<angle brackets>` is not decided yet and will be filled in as the project moves forward.

---

## Table of contents

- [About](#about)
- [Features](#features)
- [Screenshots](#screenshots)
- [Tech stack](#tech-stack)
- [Getting started](#getting-started)
- [Running the tests](#running-the-tests)
- [Project structure](#project-structure)
- [Documentation](#documentation)
- [Project management and workflow](#project-management-and-workflow)
- [AI usage declaration](#ai-usage-declaration)
- [Accessibility](#accessibility)
- [Team](#team)
- [License and attributions](#license-and-attributions)

---

## About

<PRODUCT_NAME> is a virtual tabletop designed to be played around a real table:

- The **Game Master (GM)** prepares a campaign, its maps and its encounters, and runs the session from the **GM view** on a computer.
- The **players** create and manage their characters from the web or the mobile app.
- A **player view**, shown on a second screen or a television, displays only what the characters can see. Hidden information is never sent to it.

The product is **compatible with the 5th edition rules** published in the System Reference Document (SRD) under a free license. All rules content is imported from the SRD, and rules are handled as **data, not code**.

## Features

**Characters**
- Step-by-step creation wizard (species, class, background, ability scores, skills, equipment, spells) validated by a rules engine
- Automatic recalculation of derived statistics (modifiers, proficiency bonus, AC, HP, saving throws, spell slots, carried weight)
- Levels 1 to 5 for all SRD classes, with level-up
- Interactive character sheet (click a skill to roll it) and PDF export

**Dice**
- Full notation: `2d6+3`, `4d6kh3`, advantage/disadvantage, exploding dice, rerolls, multiple dice in one expression
- Server-side rolls with a secure random generator
- Public, private (GM only) and hidden rolls, with a full session history
- Probability display (minimum, maximum, average, distribution)

**Maps**
- Square or hexagonal grid, background image, layers (including a GM-only layer)
- Walls and doors that block movement and sight
- Fog of war: manual reveal and automatic reveal from line of sight
- 5e distance rules and area-of-effect templates (cone, line, sphere, cube)

**Bestiary and encounters**
- SRD monster search and filters, full stat blocks
- Encounter builder with difficulty computation (easy, medium, hard, deadly)
- One-click encounter placement on a map

**Assisted combat**
- Automatic initiative, turn order, attack rolls, saving throws, damage types, resistances, conditions and resource consumption
- The GM always keeps control: every result is shown before being applied, and can be edited or undone
- "Add an effect" tool for anything the rules do not cover

**Campaign management and live play**
- GM notes, NPCs, locations, quests, and documents to reveal to players
- Two views synchronized in real time

**Mobile app**
- Character creation, character sheet and dice rolling, with the same rules engine as the web

*(TODO: update this list as features are delivered.)*

## Screenshots

*(TODO: add screenshots of the GM view, the player view, the character sheet and the mobile app.)*

## Tech stack

The stack is not final. Candidates under evaluation are documented in the [architecture decision records](docs/technical/adr/).

| Layer | Candidates | Decision |
|-------|-----------|----------|
| Website | React (Vite) + Node.js (TypeScript) + WebSocket | Accepted ([ADR-0003](docs/technical/adr/0003-web-stack.md)) |
| Mobile app | React Native (TypeScript), Android | Accepted ([ADR-0004](docs/technical/adr/0004-mobile-app.md)) |
| Database | PostgreSQL, MySQL | TODO ([ADR-002](docs/technical/adr/)) |
| CI/CD | GitHub Actions | Decided |
| Project management | GitHub Projects | Decided |

## Getting started

> **Goal:** a new contributor should be able to run the product with the demo campaign, and the tests, in under 15 minutes using only this README.

### Prerequisites

- Git
- <Docker and Docker Compose / Node.js version / other, TODO>
- For the mobile app: <Flutter SDK version, Android Studio or Xcode, TODO>

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/<ORG>/<REPO>.git
cd <REPO>

# 2. Configure the environment
cp .env.example .env
# Edit .env if needed (every variable is documented in the file)

# 3. Start everything: database, server, web app,
#    SRD import and demo campaign loading
<ONE_COMMAND_TODO>
```

Then open <http://localhost:<PORT>> and sign in with the demo account: `<DEMO_ACCOUNT_TODO>`.

### Importing the SRD manually

The SRD import is replayable and versioned.

```bash
<IMPORT_COMMAND_TODO>
```

### Running the mobile app

```bash
<MOBILE_COMMANDS_TODO>
```

The mobile app can be run on a phone or on an emulator. See [the mobile documentation](docs/technical/) for details.

## Running the tests

All tests run with a single command:

```bash
<TEST_COMMAND_TODO>
```

To run a single test, see the [contributor guide](docs/technical/).

## Project structure

*(TODO: fill in once the repository layout is defined.)*

```text
.
├── .github/          # Issue and PR templates, GitHub Actions workflows
├── docs/
│   ├── user/         # User documentation (GM, players)
│   └── technical/    # Technical documentation and ADRs
├── web/              # Website (client)
├── server/           # Server and rules engine
├── mobile/           # Mobile application
├── data/             # SRD import scripts and demo campaign
├── CHANGELOG.md
├── CODE_OF_CONDUCT.md
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

## Documentation

| Document | Audience | Location |
|----------|----------|----------|
| User guide (GM) | Game Masters | [docs/user/](docs/user/) |
| User guide (player) | Players | [docs/user/](docs/user/) |
| Technical documentation | Developers | [docs/technical/](docs/technical/) |
| Architecture decision records | Developers | [docs/technical/adr/](docs/technical/adr/) |
| Contribution guide | Contributors | [CONTRIBUTING.md](CONTRIBUTING.md) |
| Changelog | Everyone | [CHANGELOG.md](CHANGELOG.md) |

## Project management and workflow

- **Tasks:** GitHub Issues, tracked on our [GitHub Projects board](<PROJECT_BOARD_URL>)
- **Code:** every change goes through a pull request linked to its issue and reviewed before merge. The main branch is protected.
- **Commits:** [Conventional Commits](https://www.conventionalcommits.org)
- **CI/CD:** GitHub Actions runs lint, tests and build on every pull request. Every merge on the main branch is deployed automatically.
- **Communication:** Discord
- **Shared documents:** Google Drive

See [CONTRIBUTING.md](CONTRIBUTING.md) for the full workflow.

## AI usage declaration

In accordance with the project rules, we declare the AI tools used during this project and what they were used for.

| Tool | Used for | Notes |
|------|----------|-------|
| <TOOL_TODO> | <code / tests / documentation / visuals> | <e.g. reviewed in PR, edited by hand> |

All AI-generated content follows the same rules as the rest of the project: tests, pull request review, repository cleanliness, security and licenses. Every team member can explain any part of the code.

*(TODO: keep this table up to date throughout the project.)*

## Accessibility

The target is **WCAG 2.2 level AA** (in France, the RGAA) on the web and on the mobile app. The audit results and the accessibility statement will be available in [docs/technical/](docs/technical/).

## Team

| Name | Role | GitHub |
|------|------|--------|
| Lucas Scheidecker | <ROLE_TODO> | [@Gazolina666](https://github.com/Gazolina666) |
| Kévin Landbeck | <ROLE_TODO> | [@lankev](https://github.com/lankev) |
| Louis Ferrari | <ROLE_TODO> | [@Louis-Ferrari](https://github.com/Louis-Ferrari) |

## License and attributions

The source code is released under the <LICENSE_TODO> license. See [LICENSE](LICENSE).

### SRD 5.1 attribution

This work includes material taken from the System Reference Document 5.1 ("SRD 5.1") by Wizards of the Coast LLC and available at <https://dnd.wizards.com/resources/systems-reference-document>. The SRD 5.1 is licensed under the Creative Commons Attribution 4.0 International License available at <https://creativecommons.org/licenses/by/4.0/legalcode>.

### Visual assets

*(TODO: credit every image, icon, map and token that is not created by the team, with its source and license.)*

> <PRODUCT_NAME> is not affiliated with, endorsed or sponsored by Wizards of the Coast. It is presented as "compatible with the 5th edition".