# Technical Case Study

This case study walks through how Hearth implements short social messages, home-screen widgets, and an unreleased Android TV ambient display from two related Flutter codebases.

## Product shape

Users think in “boards”: three rows of split-flap cells showing uppercase lines sliced to a per-plan character width. Friends see each other’s boards in a scrollable feed; partners have a dedicated exchange. Everything is intentionally brief and ephemeral compared to chat apps: messages expire, archives roll off after a retention window, and the UI reinforces the mechanical board metaphor with flip sounds and themed wood frames.

The mobile app is live on Google Play as **Hearth**. The Android TV fork is complete enough to pair, stream boards, and show ads in engineering builds, but it is **not publicly released** and has no store link in this repository.

## Client architecture

Both apps use Flutter with Provider view models. Navigation is primarily imperative: splash and onboarding routes into a signed-in shell. There is no declarative router package; instead, services hold tab indices and navigation helpers for deep links and post-send flows.

Feature folders follow a consistent layout: models, repositories talking to Firestore, view models, views, and widgets. A large `core` tree supplies theme, analytics, connectivity guards, storage singletons, and shared UI primitives (glass bottom sheets, shimmer loading, branded buttons).

### Scale

| Codebase | Dart files under `lib/` | Lines under `lib/` |
|---|---|---|
| Mobile | 299 | about 61,200 |
| Android TV fork | 214 | about 39,500 |
| Shared paths (both) | 191 paths overlap | 53 byte-identical files |

Counts are static line totals from the production trees; they are not performance metrics.

## Messaging and rendering

A message carries text, sender and recipient identifiers, usernames for display, expiry timestamps, plan-derived character limits, optional partner flag, and URLs for pre-rendered widget images (light/dark, Android aspect, iOS aspect, and per-board-theme maps for newer clients).

Sending flows through a repository that writes Firestore documents and may attach broadcast identifiers when fanning out to multiple friends. Receivers merge stream updates into feed view models; the home widget service selects the correct URL set, downloads if necessary, and pushes bytes into native widget storage keys before calling platform update methods.

Rendering reuses the same `HearthPostCard` widget used in the feed, laid out at fixed logical sizes so label clamps match between in-app and launcher presentation. Waiting states use constant placeholder posts so empty widgets still look on-brand.

## Widget platform differences

Android registers two provider classes (friend and partner widgets). iOS uses the plugin’s named widget entry. Because iOS systemMedium frames differ from Android cell grids, the app maintains parallel upload pipelines and duplicate URL fields on messages for backward compatibility with older clients that only stored the Android-shaped previews.

Workmanager schedules refresh work on mobile when pushes arrive while the app is backgrounded, reducing stale launcher tiles.

## Social graph and moderation

Friends flow through search, pending requests, acceptance, block lists, and hidden-board lists for TV visibility. Repositories map Firestore documents into immutable model classes; view models expose sorted lists and loading gates for shimmer placeholders.

Invite and message links use app link handling to route into the correct screen when installed; exact hostnames are not published here.

## Subscriptions

The in_app_purchase plugin wraps Play Billing and StoreKit. A subscription service listens to purchase streams, normalises plan names (basic, pro, max), and writes entitlements back to the user profile through the user repository. Paywall and plan screens are shared or forked between mobile and TV; TV includes download prompts that explain phone-only features.

Analytics modules track paywall impressions, purchase attempts, and subscription lifecycle events through the shared analytics dispatcher.

## TV engineering (unreleased)

The TV manifest requires leanback and exposes a TV launcher banner (artwork in this showcase). On cold start, local storage reveals whether a pairing exists. Unpaired devices land on a QR pairing screen driven by a view model with explicit UI phases: show code, wait for claim, success, or error with regeneration.

Paired devices construct a boards view model scoped to the owner UID and optional pair code, then render a large-format home experience: owner header, focusable controls, animated transitions between posts, empty states, and sleep screens when the stream is quiet. Split-flap sounds reuse the shared audio service.

Mobile complements this with QR scanning, manual code entry, connected-TV lists, rename/disconnect actions, quota-aware “push to TV” sheets, and friend-level hide lists.

## Backend and push

Authentication uses Firebase Auth (email and third-party providers configured in the console). Primary structured data lives in Firestore; binary assets use Firebase Storage and/or a dedicated image CDN upload service on the client.

Cloud Functions (Node, v2 Firestore triggers) listen for new message documents, honour notification preference maps on recipient profiles, truncate bodies for notification text, and send FCM messages to stored device tokens. This keeps notification policy centralized even when senders use different app versions.

## Analytics depth

Both apps ship an unusually large analytics subdirectory: registries, validators, queues, journey and revenue helpers, HTTP interceptors, and widget wrappers that auto-log impressions. TV adds ad-specific analytics tests. This reflects a product decision to instrument funnels deeply rather than a claim about how data is consumed operationally.

## Testing

Mobile includes widget tests (including intrinsic height checks on the post card) and an analytics test suite. TV includes widget smoke tests plus analytics and ad analytics tests. There is no published claim that CI always passes; the showcase only notes that these files exist.

## Trade-offs worth naming

- **Bitmap widgets** simplify launcher integration but increase upload bytes per send and complicate theme changes after delivery.
- **Forked repos** accelerated TV but duplicate fixes across 191 overlapping paths.
- **Client-side tiers** keep UX responsive; tying tiers to verified receipts on every write would be stronger.
- **Unreleased TV** means this document describes engineering intent, not shipped TV user experience in the wild.

## Where to read next

- [Architecture](ARCHITECTURE.md) for diagrams and layer table
- [Mobile vs TV](MOBILE_VS_TV.md) for platform comparison
- [Engineering challenges](ENGINEERING_CHALLENGES.md) for problem statements
- [Privacy and source](PRIVACY_AND_SOURCE.md) for publication boundaries
