# DW Personal Trainer – Native App Wrapper

Capacitor wrapper that loads the live web app (`https://app.dw-personal-trainer.de`) inside a native Android/iOS shell.

## What's set up

- `capacitor.config.json` – appId `de.dwpersonaltrainer.app`, appName "DW Personal Trainer", server URL pointed at the live site.
- `android/` – full native Android Studio project (Gradle), app icons + splash screen generated from your assets.
- `ios/` – full native Xcode project, app icon + splash screen generated from your assets.
- App icons/splash were generated from `assets/capacitor-icon*.png` and `assets/capacitor-splash-2732.png` using `@capacitor/assets`.

## Getting it running

1. Unzip, then `npm install` in the project root (regenerates `node_modules`, which was stripped from this zip to keep it small).
2. `npx cap sync` any time you change `capacitor.config.json` or the assets.
3. **Android**: `npx cap open android` (requires Android Studio) → Run on a device/emulator, or Build > Generate Signed Bundle/APK for the Play Store.
4. **iOS**: `npx cap open ios` (requires a Mac + Xcode) → set your Apple Developer Team in Signing & Capabilities, then Run or Archive for App Store submission.

## Still to do before a store submission

- Apple Developer Program ($99/yr) and a Google Play Developer account (one-time $25) if you don't have them yet.
- Code signing: an iOS distribution certificate + provisioning profile, and an Android upload keystore (`keytool -genkey ...`) — keep the keystore file and password safe, it can't be recovered.
- Store listing assets: screenshots per device size, a privacy policy URL (referencing DSGVO/GDPR handling, since the app talks to your backend), and app description/keywords.
- Since this wrapper loads a live remote URL, double check Apple's guidelines on "web wrapper" apps — Apple sometimes requires the app to offer meaningful native functionality beyond a plain webview (e.g. push notifications, native onboarding) to avoid rejection under App Store Review Guideline 4.2.
