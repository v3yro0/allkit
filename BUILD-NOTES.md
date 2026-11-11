Delivered November 11, 2026. Windows portable launch verified; Android debug APK signature verified; 7 logic tests and 10 browser integration tests passed. iPhone project supplied, not compiled or signed. See allkit-source/docs/VALIDATION.md for test coverage and device-testing limits.

# allkit · v1 beta

A midnight-themed toolkit built from your supplied icon: 25 general tools, 5 fun features, and 10 discoverable easter eggs. Shared React interface, native Capacitor projects for iPhone and Android, and Electron for Windows.

## What is ready

- **Windows:** portable x64 beta executable. No installation required. It is unsigned; Windows may show an unrecognized-publisher notice.
- **Android:** debug-signed APK for testing, targeting Android 16 / API 36, minimum Android 7 / API 24. This is not a Google Play release build.
- **iPhone:** generated Xcode project with app icon, launch screen, permission descriptions, privacy manifest, and Swift Package Manager configuration. **No IPA has been built or signed on this Windows computer.** A Mac with Xcode 26+ and a signing team are required for device distribution. iOS deployment target: 15.0.

No app has been submitted to an app store or published. The default bundle identifier is `app.allkit.toolkit`; choose an identifier you control before release.

## Run and develop

Use Node.js 24 or newer (required by the selected scanner dependency). This build was also successfully compiled and browser-tested on the available Node.js 22.23.1 host, which emits an engine warning for that dependency. Prefer Node 24 for reproducible future development.

```sh
npm ci
npm run dev
```

The development URL appears in the terminal. Production assets are produced with `npm run build`.

```sh
npm test
npx playwright install chromium
npm run build
npm run test:ui
```

## Windows build

```sh
npm run build:windows
```

Find the portable executable under `release/`. For a local desktop run, use `npm run build` then `npm run desktop`. Signing requires your own Windows code-signing certificate; none is included. The renderer has Node integration disabled, context isolation enabled, and Chromium sandboxing enabled. External navigation and pop-up windows are blocked.

## Android build

Install Android Studio, JDK 21, Android SDK platform 36, and the corresponding build tools. Set `ANDROID_HOME` or configure `android/local.properties` for your machine.

```sh
npm ci
npm run sync
cd android
./gradlew assembleDebug
```

On Windows, use `gradlew.bat assembleDebug`. The APK is `android/app/build/outputs/apk/debug/app-debug.apk`. `scripts/setup-android-build.ps1` is an optional portable setup helper; it downloads official Java/SDK tools into the adjacent work directory and accepts SDK licenses. Use the standard Android Studio workflow if you prefer to manage those tools yourself.

To install the supplied test APK, transfer it to your Android device and open it, allowing installation for the file source if you choose. Camera and notification access are requested only when those tools are used. No phone was connected for physical device testing during this build.

For Google Play, generate a release keystore and configure Android release signing privately, then build `bundleRelease`. The debug key must not be used for production distribution.

## iPhone build on a Mac

```sh
npm ci
npm run sync
npm run ios
```

In Xcode, open `ios/App/App.xcodeproj`, select the **App** target, set your signing team under **Signing & Capabilities**, select your iPhone or simulator, and run. Update the bundle identifier in the Capacitor config and both native projects if needed. For TestFlight, choose a device destination, archive, and distribute using your Apple Developer account. Signing credentials are intentionally not stored in this project.

The included GitHub Actions workflow can build Windows and Android artifacts and an **unsigned iOS Simulator app** on a Mac runner. It has not been run remotely. A simulator app cannot be installed on an iPhone; device signing is a separate step.

## Feature behavior and limits

All 30 tool screens have working implementations:

- Math: standard and scientific calculators, percentages, discounts, tip splitting.
- Time: stopwatch with laps, countdown, one-time notification alarm, age and date calculators, world clocks, timezone conversion with daylight-saving handling.
- Conversion: length, mass, volume, area, speed, time, data and temperature; currency reference rates via Frankfurter, with dated local caching.
- Everyday: QR generation with native image sharing, QR and barcode decoding from camera or photo, cryptographic password and random-number generation, compass and calibrated ruler.
- Creative: color selection, three palette harmonies, Unicode-aware word counting and text case conversion.
- Play: magic 8 ball, equal-probability wheel, reaction timer, saved virtual pet, and configurable dice.

Important beta limits:

- **Timers and alarms:** on Windows/browser previews, allkit must remain open and the computer awake. Mobile builds schedule local notifications. Sound, Focus modes, permissions, exact-alarm settings and battery policies affect delivery. This is not a system wake-up alarm service. Alarms are one-time; one countdown and one alarm can be scheduled at once.
- **Compass:** depends on an absolute orientation sensor exposed by the device. Typical Windows PCs have no compatible sensor. It shows an unavailable message rather than a fabricated heading. Hardware behavior needs testing on physical iPhone/Android devices.
- **Ruler:** requires calibration against a standard 85.60 mm card. Recalibrate after display scaling, zoom, orientation or screen changes. It is not a precision measurement instrument.
- **Currency:** internet is needed for new rates. Saved rates are explicitly marked and dated. Reference rates are not bank transaction quotes. Other tools work without network access.
- **Scanning:** decoded data is displayed as text; links are never opened automatically. Barcode scanning does not perform product or price lookup.
- **QR export:** native sharing uses a temporary app-cache PNG. Windows downloads the PNG. Physical-device sharing has not been exercised here.
- **Date conventions:** date differences exclude the start date. A February 29 birthday reaches the next completed year on March 1 in a non-leap year.

## Privacy

No account, advertising or analytics. Favorites, recent tools, discoveries, themes, timers, pet stats and calibration are saved locally. Passwords and typed tool text are not saved. Camera frames and selected photos are processed locally. Currency conversion contacts `api.frankfurter.dev` with currency codes; the entered amount is calculated locally and is not sent. Android app backups are disabled. iOS system backups may include local preferences according to the user’s device settings.

## Files

- `src/` — interface, all tools, shared logic and easter eggs
- `tests/` — calculation and browser integration tests
- `android/` — native Android Gradle project
- `ios/` — native iPhone Xcode project
- `electron/` — isolated Windows app shell
- `public/` — supplied icon and generated platform assets
- `docs/SECRETS.md` — easter egg walkthrough (spoilers)
- `.github/workflows/build.yml` — optional CI builds

Dependencies retain their respective licenses. Your supplied icon is included unchanged as the source artwork, with resized derivatives for platform packaging.

Official build references: [Capacitor environment setup](https://capacitorjs.com/docs/getting-started/environment-setup), [local notification behavior](https://capacitorjs.com/docs/apis/local-notifications), [Electron distribution](https://www.electronjs.org/docs/latest/tutorial/distribution-overview), [Frankfurter reference rates](https://frankfurter.dev/).
