# 12 — Store forms: exact answers (v1.0.0)

Answers derived from the shipped code and the merged release manifest
(2026-09-28). Re-check this file whenever an SDK or data flow changes.
Legal wording is the owner's call; this file states facts about the app.

## Facts the answers rest on

| Data | Where it goes | Who receives it |
|---|---|---|
| Chats, attachments, settings | SQLite on the device | nobody (export only on user action) |
| API keys | Keychain / Keystore | only the user's own AI provider, on each request |
| Message text + attachments | HTTPS to the provider the user configured | the user's provider (user-initiated) |
| Advertising ID, IP-derived coarse location, ad interactions, diagnostics | Google AdMob + UMP | Google — **only for users who have not bought Remove ads** |
| Crash reports (scrubbed, no PII) | Sentry | developer — **only after the user opts in** (default off) |
| Report: category, app version, OS, optional note, optional AI message text | report.h93lab.com (Cloudflare Worker, 90-day retention, IP never stored) | developer — only when the user submits a report |
| Microphone audio | OS speech recognizer (Google on Android, Apple on iOS) | the OS vendor — the app never receives or sends audio itself |
| Purchase | Google Play Billing / StoreKit | the store |

Merged Android permissions: INTERNET, RECORD_AUDIO, BILLING, AD_ID,
ACCESS_ADSERVICES_* (AdMob), ACCESS_NETWORK_STATE, WAKE_LOCK,
FOREGROUND_SERVICE (WorkManager, pulled in by AdMob).

## Google Play Console

### App content → Advertising ID
- Does your app use advertising ID? **Yes**
- Purpose: **Advertising or marketing** (AdMob). Also tick **Analytics** and
  **Fraud prevention, security, and compliance** (AdMob's own uses).

### App content → Ads
- Contains ads: **Yes**.

### App content → Data safety
- Does your app collect or share any required user data types? **Yes**
- Is all user data encrypted in transit? **Yes**
- Do you provide a way for users to request that their data is deleted?
  **Yes** — Settings → Delete all data (on-device), reports/crash data via
  support@h93lab.com.

| Data type (Play category) | Collected | Shared | Optional? | Purposes |
|---|---|---|---|---|
| Location → Approximate location | Yes | Yes (Google, ads) | Required* | Advertising or marketing; Fraud prevention |
| Device or other IDs | Yes | Yes (Google, ads) | Required* | Advertising or marketing; Analytics; Fraud prevention |
| App activity → App interactions | Yes | Yes (Google, ads) | Required* | Advertising or marketing; Analytics |
| App info and performance → Crash logs | Yes | No | Optional | App functionality; Analytics |
| App info and performance → Diagnostics | Yes | Yes (Google, ads) | Required* | Analytics; App functionality |
| Messages → Other in-app messages | Yes | No | Optional | App functionality (sent to the AI provider the user configured; optional text in a user-submitted report) |

\* "Required" only because a free user cannot switch ads off without buying
Remove ads; mention that in the description.

Not collected: name, email, contacts, precise location, photos (sent only to
the user's own provider as part of a message — covered by "Other in-app
messages"), files, audio (processed by the OS recognizer), financial info
(purchases are processed by Google Play), health, web history.

### App content → Target audience and content
- Target age: **18 and over** (keeps the app out of the Families policy;
  general-purpose AI chat can generate any content).
- Appeals to children: **No**.

### App content → Content rating (IARC questionnaire)
- Category: **Utility / productivity / communication**.
- Users can interact or exchange content: **Yes** (AI-generated content;
  there is an in-app report on every AI message).
- Shares user location with others: **No**. Digital purchases: **Yes**.
- Answer the violence/sexual/language questions **Yes, may occur** where the
  question is about user-generated/AI content that is not controlled by the
  developer. Expect a Teen/Mature rating.

### App content → App access
- **All or some functionality is restricted**: the app needs an AI provider
  key. Provide a funded demo key (OpenRouter, low budget) with the steps in
  "Review notes" below.

### App content → Government apps / Financial features / Health / News
- **No** to all.

## App Store Connect

### App Privacy (nutrition labels)
- Data used to track you: **None** (no ATT prompt, non-personalized ads only).
- Data linked to you: **None**.
- Data not linked to you:
  - **Identifiers → Device ID** — Third-party advertising
  - **Location → Coarse location** — Third-party advertising
  - **Usage data → Product interaction, Advertising data** — Third-party advertising; Analytics
  - **Diagnostics → Crash data, Performance data** — App functionality
    (Sentry, opt-in) and Third-party advertising (AdMob)
  - **User content → Other user content** — App functionality (optional
    report note / AI message text)

### Age rating
- Unrestricted generative AI / web access → answer the generative-AI and
  "unrestricted web access" questions **Yes**; expect **18+**.

### Review notes (paste into App Review Information)
```
HLLM is a native client for the user's own AI provider account (bring your
own key). No sign-in; no developer server sees chats or keys.

Demo: Onboarding → Continue → OpenRouter → paste the API key below →
model openai/gpt-4o-mini → Start chatting.
API key: <DEMO KEY — limited budget, valid until <date>>

1) Send a message; tap the token chip under the reply → Cost receipt.
2) Tap ⋯ under a reply → Inspect request (exact JSON sent, key masked).
3) Tap ⋯ under a reply → Report (in-app AI content report).
4) + → Web search on, ask a current-events question; tap a citation.
5) Menu → Settings → Remove ads ($1.99 non-consumable) / Restore purchases.
6) Menu → Settings → Network & Privacy lists every destination.
Ads: one banner at the bottom of the side menu only, never in chat.
```

### In-App Purchase review
- Screenshot: Settings → Remove ads screen.
- Review note: "Non-consumable. Removes the only ad (side-menu banner) and
  switches the ad SDK off. Restore purchases in Settings."
