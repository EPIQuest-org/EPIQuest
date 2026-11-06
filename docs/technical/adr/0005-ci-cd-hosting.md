# ADR-0005: CI/CD and hosting

| | |
|---|---|
| **Status** | Proposed (pending the validation spike and the teacher's confirmation of the main branch) |
| **Date** | 2026-11-03 |
| **Deciders** | <TEAM_MEMBERS_TODO> |
| **Related** | [CI/CD and hosting benchmark (Google Drive)](<DRIVE_URL_TODO>), [ADR-0002: Database choice](0002-database.md), [ADR-0003: Web stack](0003-web-stack.md), [ADR-0004: Mobile app](0004-mobile-app.md) |

## Context

The brief requires:

- continuous integration on every pull request (lint, tests of the web, the server and the mobile app, build), with a failing pipeline blocking the merge through mandatory checks on the main branch;
- continuous deployment: every merge on the main branch updates the product automatically, accessible online with the demo campaign, on a free hosting offer of the team's choice;
- an installable or testable APK produced by the pipeline on every merge;
- pipeline secrets stored in GitHub Secrets, never in the code;
- the pipeline described in the technical documentation, with its status badge in the README;
- a newcomer able to run the product and the tests in under 15 minutes.

The team already uses GitHub Projects and GitHub Actions, follows Git Flow (`main`, `develop`, `feature/*`, `bugfix/*`, `release/*`, `hotfix/*`) and Conventional Commits. The stack is React (Vite) and Node.js with WebSocket (ADR-0003), PostgreSQL (ADR-0002) and a React Native Android app (ADR-0004). At the defense, the jury plays on two screens with the online environment.

Two constraints shape the decision:

1. **Branch protection and required status checks** are available on public repositories on GitHub Free, but on private repositories they require GitHub Pro or Team. GitHub Pro is included for verified students in the GitHub Student Developer Pack.
2. **Free hosting offers have shrunk.** As checked on 2026-11-03: Koyeb needs a card since February 2026, Fly.io has no free tier for new accounts, Railway gives a short trial, and Render's free PostgreSQL expires after 30 days. Render's free web service sleeps after 15 minutes without traffic, and Neon offers a permanent free PostgreSQL.

## Decision drivers

1. Cost and card requirement (20%)
2. Always available for the demo and the defense (20%)
3. WebSocket support (15%)
4. Data persistence (10%)
5. Integration with GitHub Actions (10%)
6. Operational workload (10%)
7. Longevity of the offer (10%)
8. Parity with local development (5%)

## Options considered

| ID | Hosting stack | Weighted score (out of 5) |
|----|---------------|---------------------------|
| H1 | Render free web service + Neon free PostgreSQL | 4.10 |
| H3 | Azure student credit (virtual machine or managed services) | 4.00 |
| H4 | Oracle Cloud Always Free virtual machine | 3.70 |
| H9 | Cheap paid virtual server (fallback, not free) | 3.70 |
| H5 | Google Cloud Run + Neon | 3.65 |
| H6 | Railway (trial then credit) | 3.30 |
| H8 | A teammate's machine with a tunnel | 3.30 |

Eliminated: Render web service with Render free PostgreSQL (the database expires after 30 days), Koyeb and Fly.io (not free for new accounts), and serverless or edge hosts for the server (no long-lived Node server with WebSocket connections).

The scores are an initial assessment, not measurements.

## Decision

### CI/CD

GitHub Actions, with four workflows:

- **CI**, on pull requests to `develop` and `main` and on pushes to both: lint and format check, commit message and pull request title check (Conventional Commits), unit tests of the shared package and the server (with a PostgreSQL service container), web tests and build, end-to-end tests (Playwright), mobile tests and debug APK build, secret scanning and dependency audit. A single final job, "all checks passed", is the required status check, so path filtering can skip jobs without blocking merges.
- **Deploy**, after CI passes on pushes to `develop` and to `main`: triggers the deployment of one demo environment and runs a health check.
- **Release**, on tags `v*`: builds the release APK and creates a GitHub Release with the changelog and the APK attached.
- **Dependency updates**: Dependabot, weekly.

Pipeline rules:

- `main` and `develop` are protected, with the "all checks passed" job and one approving review required.
- GitHub-hosted runners, with pnpm and Gradle caches and path filtering to save minutes.
- APK artifacts have a short retention (for example 14 days). Release APKs are attached to GitHub Releases.
- GitHub Secrets contain only what the pipeline needs (for example the host's deploy hook URL). Runtime secrets live in the host's environment settings. `.env.example` documents every variable. Secret scanning runs on every pull request.
- Database migrations and the demo campaign are loaded by an idempotent step at server start, controlled by a flag.
- The pipeline is described in the technical documentation, and its status badge is in the README.

### Git Flow and deployment

Both `develop` and `main` deploy to the **same single demo environment**. This satisfies the brief's "every merge on the main branch" under either reading of "main branch", and keeps the free hosting use to one environment. This must be **confirmed with the teacher** and documented in the README.

### Hosting (proposed)

- **Web server and web app:** one Node.js container (built from a Dockerfile) that serves the API, the WebSocket connections and the built React app, on a **Render free web service**. A single deployable unit avoids cross-site cookie problems, and Render provides TLS, which the Android app needs outside debug builds.
- **Database:** **Neon free PostgreSQL**, which is standard PostgreSQL (JSONB, GIN indexes, `pg_trgm`, Row-Level Security) and persists without expiry.
- **Keep-alive:** an external uptime monitor pings a lightweight health endpoint that does not query the database, within Render's free instance hours. The host's terms of use must be checked first. This is a mitigation, not a guarantee.
- **Reconnection:** the web client and the mobile app reconnect automatically after a restart.
- **Plan B for the defense:** the complete stack running locally with Docker Compose on a laptop.
- **Documented fallbacks:** Azure student credit (every member has one, to check against the virtual machine price and the period until the retake in February) or an Oracle Cloud virtual machine, with Docker Compose deployed over SSH.

### Repository plan (decided)

The repository is **private** and every team member is verified in the GitHub Student Developer Pack, so each has GitHub Pro. The repository is **owned by one teammate's personal account**, and the others are collaborators. Branch protection and required status checks therefore work on `main` and `develop`.

Consequences to handle:

- The Actions minutes (3,000 per month on GitHub Pro) belong to the owner's account and are shared by the whole team.
- If the owner's student verification lapses and Pro ends, branch protection stops being enforceable on the private repository. The owner checks the verification date, and the team keeps a plan to transfer ownership.
- The owner is the only administrator, so choose someone reliable.
- An organization on the free plan does not give protected branches on private repositories, so the repository must not be moved to an organization without a paid Team plan.

## Consequences

**Positive**
- Everything needed runs at no cost and without a card.
- Merge-to-live is automatic, gated by CI, with a health check.
- One container and Docker Compose keep local development, CI and hosting close to each other.
- The pipeline matches the brief's requirements: required checks, APK artifact, secrets in GitHub Secrets, documented pipeline and badge.

**Negative / trade-offs**
- The free Render service sleeps without traffic. A cold start takes about a minute, which is a risk for the jury demo.
- Free offers change often, so the choice must be re-checked during the project.
- Neon's free storage is small and its compute hours are limited.
- The real-time connections drop when the instance restarts.
- The repository depends on one teammate's account and student verification.

**Risks and mitigations**
- *Risk:* free offers change. *Mitigation:* re-check in mid-December and two weeks before the defense, keep the Docker Compose setup portable, keep the documented fallbacks.
- *Risk:* Actions minutes run out. *Mitigation:* path filtering, caching, short artifact retention, mobile builds only when needed and on merges, and a check of the minutes used after the first weeks.
- *Risk:* a cold start or an outage during the jury demo. *Mitigation:* keep-alive monitor, warm-up 10 minutes before, local plan B.
- *Risk:* the database storage limit is reached. *Mitigation:* check the size after the first full SRD import.
- *Risk:* a deployment breaks the demo. *Mitigation:* deployment gated by CI, post-deploy health check, redeploy the previous commit to roll back.
- *Risk:* a leaked secret. *Mitigation:* secret scanning in CI, secrets only in GitHub Secrets and in the host settings.

## Follow-up

- [ ] Choose which teammate owns the repository, then enable branch protection on `main` and `develop` with the required check, and note the owner's verification date.
- [ ] Confirm with the teacher which branch counts as the main branch under Git Flow.
- [ ] Run the validation spike (Dockerfile, Render and Neon, CI with a PostgreSQL service container, deploy workflow, WebSocket wake-up time, APK job, Actions minutes used) and record the results in the benchmark.
- [ ] Check the current Render and Neon terms and limits on their official pages, and the host's rules on keep-alive pings.
- [ ] Write the pipeline and deployment documentation in `docs/technical/` and add the CI badge to the README.
- [ ] Re-check free hosting offers in mid-December and two weeks before the defense.