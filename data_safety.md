# Play Console: Data safety answers (v1.0.0)

These reflect what the app does today: no account, photos stay on the device, and the AdMob banner on the gallery. Re-check them whenever you add a feature (cloud backup, team mode, analytics).

## Overview questions
| Question | Answer |
|---|---|
| Does your app collect or share any of the required user data types? | **Yes** (because of AdMob, and location sent for address/map lookup) |
| Is all user data encrypted in transit? | **Yes** (all requests use HTTPS) |
| Do you provide a way for users to request that their data is deleted? | **No account exists**: answer per Play Console wording. Photos and settings are deleted by deleting them in the app or uninstalling. |

## Data types

### Location
| Type | Collected | Shared | Processed ephemerally | Required | Purpose |
|---|---|---|---|---|---|
| Approximate location | Yes | Yes (to the map tile provider and to Google AdMob) | Yes | Required | App functionality; Advertising (AdMob) |
| Precise location | Yes | No | Yes | Required | App functionality (address lookup, map snapshot) |

Why: coordinates go to the phone's geocoder service for the address and to MapTiler for the map image. Nothing is stored on any ProofCam server (there isn't one).

### Device or other IDs
| Collected | Shared | Ephemeral | Required | Purpose |
|---|---|---|---|---|
| Yes | Yes (Google AdMob) | No | Required | Advertising or marketing; Fraud prevention, security and compliance |

### App activity → App interactions
| Collected | Shared | Ephemeral | Required | Purpose |
|---|---|---|---|---|
| Yes | Yes (Google AdMob) | No | Required | Advertising or marketing; Analytics |

### App info and performance → Diagnostics
| Collected | Shared | Ephemeral | Required | Purpose |
|---|---|---|---|---|
| Yes | Yes (Google AdMob) | No | Required | Advertising or marketing; Analytics |

### Not collected
Photos and videos (stay on device), personal info (name, email, phone), contacts, files and docs, messages, financial info, health, audio.

Before you submit, cross-check the AdMob rows with Google's current guidance: support.google.com/admob → "Data disclosure" for the Google Mobile Ads SDK.
