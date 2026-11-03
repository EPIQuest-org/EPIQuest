# ADR-0004: Mobile app (platform and technology)

| | |
|---|---|
| **Status** | Accepted (to be confirmed by the validation spike described in the benchmark) |
| **Date** | 2026-11-03 |
| **Deciders** | <TEAM_MEMBERS_TODO> |
| **Related** | [Mobile benchmark (Google Drive)](https://docs.google.com/document/d/1U2ftKrIDK67S-RNQoy7WzLg17xq16JeXtpCdoslDKZg/edit?tab=t.0), [ADR-0003: Web stack](0003-web-stack.md) |

## Context

The brief requires a mobile app that lets a player create a character (with the same rules engine as the web), view and update the character sheet, and roll dice. It must target **one platform only, iOS or Android**, must not be a website wrapped in an app (PWA, Ionic / Capacitor, Cordova, WebView), and must meet WCAG 2.2 AA on mobile (TalkBack on Android, dynamic text sizes, touch targets of at least 48 dp). It needs its own tests (logic and UI), a documented launch on an emulator, and a build produced by CI on every merge (APK as a pipeline artifact). No store publication is required.

[ADR-0003](0003-web-stack.md) already decided that the rules engine and the dice engine live on the server and are exposed through the API. The app is therefore a client of that API and does not re-implement the rules.

The team's initial idea was Flutter. The person in charge of the app owns an Android phone and chose Android as the target platform. They have never used Kotlin, and have used React Native and Flutter before, some time ago.

## Decision drivers

1. Accessibility: TalkBack, dynamic text, touch targets, accessibility testing (20%)
2. Productivity and learning curve for one person in about three months (20%)
3. Testing and CI (15%)
4. Fit with the scope (10%)
5. Native quality and performance (10%)
6. Ecosystem and libraries (10%)
7. Longevity and stability risk (8%)
8. Code sharing with the web and the server (7%)

## Options considered

| ID | Option | Weighted score (out of 5) |
|----|--------|---------------------------|
| B | React Native with TypeScript | 4.17 |
| C | Native Android with Kotlin and Jetpack Compose | 4.09 |
| A | Flutter (Dart) | 4.01 |
| D | Kotlin Multiplatform with Compose Multiplatform | 3.66 |
| E | .NET MAUI (C#) | 3.13 |

The scores are an initial assessment, not measurements. Jetpack Compose scored 4.29 before the productivity criterion was corrected to reflect that the person in charge has never used Kotlin. The top three are within 0.2 points, which is smaller than the uncertainty of the scores, so the decision relies on the arguments below and on the validation spike.

## Decision

### Platform

The app targets **Android**. This choice is final for the brief (one platform only) and matches the devices available to the person in charge.

### Technology

The app is built with **React Native and TypeScript**.

Why:

- It has the highest weighted score once the person in charge's real experience is taken into account.
- It uses the same language as the web client and the server (ADR-0003). This allows sharing API types and validation schemas through the shared package, and lets web teammates review mobile pull requests, which reduces the risk of one person owning the whole app.
- It renders native views, so TalkBack works naturally, and font scaling is supported by default.
- Jetpack Compose was dropped because learning Kotlin and the Android toolchain in about three months is the main risk of the project. Flutter was the close alternative: it has better built-in testing, but Dart is unfamiliar to the web teammates, which would make reviews harder.

### Choices left open

These are settled during the validation spike, then recorded here:

- Expo or a bare React Native project (Expo is expected to simplify the Android setup).
- State management, navigation and secure storage libraries.
- End-to-end test tool (Maestro or Detox), if any.

### Common decisions

- The app is a client of the server API. Character validation, derived statistics and dice rolls are computed by the server.
- The API is described with OpenAPI, and the app uses a typed client generated from it, or the shared TypeScript types from the monorepo.
- The authentication mode used by the app (secure cookies or short-lived tokens in secure storage) is decided together with the web authentication design.
- A minimum Android version is chosen and documented so that it covers the demo phone and the emulators.
- The README documents how to run the app on an emulator, including the host address used by the Android emulator to reach the local server, and the development-only configuration for plain HTTP.
- CI runs the mobile tests and uploads an APK as a pipeline artifact on every merge. A debug build is enough, and any signing key lives in GitHub Secrets.
- The React Native (and Expo, if used) versions are pinned and only upgraded between milestones.

## Consequences

**Positive**
- One language across web, server and mobile, with shared types.
- Mobile pull requests can be reviewed by the whole team.
- The rules stay on the server, avoiding a second implementation of the rules engine.
- Accessibility can be tested with Testing Library queries by role and label, the Android accessibility tools and manual TalkBack tests.

**Negative / trade-offs**
- The app depends on the server and on network access for every rule check and dice roll.
- Two layers can be involved when something breaks (JavaScript and native), and the ecosystem moves fast, so upgrades need care.
- Fewer first-party accessibility test tools than Jetpack Compose: manual TalkBack testing carries more weight.
- JavaScript dependencies carry supply-chain risk.
- One person owns the whole app, which is a risk for the defense.

**Risks and mitigations**
- *Risk:* the app is not usable with TalkBack. *Mitigation:* set labels, roles and focus order from the first screen, test by role and label, and test manually with TalkBack every week.
- *Risk:* the creation wizard is too large for the time available. *Mitigation:* build the character sheet and dice first, then the wizard.
- *Risk:* the emulator cannot reach the local server. *Mitigation:* document the setup and test it on a clean machine.
- *Risk:* a framework upgrade breaks the build. *Mitigation:* pin versions and upgrade only between milestones.
- *Risk:* rusty React Native skills slow the start. *Mitigation:* run the one-day spike first, and fall back to Flutter if the setup proves much slower than expected.
- *Risk:* a single owner. *Mitigation:* require a reviewer from the web team on mobile pull requests, document the architecture early, and make sure any teammate can explain the code at the defense.

## Follow-up

- [ ] Run the validation spike (skeleton on the real phone and an emulator, one screen with a dice roll through the API, TalkBack check, one logic test and one UI test, GitHub Actions job that uploads the APK) and record the results in the benchmark.
- [ ] Decide Expo or bare React Native.
- [ ] Choose the minimum Android version.
- [ ] Agree on the authentication mode for the app with the web team.
- [ ] Update the README tech stack table and the mobile launch instructions.