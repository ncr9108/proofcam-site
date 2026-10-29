# ProofCam: release checklist (Phase 1)

Work top to bottom. Items marked **[you]** need your accounts or a phone.

## 1. Accounts and keys [you]

| What | Where | Goes into |
|---|---|---|
| MapTiler key (free tier) | cloud.maptiler.com → API keys. Restrict it to the Android app ID `com.proofcam.proofcam` | `config/prod.json` → `MAPTILER_KEY` |
| AdMob app | admob.google.com → Apps → Add app (Android, not yet listed) | App ID → `android/gradle.properties` → `admobAppId=` |
| AdMob banner unit | AdMob → your app → Ad units → Banner | Unit ID → `config/prod.json` → `ADMOB_BANNER_ID` |
| Play Console | play.google.com/console (one-time $25) | n/a |

```bash
copy config\prod.example.json config\prod.json   # then fill in the two values
```

`config/prod.json` is git-ignored. Debug builds use Google's test ads automatically.

## 2. Upload key (one time) [you]

```bash
mkdir D:\APP_WORK\P\keys
keytool -genkey -v -keystore D:\APP_WORK\P\keys\proofcam-upload.jks -keyalg RSA -keysize 2048 -validity 10000 -alias upload
copy android\key.properties.example android\key.properties   # fill in the passwords
```

Keep the `.jks` file and both passwords in a password manager, and keep a backup outside the project folder. Enrol in **Play App Signing** when you create the app in Play Console (the default), so Google holds the real signing key.

## 3. Build the release bundle

```bash
flutter clean
flutter pub get
flutter test
flutter analyze
flutter build appbundle --release --dart-define-from-file=config/prod.json --obfuscate --split-debug-info=build/symbols
```

Output: `build/app/outputs/bundle/release/app-release.aab`. Keep `build/symbols` for this version so crash stacks can be decoded.

Quick device check of the exact release build:

```bash
flutter run --release --dart-define-from-file=config/prod.json
```

## 4. Low-end device test [you]

Test on at least one 3–4 GB RAM phone (Redmi A/9 series, Realme C series, Samsung Galaxy A0x/M1x) plus your Moto. Use the release build.

**Install and onboarding**
- [ ] Fresh install shows onboarding; "Allow & start" asks for camera and location
- [ ] Deny both → camera screen shows "Camera access needed" and the GPS chip says "Location permission needed · Fix"; both recover after allowing

**Camera**
- [ ] Preview opens in under 2 s, isn't stretched, and the stamp sits at the bottom of the preview
- [ ] Tap to focus shows the amber ring
- [ ] Flash off/auto/on, grid toggle and front/back switch all work
- [ ] App to background and back (and lock/unlock) → preview comes back

**Location and stamp**
- [ ] GPS chip goes from "Getting GPS…" to "GPS ±N m" within about 10 s outdoors
- [ ] Address, map and coordinates appear; the clock ticks
- [ ] Turn location off in quick settings → chip shows "Location is off · Fix" → tap opens settings → turning it on recovers
- [ ] Airplane mode: stamp still shows coordinates and time; the map shows the placeholder; nothing crashes

**Capture**
- [ ] Shutter frees up immediately; the thumbnail shows a spinner while saving
- [ ] Take 5 photos quickly: all save, in order, with no crash or out-of-memory error
- [ ] Time from shutter to saved: note it (target under 4 s on low-end)
- [ ] Saved photo: upright, stamp matches the preview, readable text
- [ ] Photo appears in Google Photos / Gallery → Pictures → ProofCam
- [ ] Google Photos "Details" shows the location (EXIF GPS)

**Gallery and viewer**
- [ ] Grid loads fast with 50+ photos
- [ ] Viewer pinch-zoom, Share (WhatsApp) and Open in Maps work
- [ ] Delete removes it from the app; the gallery copy stays
- [ ] Banner ad shows at the bottom of the Gallery only (test ad in debug)

**Settings**
- [ ] Classic/Advanced, decimal/DMS, date formats and each toggle update the preview live
- [ ] Settings survive an app restart

Record the results (phone model, Android version, pass/fail, notes). Anything that fails, send me the log.

## 5. Play Console setup [you]

1. Create app: **ProofCam: GPS Map Camera**, App, Free, default language English (India).
2. **Store listing:** text from `docs/play_store_listing.md`, `docs/play_icon_512.png`, `docs/feature_graphic_1024x500.png`, and 4–8 phone screenshots (take them on the Moto: camera with stamp, settings, gallery, viewer, a saved photo).
3. **Privacy policy URL:** host `docs/privacy_policy.html` (GitHub Pages, Google Sites or your website) and paste the URL. Fill in the `[placeholders]` first.
4. **App content:**
   - Ads: Yes
   - App access: All functionality available without special access
   - Content rating: questionnaire → Utility / Productivity, no objectionable content
   - Target audience: 18+ (keeps you out of the Families ad rules)
   - Data safety: answers in `docs/data_safety.md`
   - Government app: No. Financial features: None. Health: No.
5. **app-ads.txt:** AdMob shows a line to publish at `https://<your-domain>/app-ads.txt`. Your website domain must also be the developer website in Play Console.
6. **Testing → Closed testing:** upload the `.aab`, add testers' emails (personal accounts need 12+ testers opted in for 14 days before production access), and roll out.

## 6. After the closed test

- Fix issues from testers, bump the version in `pubspec.yaml` (`1.0.1+2`), rebuild and upload
- Apply for production access once the 14-day test is met
