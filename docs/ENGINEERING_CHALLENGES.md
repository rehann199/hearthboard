# Engineering Challenges

Each section follows problem, approach, outcome. Limits and quotas are described conceptually; numeric tuning is not published.

## Widget fidelity without Flutter on the home screen

**Problem.** Launcher widgets on Android and iOS cannot host a full Flutter engine reliably. The product promise is that the home screen shows the same split-flap card as the feed, including theme, reactions strip, and typography.

**Approach.** The app renders the feed card widget to an off-screen canvas at fixed sizes: a wider canvas for Android widgets and a shorter, wider canvas matched to the iOS systemMedium aspect ratio. Light and dark theme variants upload to remote storage; URLs are stored on the outgoing message so receivers pick the variant that matches their current theme. Native widget code loads the bitmap and updates providers through the home_widget plugin.

**Outcome.** Visual parity between feed and widget for senders and receivers. Changing a theme may require a new message or refresh because the launcher shows a bitmap, not a relayout engine.

## Multi-recipient send without blocking the UI

**Problem.** Broadcasting a short message to many friends can involve several network writes, image uploads, and quota checks. Blocking the compose screen would feel broken on slow networks.

**Approach.** A background post service enqueues jobs tagged as friends or partner broadcasts, tracks per-job state (posting, posted, failed), and surfaces status in the shell. The user can navigate away while work continues; failures retain an error message for retry.

**Outcome.** Compose stays responsive. The trade-off is complexity in reconciling UI state with asynchronous completion and ensuring analytics still attribute the send correctly.

## Optimistic settings and profile sync

**Problem.** Users expect toggles and profile edits to stick immediately even when Firestore writes lag or fail transiently.

**Approach.** An optimistic sync helper writes to local storage first, invokes UI callbacks, then runs the remote write in the background with de-duplicated sync keys and optional failure hooks.

**Outcome.** Snappy settings and theme changes. Remote failure may leave a brief drift until retry; the app does not claim strong offline-first conflict resolution.

## TV pairing across asymmetric devices

**Problem.** The TV has no keyboard for login in the living room; the phone has no large screen for ambient display. Pairing must bind a display to an account securely enough for a consumer product without exposing long secrets on screen.

**Approach.** The TV creates a pending pairing record and renders a QR encoding the short code. The authenticated phone claims the code, which flips the record to connected and stores owner UID locally on the TV. Both sides subscribe to stream updates for rename, disconnect, and quota used when pushing custom TV slides from the phone.

**Outcome.** A workable QR-led flow. Unreleased TV builds still need broad device QA and store policies before launch.

## Tier gating for sends and TV pushes

**Problem.** Free and paid tiers differ in character width, daily sends, partner exchanges, and how often custom content may appear on a paired TV. The UI must block gracefully with upgrade paths.

**Approach.** Central plan helpers normalise tier names from the user profile and expose helpers for max characters, daily caps, and TV cooldown windows. Compose, TV actions, and bottom sheets consult the same helpers so limits stay consistent across screens.

**Outcome.** Predictable gating in the client. Entitlements are read from profile documents; server-side receipt verification would reduce reliance on client-trusted fields.

## Push notifications that respect preferences

**Problem.** New messages should notify recipients, but users can disable categories (friend messages vs partner messages). Partner and friend paths must not spam when toggled off.

**Approach.** A serverless handler runs on new message documents, loads the recipient profile, reads boolean preference maps with sensible defaults, formats a concise body from message text, and sends FCM only when allowed.

**Outcome.** Category-aware pushes without waking the client app for logic. Token absence is logged server-side and skipped silently.

## Two Flutter forks without a shared package

**Problem.** Mobile and TV share messaging, auth, analytics, and theme code, but ship on different release tracks and feature sets.

**Approach.** The TV project copied the mobile tree and diverged: 191 shared paths remain, 53 files are still byte-identical. TV adds leanback entry, focus helpers, home loop, and ad slots; mobile adds widgets, workmanager, and richer shell tabs.

**Outcome.** Faster initial TV delivery, but duplicate maintenance. Extracting a private package for repositories and models would be the natural consolidation step.

## Honest limits

- Widget accessibility (TalkBack / VoiceOver on the launcher tile) depends on native widget labels; the split-flap grid inside the app is visually dense.
- Message lifetime and archive retention are fixed durations in client constants; changing them requires app updates unless moved to remote config.
- The TV app shares the same application id namespace as mobile in Gradle configuration; store separation would need careful packaging before any TV release.
- Test coverage emphasises widget sizing and analytics wiring, not exhaustive repository integration tests.
