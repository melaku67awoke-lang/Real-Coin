# RealCoin — Phone-only GitHub Actions APK build

This project is prepared for a GitHub Actions build. The workflow does not require Android Studio on the phone.

## Important

Upload the **contents of this `Realcoin1-main` folder** to the root of a GitHub repository so that `.github/workflows/build-apk.yml` is at the repository root.

This project currently does not include the Gradle Wrapper JAR/scripts, so the workflow installs Gradle 9.3.1 directly with `gradle/actions/setup-gradle` and runs `gradle :app:assembleDebug`.

## Build from phone

1. Create a GitHub repository.
2. Upload all files/folders inside `Realcoin1-main` to the repository root.
3. Commit to `main`.
4. Open the repository's **Actions** tab.
5. Open **Build RealCoin APK**.
6. The workflow can run automatically after a push, or use **Run workflow**.
7. After a successful run, open the run and download the **RealCoin-debug-apk** artifact.
8. Extract the artifact on the phone and install the APK.

This is a debug APK intended for testing. A production/release APK needs a secure signing key and release signing configuration.
