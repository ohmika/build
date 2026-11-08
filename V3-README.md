# GitHub Build Prep V3 (development)

A browser-only ZIP preparation tool for Android and Wear OS projects. ZIP parsing and rewriting happen locally in the browser; project files are not uploaded by the page.

## V3 changes in progress

- Removed the embedded signing keystore from the public HTML source and generated project ZIPs.
- Added optional stable debug signing through the GitHub Actions repository secret `ANDROID_DEBUG_KEYSTORE_BASE64`.
- APK collection now searches all Gradle modules/build variants and avoids overwriting APKs that share the same filename.
- Keeps the existing ZIP-based workflow and plain Java fallback for simple projects.

## Stable signing (optional)

Create or use a dedicated Android **debug** keystore whose alias and passwords match the Android default debug credentials (`androiddebugkey`, password `android`). Base64-encode the keystore file and add the result as a GitHub Actions repository secret named:

`ANDROID_DEBUG_KEYSTORE_BASE64`

The workflow decodes the secret into the ephemeral runner's `~/.android/debug.keystore`. The key is not stored in this repository or the prepared ZIP. Keep a private backup of the keystore: losing it means future builds cannot update apps signed with it.

Without the secret, GitHub Actions uses its temporary runner debug key. Builds can succeed, but signatures are not stable between separate runner instances.

## Use

1. Open the GitHub Pages deployment (or open `index.html` locally in a modern browser).
2. Select the Android/Wear OS project ZIP.
3. Review the detected project type and build options.
4. Prepare and download the generated ZIP.
5. Push the ZIP to the repository. The workflow is included at `.github/workflows/build.yml`.
6. Download APKs from the Actions artifact or the generated GitHub Release.

## Current limitations / test status

- This branch is under development; do not treat it as a release yet.
- The generic no-Gradle fallback only supports very simple Java Android apps. Kotlin, AndroidX/third-party dependencies, Compose, native code, and complex resource setups require a Gradle project.
- The workflow builds the `debug` variant using `assembleDebug`; projects with custom-only variants may need configuration.
- GitHub Actions validation against representative real projects is still required.
