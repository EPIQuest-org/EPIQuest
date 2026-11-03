# Contributing to <PRODUCT_NAME>

Thank you for contributing! This guide explains how we work, so that every contribution is easy to review, merge and trace.

> Everything marked `TODO` or written between `<angle brackets>` will be filled in as the project moves forward.

## Table of contents

- [Code of conduct](#code-of-conduct)
- [Before you start](#before-you-start)
- [Workflow overview](#workflow-overview)
- [Branching strategy (Git Flow)](#branching-strategy-git-flow)
- [Commit convention](#commit-convention)
- [Pull requests](#pull-requests)
- [Code quality](#code-quality)
- [Tests](#tests)
- [Documentation](#documentation)
- [Releases and versioning](#releases-and-versioning)
- [Using AI tools](#using-ai-tools)
- [Security and secrets](#security-and-secrets)
- [Licenses and assets](#licenses-and-assets)
- [Accessibility](#accessibility)
- [Communication](#communication)
- [Your first contribution](#your-first-contribution)

---

## Code of conduct

By participating, you agree to follow our [Code of Conduct](CODE_OF_CONDUCT.md).

## Before you start

1. Read the [README](README.md) and get the product running locally with the demo campaign (the README should be enough, in under 15 minutes). If it is not, that is a bug in our documentation: please open an issue.
2. Look at the [GitHub Projects board](https://github.com/orgs/EPIQuest-org/projects/10) to see what is planned and in progress.
3. Install the recommended tooling:
   - Git
   - Visual Studio Code with the **Conventional Commits** extension (`vivaxy.vscode-conventional-commits`)
   - <Linter, formatter and other tools, TODO>

## Workflow overview

Every change follows the same path:

1. **An issue exists.** Every task is a GitHub issue with an assignee, a label and a status, attached to a milestone or an iteration. No issue, no work.
2. **A branch is created** from `develop` (see [Git Flow](#branching-strategy-git-flow)).
3. **Commits follow the [Conventional Commits](#commit-convention) convention.**
4. **A pull request is opened**, linked to the issue (`Closes #<issue>`).
5. **CI passes and at least one teammate reviews** the pull request.
6. **The pull request is merged**, and the issue moves to *Done* on the board.

### Issues

- Use the issue templates in `.github/ISSUE_TEMPLATE/`.
- Write a clear title and an actionable description with acceptance criteria.
- Set one **label** for the type (for example `feature`, `bug`, `docs`, `chore`, `accessibility`) and, when relevant, an area label (`web`, `server`, `mobile`, `rules-engine`, `database`).
- Set an **assignee**, a **status** on the board (`Backlog`, `Ready`, `In progress`, `In review`, `Done`) and a **milestone** or iteration.
- Issues that are good for newcomers are labeled `good first issue`.

*(TODO: adjust labels and statuses to match the board once it is configured.)*

## Branching strategy (Git Flow)

We use **Git Flow**.

| Branch | Purpose | Created from | Merged into |
|--------|---------|--------------|-------------|
| `main` | Production-ready code. Every commit is a tagged release. | n/a | n/a |
| `develop` | Integration branch for the next release. | `main` | n/a |
| `feature/*` | New features and improvements. | `develop` | `develop` |
| `bugfix/*` | Bug fixes found on `develop`. | `develop` | `develop` |
| `release/*` | Release preparation (version bump, changelog, final fixes). | `develop` | `main` and `develop` |
| `hotfix/*` | Urgent fixes on a released version. | `main` | `main` and `develop` |

### Rules

- `main` and `develop` are **protected**: no direct push, pull request required, review required, status checks (CI) required.
- Branch names use lowercase and hyphens, and start with the issue number:
  `feature/42-dice-parser`, `bugfix/87-fog-of-war-flicker`, `release/1.0.0`, `hotfix/1.0.1-session-leak`.
- Keep branches short-lived and focused on a single issue. Rebase or merge `develop` into your branch regularly to avoid large conflicts.
- Merge strategy:
  - `feature/*` and `bugfix/*` into `develop`: **squash merge** (one clean Conventional Commit per issue; use the PR title as the commit message).
  - `release/*` and `hotfix/*` into `main`: **merge commit** (keeps the release history), then merge back into `develop`.
- Delete the branch after merging.

### Continuous integration and deployment

- GitHub Actions runs on **every pull request**: lint, tests (web, server and mobile) and build. A pull request with a failing pipeline cannot be merged.
- Every merge triggers an automatic deployment of the online demo environment with the demo campaign. *(TODO: decide which branch deploys, see the CI/CD ADR.)*
- The mobile build (APK or iOS simulator build) is produced as a pipeline artifact.
- Secrets live in GitHub Secrets, never in the code.

## Commit convention

We follow [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/), using the VS Code **Conventional Commits** extension to build the messages.

### Format

```text
<type>(<scope>): <short description>

[optional body]

[optional footer(s)]
```

- Use the imperative mood, lowercase, no trailing period: `add`, not `added` or `adds`.
- Keep the first line under 72 characters.
- Reference the issue in the footer: `Refs #42` or `Closes #42`.

### Types

| Type | Use for |
|------|---------|
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation only |
| `style` | Formatting, no change in behavior |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf` | Performance improvement |
| `test` | Adding or fixing tests |
| `build` | Build system or dependencies |
| `ci` | CI/CD configuration |
| `chore` | Other maintenance tasks |
| `revert` | Reverts a previous commit |

### Scopes

*(TODO: confirm the list.)* Suggested scopes: `web`, `server`, `mobile`, `db`, `rules`, `dice`, `maps`, `combat`, `sync`, `a11y`, `docs`, `ci`.

### Examples

```text
feat(dice): add keep-highest modifier to the dice parser

fix(maps): hide GM layer from the player view

Closes #87

docs(adr): add ADR-002 for the database choice

feat(rules)!: change the spell description format

BREAKING CHANGE: spells now use the v2 structured format; re-run the SRD import.
```

A `!` after the type/scope, or a `BREAKING CHANGE:` footer, marks a breaking change and triggers a major version bump.

*(TODO: enforce the convention automatically with commitlint in a pre-commit/commit-msg hook and in CI.)*

## Pull requests

1. Open the pull request against the right branch (`develop` for features and bug fixes).
2. Use the pull request template (`.github/PULL_REQUEST_TEMPLATE.md`) and fill it in.
3. Use a Conventional Commit style **title** (it becomes the squash commit message).
4. Link the issue (`Closes #<issue>`).
5. Add screenshots or recordings for any visible change.
6. Make sure the checklist is complete before asking for a review.

### Review

- At least **one approval** from a teammate other than the author is required.
- Reviewers check: correctness, tests, readability, security, accessibility, documentation, and compliance with this guide.
- Be kind and constructive. Comment on the code, not the person.
- The author answers every comment and pushes fixes; the reviewer resolves their own threads.
- Review within <TARGET_DELAY_TODO, e.g. 24 hours> where possible.

### Pull request checklist

- [ ] Linked to an issue
- [ ] The branch is up to date with its target branch
- [ ] CI is green (lint, tests, build)
- [ ] New code is covered by tests
- [ ] Documentation is updated (user and/or technical)
- [ ] No secrets, dead code or unused files
- [ ] Accessibility checked for UI changes (keyboard, screen reader, contrast)
- [ ] AI usage, if any, is declared in the README and the code has been reviewed and understood

## Code quality

- A **linter and formatter** are applied automatically (pre-commit hook and CI). *(TODO: name the tools once the stack is chosen.)*
- No dead code, no orphan files, no commented-out code.
- Clear folder structure: put files where the [project structure](README.md#project-structure) says they belong.
- Error messages and logs must be understandable in development.
- Keep dependencies up to date and locked (lockfile committed).
- The server is the authority: dice rolls and combat actions are computed and validated server-side, and permissions are always checked server-side.
- Never send hidden information (fog-covered tokens and zones, GM layer, hidden rolls, private notes) to the player view, even in the data received by the browser.

## Tests

- All tests run with **one command**: `<TEST_COMMAND_TODO>`.
- To run a single test: `<SINGLE_TEST_COMMAND_TODO>`.
- New behavior requires tests; bug fixes require a test that reproduces the bug.
- Areas that must be tested at minimum: the dice parser (including statistical distribution tests), the rules engine (reference characters with all computed values checked), level-up, line of sight and fog of war, encounter difficulty, combat action resolution (automatic effects, GM-added effects, undo), and the filtering of information sent to the player view.
- The mobile app has its own tests (logic and UI).

## Documentation

- Documentation is part of the product and is written along with the code, in **English**.
- User documentation lives in `docs/user/`, technical documentation in `docs/technical/`.
- Significant technical decisions are recorded as **Architecture Decision Records** in `docs/technical/adr/`. To propose one, copy `docs/technical/adr/0000-template.md`, number it with the next available number, and open a pull request. ADRs are never deleted: a decision that changes is marked as superseded by a new ADR.
- Update `CHANGELOG.md` for any user-visible change (see below).

## Releases and versioning

- We use [Semantic Versioning](https://semver.org/): `MAJOR.MINOR.PATCH`.
- Releases are prepared on a `release/<version>` branch: bump the version, update `CHANGELOG.md` (format: [Keep a Changelog](https://keepachangelog.com/)), fix last-minute issues only.
- After merging into `main`, tag the commit: `git tag -a v1.0.0 -m "Release 1.0.0"`, then merge `main` back into `develop`.

## Using AI tools

AI tools are allowed, within these rules:

- **Transparency:** declare the tools you use and what for (code, tests, documentation, visuals) in the README's [AI usage declaration](README.md#ai-usage-declaration).
- **Responsibility:** you are responsible for everything you submit. You must be able to explain any part of your code.
- **Same requirements:** generated content goes through the same tests, review, cleanliness and security rules as everything else.
- **Licenses:** generated or retrieved images, maps and texts must respect licenses. No content from paid rulebooks may be reproduced.

## Security and secrets

- Never commit secrets, tokens, passwords or personal data, including in the history. Use `.env` locally (git-ignored) and document variables in `.env.example`.
- Store pipeline secrets in GitHub Secrets.
- Keep the OWASP Top 10 in mind: validate inputs, check permissions server-side, hash passwords, use secure sessions.
- If you find a vulnerability, do not open a public issue: contact the team privately on Discord. *(TODO: add a `SECURITY.md` if needed.)*

## Licenses and assets

- Rules content comes only from the **SRD 5.1** (CC BY 4.0). Keep the attribution notice in place.
- Visuals (maps, tokens, icons) must be under a free license or created by the team, and **credited** in the README's visual assets section.
- Do not use the name or logo of existing commercial products, and do not copy the interface of an existing product.

## Accessibility

The target is **WCAG 2.2 level AA** on the web and on mobile. For every UI change, check at least:

- Keyboard-only use, with a logical order and a always-visible focus
- Semantic HTML, headings and labels (screen readers)
- Contrast ratio of 4.5:1 for body text, and information never conveyed by color alone
- Zoom at 200% without loss of content, and `prefers-reduced-motion` respected
- On mobile: VoiceOver/TalkBack, dynamic text sizes and touch targets of at least 44 pt / 48 dp

## Communication

- **Discord:** day-to-day discussion and quick questions. *(TODO: list the channels.)*
- **GitHub Issues and pull requests:** everything that concerns the code. Decisions taken on Discord must be written down in the related issue or ADR.
- **Google Drive:** shared working documents, meeting notes and deliverable drafts. [link](https://drive.google.com/drive/folders/1LjRGrfXMlRUQp_-uMnpIMYimd-XxE3tO?usp=sharing)

## Your first contribution

1. Pick an issue labeled `good first issue` on the [board](https://github.com/orgs/EPIQuest-org/projects/10/views/1) and comment to claim it.
2. Create your branch from `develop`: `git checkout develop && git pull && git checkout -b feature/<issue>-<short-description>`.
3. Make your change, with tests and documentation.
4. Commit using the Conventional Commits extension.
5. Push and open a pull request.

Questions? Ask on Discord. Welcome aboard!