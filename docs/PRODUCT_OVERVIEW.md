# Product Overview

## What Hearth is

Hearth (Hearthboard) is a social messaging app built around a split-flap board aesthetic. Users compose very short uppercase messages and send them to friends. Recipients see those messages in an in-app feed and, when they add the home-screen widget, on their launcher as a rendered board card that matches the app theme.

The product also supports a partner board for a paired relationship, reactions, board themes, archives, and optional display on a living-room TV after the phone pairs with the TV app. The Android phone and tablet build is listed on Google Play under the title **Hearth**. An Android TV companion app exists in engineering form but is **not publicly released**; this showcase describes it as unreleased and does not link to a TV store listing.

The Flutter mobile client also targets iOS, including a separately sized widget render path. Only the Google Play listing is referenced in the [README](../README.md); this document does not claim other store availability.

## Core loop

1. **Sign in** with email or a social provider supported by Firebase Auth.
2. **Add friends** through search, requests, and invite links.
3. **Compose** a short message (character caps and daily send allowances depend on subscription tier).
4. **Deliver** to one or more friends or to a partner; the app may queue sends in the background while the user keeps browsing.
5. **Receive** messages in the feed; the home-screen widget updates to the latest relevant post using pre-rendered images attached to each message.
6. **Optional TV** (unreleased TV app): the phone scans a QR code or enters a pairing code shown on the TV, then chooses which friend boards may appear on that display.

Messages expire after a fixed lifetime and can move into archives with a separate retention window. Exact timings and numeric quotas are product tuning and are not published here.

## Home-screen widget

The widget is not a live Flutter view on the home screen. The sender (or the app on behalf of the receiver) renders the same split-flap card used in the feed to an off-screen canvas, uploads light and dark variants (and platform-specific aspect ratios), and stores URLs on the message. The native widget reads those URLs and shows the bitmap that matches the user’s current theme.

Placeholder states exist when no friend has posted yet (“waiting for a friend”) and for the partner widget variant.

<table>
  <tr>
    <td align="center"><img src="../assets/screenshots/hearthboard-widget-prompt.webp" width="260" alt="Widget onboarding prompt over the feed"/><br/><sub>Widget onboarding prompt</sub></td>
    <td align="center"><img src="../assets/screenshots/hearthboard-my-board.webp" width="260" alt="My Hearth Board preview with an empty split-flap grid"/><br/><sub>My board preview (empty grid)</sub></td>
  </tr>
</table>

## Social graph and safety

Friends can be searched, requested, blocked, and removed. Users can hide specific boards from individual friends when sending to TV (mobile feature tied to pairing). Notification preferences control message, partner, friend-request, and reaction alerts; pushes respect those toggles when the backend notification handler runs.

## Partner board

Separate from the friends feed, a partner board supports back-and-forth messages with higher exchange limits on paid tiers. Partner messages can use the partner home-screen widget slot.

## Subscriptions

Three plan levels (basic, pro, max) change character limits, board column width, daily send counts, partner exchange caps, and how often a user may push custom content to a paired TV. Entitlements are enforced in the client against stored profile fields; server-side receipt validation would be a natural hardening step. Prices and trial wording are not quoted in this repository.

## TV pairing (unreleased TV app)

On the phone, a dedicated TV section lists connected displays, supports claiming a code, renaming, disconnecting, and sending a test board. On the TV (engineering build), a QR-led flow creates a pending pairing record until the phone claims it. Once paired, the TV home experience loops friend boards in a landscape, leanback layout with D-pad focus, owner header, and empty or sleep states when nothing is available.

No Android TV gameplay or feed screenshots are published here; behaviour is described from the engineering sources and illustrated by diagrams and launcher banner artwork labelled as artwork.

## Onboarding and education

First-run flows cover how the product works, widget installation, and tips. Screenshots in this showcase are real captures from the Android build, cropped to app content, with sample placeholder usernames and empty feeds where applicable.

<table>
  <tr>
    <td align="center"><img src="../assets/screenshots/hearthboard-how-it-works.webp" width="230" alt="How it works explanatory screen"/><br/><sub>How it works</sub></td>
    <td align="center"><img src="../assets/screenshots/hearthboard-onboarding-tip.webp" width="230" alt="Onboarding tip screen"/><br/><sub>Onboarding tip</sub></td>
    <td align="center"><img src="../assets/screenshots/hearthboard-feed-empty.webp" width="230" alt="Empty friends feed"/><br/><sub>Empty feed</sub></td>
  </tr>
</table>

## What this document is not

This is a software product description for a portfolio showcase. It does not promise user counts, ratings, or message delivery times, and it does not describe health, wellness, or safety outcomes.
