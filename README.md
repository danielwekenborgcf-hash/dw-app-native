# DW Personal Trainer – Native App Wrapper

Capacitor wrapper that loads the live web app (`https://app.dw-personal-trainer.de`) inside a native Android/iOS shell.

## What's set up

- `capacitor.config.json` – appId `de.dwpersonaltrainer.app`, appName "DW Personal Trainer", server URL pointed at the live site.
- `android/` – full native Android Studio project (Gradle), app icons + splash screen generated from your assets.
- `ios/` – full native Xcode project, app icon + splash screen generated from your assets.
- App icons/splash were generated from `assets/capacitor-icon*.png` and `assets/capacitor-splash-2732.png` using `@capacitor/assets`.
- `@capacitor/push-notifications` is installed and wired up on Android (permission declared, Google Services Gradle plugin auto-applies once a real `google-services.json` is present). See `PUSH_SETUP.md` for the remaining Firebase setup + the JS snippet needed in the web app to register for push.
- `.github/workflows/build-android.yml` builds an installable debug APK automatically on every push — see below.
- `.github/workflows/build-ios.yml` builds an unsigned iOS `.ipa` on demand (manual trigger) — see "Installing on your phone without a Mac (iOS)" below.

## Installing on your phone without an app store (Android)

This project includes a GitHub Actions workflow (`.github/workflows/build-android.yml`) that builds an installable debug APK automatically in the cloud — no Android Studio, no Play Store, no signing setup needed:

1. Create a new (free, can be private) GitHub repository and push this project's contents to it.
2. GitHub builds the APK automatically (check the "Actions" tab — takes ~3-5 minutes). You can also trigger it manually via the "Run workflow" button.
3. Once the run finishes, open it and download the `dw-personal-trainer-debug-apk` artifact (a zip containing `app-debug.apk`).
4. Transfer `app-debug.apk` to your Android phone (e.g. via a cloud drive, email, or USB) and open it. Android will ask you to allow "install from unknown sources" the first time — that's expected since it's not from the Play Store.

No Apple Developer account, no Play Console account, no keystore needed for this path — the debug build is self-signed automatically by Gradle.

See `PUSH_SETUP.md` for wiring up real push notifications (a free Firebase project is required for that part).

## Installing on your phone without a Mac (iOS)

This project also includes a manually-triggered GitHub Actions workflow (`.github/workflows/build-ios.yml`) that builds an unsigned `.ipa` in the cloud on a macOS runner — no Mac purchase and no paid Apple Developer account needed to build it. You then sign and install it onto your own iPhone from Windows using a free tool (Sideloadly or AltStore) and your regular, free Apple ID. Full step-by-step instructions (German) are in `IOS_SIDELOAD.md`.

Unlike the Android workflow, this one does not run automatically on every push (macOS build minutes are more expensive) — trigger it manually from the "Actions" tab whenever you want a new iOS build.

## Building manually instead

1. Unzip, then `npm install` in the project root (regenerates `node_modules`, which was stripped from this zip to keep it small).
2. `npx cap sync` any time you change `capacitor.config.json` or the assets.
3. **Android**: `npx cap open android` (requires Android Studio) → Run on a device/emulator, or Build > Generate Signed Bundle/APK for the Play Store.
4. **iOS**: `npx cap open ios` (requires a Mac + Xcode) → set your Apple Developer Team in Signing & Capabilities, then Run or Archive for App Store submission. (Installing on an iPhone without the App Store — e.g. via TestFlight or ad-hoc signing — still requires a Mac + Xcode and an Apple Developer account; this isn't possible from this cloud sandbox.)

## Still to do before a store submission

- Apple Developer Program ($99/yr) and a Google Play Developer account (one-time $25) if you don't have them yet.
- Code signing: an iOS distribution certificate + provisioning profile, and an Android upload keystore (`keytool -genkey ...`) — keep the keystore file and password safe, it can't be recovered.
- Store listing assets: screenshots per device size, a privacy policy URL (referencing DSGVO/GDPR handling, since the app talks to your backend), and app description/keywords.
- Since this wrapper loads a live remote URL, double check Apple's guidelines on "web wrapper" apps — Apple sometimes requires the app to offer meaningful native functionality beyond a plain webview (e.g. push notifications, native onboarding) to avoid rejection under App Store Review Guideline 4.2.
