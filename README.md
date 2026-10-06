<div align="center">

<img src="assets/branding/app-icon.webp" alt="Hearth app icon" width="110" />

# Hearth (Hearthboard)

**A split-flap social board for very short messages: friends see posts in-app and on a home-screen widget; an engineering Android TV build pairs by QR for ambient living-room display (TV app not publicly released).**

**Android phone and tablet · iOS client · Android TV (unreleased engineering build)**<br/>
Flutter · Dart · Kotlin · Swift · Firebase · Play Billing · StoreKit · Cloudinary

![Flutter](https://img.shields.io/badge/Flutter-Dart-02569B?logo=flutter&logoColor=white)
![Android](https://img.shields.io/badge/Android-3DDC84?logo=android&logoColor=white)
![iOS](https://img.shields.io/badge/iOS-000000?logo=apple&logoColor=white)
![Android TV](https://img.shields.io/badge/Android%20TV-unreleased-D9822B?logo=androidtv&logoColor=white)
![Firebase](https://img.shields.io/badge/Firebase-FFCA28?logo=firebase&logoColor=black)

<table>
  <tr>
    <td><img src="assets/screenshots/hearthboard-feed-empty.webp" width="300" alt="Friends feed with an empty split-flap card and bottom navigation" /></td>
    <td><img src="assets/screenshots/hearthboard-my-board.webp" width="300" alt="My Hearth Board preview with an empty three-row grid" /></td>
  </tr>
</table>
<sub>Real screenshots from the Android build, cropped to the app content, captured with sample or empty data, metadata removed. No Android TV UI screenshots are published in this repository.</sub>

</div>

---

## Get the App

<p>
  <a href="https://play.google.com/store/apps/details?id=com.hearthboard.hearthboard"><img alt="Get it on Google Play" src="https://img.shields.io/badge/Google%20Play-Listing%20title%20Hearth-414141?style=for-the-badge&logo=googleplay&logoColor=white" /></a>
</p>

The Android TV companion app is **engineered but not publicly released**. There is **no TV store link** in this showcase. TV capability is described in the docs and shown only through launcher banner artwork labelled as artwork.

## Product at a Glance

| | |
|---|---|
| **What it is** | Send short uppercase “board” messages to friends; read them in a feed and on a home-screen widget styled like a split-flap display |
| **Social** | Friend requests, search, block list, reactions, optional partner board |
| **Widget** | Native home-screen widget shows a pre-rendered bitmap of the feed card (light/dark and platform-specific sizes) |
| **TV (unreleased)** | Phone pairs over QR; TV loops friend boards for ambient display after pairing |
| **Platforms** | Mobile Flutter app (Android live on Google Play; iOS in the same codebase). Separate Flutter fork for Android TV (not released) |
| **Backend** | Firebase Auth, Firestore, Storage, Cloud Messaging, Analytics, Crashlytics, Performance; serverless handler for new-message pushes |
| **Monetisation** | Tiered subscriptions (basic / pro / max) via store in-app purchase APIs |
| **Code size** | About 61,200 lines of Dart in the mobile app and 39,500 in the TV fork (`lib/` each) |

## Showcase

<table>
  <tr>
    <td align="center"><img src="assets/screenshots/hearthboard-how-it-works.webp" width="260" alt="How it works explanatory screen"/><br/><sub><b>How it works</b><br/>Product education</sub></td>
    <td align="center"><img src="assets/screenshots/hearthboard-widget-prompt.webp" width="260" alt="Prompt to add the home-screen widget"/><br/><sub><b>Widget prompt</b><br/>Onboarding overlay</sub></td>
    <td align="center"><img src="assets/screenshots/hearthboard-board-themes.webp" width="260" alt="Board theme selection grid"/><br/><sub><b>Board themes</b><br/>Visual customisation</sub></td>
  </tr>
  <tr>
    <td align="center"><img src="assets/screenshots/hearthboard-board-archives.webp" width="260" alt="Archived boards list"/><br/><sub><b>Archives</b><br/>Past boards</sub></td>
    <td align="center"><img src="assets/screenshots/hearthboard-settings.webp" width="260" alt="Settings menu list"/><br/><sub><b>Settings</b><br/>Account and preferences</sub></td>
    <td align="center"><img src="assets/screenshots/hearthboard-sign-in-background.webp" width="260" alt="Sign-in screen with email and social options"/><br/><sub><b>Sign in</b><br/>Auth entry</sub></td>
  </tr>
</table>

## Two Platforms, Two Codebases

The mobile app optimises for compose, social graph, widgets, and phone-led TV pairing. The TV fork optimises for ten-foot UI, D-pad focus, QR pairing on the display, and an ambient board loop. They share a large overlapping Dart tree (191 paths, 53 byte-identical files) but ship as separate projects.

<p align="center">
  <img src="assets/diagrams/platform-overview.svg" alt="Platform overview: mobile with widgets and unreleased Android TV sharing Firebase services" width="900" />
</p>

<table>
  <tr>
    <td width="50%" align="center"><img src="assets/artwork/android-tv-launcher-banner.webp" width="400" alt="Android TV launcher banner artwork"/><br/><sub><b>Android TV (unreleased)</b>: launcher banner artwork (artwork, not a screenshot)</sub></td>
    <td width="50%" align="center"><img src="assets/screenshots/hearthboard-onboarding-tip.webp" width="260" alt="Onboarding tip screen"/><br/><sub><b>Mobile</b>: onboarding tip (real Android screenshot)</sub></td>
  </tr>
</table>

| | Mobile (Google Play) | Android TV (not released) |
|---|---|---|
| **Primary UX** | Feed, compose, friends, profile | Ambient board loop after pairing |
| **Widget** | Friend and partner home-screen widgets | Full-screen display only |
| **Pairing** | Scan QR / enter code on phone | Show QR on TV |
| **Input** | Touch | D-pad focus |
| **Background work** | Workmanager widget refresh | None equivalent |
| **Ads** | Subscription-focused on mobile paths | Mobile Ads SDK present in TV project |
| **Orientation** | Portrait shell | Landscape |

Full comparison: [docs/MOBILE_VS_TV.md](docs/MOBILE_VS_TV.md).

## Engineering Highlights

### Launcher widgets via off-screen renders
- **Problem:** Home-screen widgets cannot run the full Flutter board layout reliably on Android and iOS.
- **Approach:** Render the same feed card off-screen at fixed sizes (including a distinct iOS aspect ratio), upload light/dark previews, store HTTPS URLs on each message, and let native widget code display the matching bitmap.
- **Outcome:** Feed and widget stay visually aligned; theme changes on already-delivered messages depend on stored previews, not live relayout.

<p align="center">
  <img src="assets/diagrams/message-sync-flow.svg" alt="Flow from compose through upload and cloud write to widget and TV display" width="900" />
</p>

### Sends that do not freeze compose
- **Problem:** Broadcasting to many friends involves multiple writes and uploads.
- **Approach:** A background post queue tracks jobs (friends vs partner), states, and errors while the shell remains navigable.
- **Outcome:** Compose feels instant; failures surface asynchronously.

### QR TV pairing without typing on a remote
- **Problem:** TVs lack a practical keyboard for account linking.
- **Approach:** The unreleased TV app publishes a short code as QR; the signed-in phone claims it, binding owner identity in local TV storage and cloud pairing records both sides subscribe to.
- **Outcome:** Living-room setup stays phone-driven; TV code remains unreleased to the public store.

### Push that honours notification toggles
- **Problem:** Users disable friend or partner message alerts independently.
- **Approach:** A serverless Firestore trigger reads recipient preference maps before sending FCM.
- **Outcome:** Category-aware pushes without duplicating policy in every client version.

### More

| Area | What was built |
|---|---|
| **State** | Provider view models over Firestore repositories |
| **Sync** | Optimistic local-first helper for settings and profile fields |
| **Social** | Friends, requests, blocks, partner board, reactions, archives |
| **TV gating** | Plan-aware quotas for custom TV pushes from the phone |
| **Analytics** | Layered event registry, queues, journey and paywall helpers |
| **Tests** | Mobile widget layout tests and analytics suite; TV widget and ad analytics tests |

Details: [docs/ENGINEERING_CHALLENGES.md](docs/ENGINEERING_CHALLENGES.md).

## Architecture

```mermaid
flowchart TD
    subgraph Phone["Mobile client"]
        CMP["Compose and feed UI"]
        REN["Off-screen card render"]
        NAT["Native home widget"]
    end
    subgraph Cloud["Firebase and serverless"]
        AUTH["Authentication"]
        DB["Firestore messages and profiles"]
        IMG["Hosted widget preview URLs"]
        PUSH["FCM via new-message handler"]
    end
    subgraph TV["Android TV client · unreleased"]
        QR["QR pairing screen"]
        LOOP["Ambient board loop"]
    end
    CMP --> REN
    REN --> IMG
    CMP --> DB
    DB --> PUSH
    PUSH --> NAT
    PUSH --> CMP
    QR --> DB
    DB --> LOOP
    AUTH --> CMP
    AUTH --> TV
```

More diagrams: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

## Honest Trade-offs

- **Two Flutter repos** share many files but not a package; messaging or subscription changes may require dual maintenance.
- **Bitmap widgets** trade bandwidth and storage for launcher fidelity.
- **Client-side tier checks** read plan fields on profiles; server-side receipt validation would tighten enforcement.
- **Android TV** is not shipped; banner artwork here is not evidence of a live TV listing.
- **iOS** shares the mobile codebase and widget pipeline; this README links only the Google Play listing provided for the live Android release.

## Technology Stack

| Layer | Technology |
|---|---|
| UI | Flutter / Dart 3, Provider |
| Mobile widgets | home_widget plugin, native Android providers, iOS extension |
| Backend | Firebase Auth, Firestore, Storage, Cloud Messaging, Analytics, Crashlytics, Performance |
| Push side effects | Node Cloud Functions on Firestore creates |
| Image hosting | Client upload service (CDN) for widget previews |
| Background | workmanager on mobile |
| Billing | in_app_purchase (Play Billing and StoreKit) |
| TV | Leanback manifest, focus wrappers, google_mobile_ads on TV fork |
| QR | mobile_scanner on phone; qr_flutter on TV |

## Documentation

| Read | For |
|---|---|
| [Product overview](docs/PRODUCT_OVERVIEW.md) | User journeys, widget, TV pairing, subscriptions (conceptual) |
| [Technical case study](docs/TECHNICAL_CASE_STUDY.md) | End-to-end engineering narrative |
| [Architecture](docs/ARCHITECTURE.md) | Layers, flows, technology map |
| [Mobile vs TV](docs/MOBILE_VS_TV.md) | Platform comparison (TV labelled unreleased) |
| [Engineering challenges](docs/ENGINEERING_CHALLENGES.md) | Problem → approach → outcome |
| [Privacy and source](docs/PRIVACY_AND_SOURCE.md) | What is and is not published |

## Source Code

This repository is a portfolio showcase for a production application. Production source code is maintained privately. See [RIGHTS.md](RIGHTS.md) for usage terms.
