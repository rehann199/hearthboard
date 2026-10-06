# Privacy and Source

## What this repository is

A portfolio showcase for a production application listed on Google Play as **Hearth** (Hearthboard). It describes engineering across the mobile Flutter client and an Android TV companion that is **not publicly released**. It is not a source distribution.

## What is published

- Written descriptions of architecture, design decisions and trade-offs
- Original diagrams in `assets/diagrams/`
- Real screenshots from the Android mobile build, cropped to app content, captured with placeholder/sample data, re-encoded to WebP with metadata removed
- The app icon and Android TV launcher banner artwork (banner labelled as artwork, not a TV screenshot)

## What is intentionally not published

- Production source code, in whole or in part
- Message quotas, cooldown durations, character caps, and other economy tuning values
- Build and signing configuration, certificates and keystores
- Firebase and Google service configuration, API keys and tokens
- Ad unit identifiers and mediation account details
- Billing product identifiers and store-internal configuration
- Database collection names, security rule contents, Cloud Function source, and backend URLs
- Version control history of the production repositories
- User data of any kind

## Data the live app handles (conceptual)

Signed-in users have profiles stored in a cloud database. Messages contain text, participant identifiers, expiry metadata, and URLs pointing to hosted widget preview images. Device tokens for push delivery sit on user profiles. Notification preference maps control which push categories fire. Pairing records link TV displays to owner accounts. Analytics events are sent to Firebase according to the instrumentation embedded in the clients.

This showcase does not state that data is anonymous, encrypted end-to-end, or compliant with specific regulations unless such properties were verified and spelled out in production legal copy. Users should read the in-app privacy policy for authoritative wording.

## Notes on figures

Line counts cover Dart files under `lib/` in each Flutter project. Shared-path and byte-identical file counts compare relative paths under `lib/` between the mobile and TV trees. Diagram labels are conceptual and are not production identifiers. No download counts, ratings, revenue, or performance timings are claimed.

## Images

- **Mobile screenshots** are real captures from the Android build with sample or empty content; they were cropped to remove system status areas where applicable.
- **Android TV** is represented only by launcher banner artwork labelled as artwork. No TV UI screenshots are published.
- **Diagrams** are original vector illustrations created for this showcase.

## Scope limits

The showcase does not document every feature flag, experimental branch, or iOS store status. Only the Google Play URL given in the README is linked as a public store listing.

## Third-party services

The production clients integrate Firebase for authentication, structured storage, file hosting, messaging delivery, analytics, crash reporting, and performance traces. Widget preview images are uploaded through a client-side image hosting integration so launcher tiles can load HTTPS bitmaps without running Flutter on the home screen. Store billing uses the platform in-app purchase APIs on whichever stores each build targets.

This showcase names those product categories only at a high level. It does not reproduce integration keys, bucket names, upload presets, or analytics event identifiers from the private repositories.

## Relationship to the unreleased TV build

Documentation here describes Android TV engineering behaviour so reviewers can understand pairing and ambient display design. Because the TV app is not publicly released, no TV store URL appears in the README, and no TV interface screenshots are included. Launcher banner artwork is labelled explicitly as artwork rather than a capture from a shipping build.

## Access

Source access for hiring or due-diligence purposes can be discussed at the owner's discretion.
