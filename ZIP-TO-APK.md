# ZIP → APK Builder

Mobile-first Android ZIP uploader and GitHub Actions build runner.

## Privacy model

- The builder page and workflow template are public source code in this repository.
- The Android project ZIP is sent directly from the browser to the user's selected **private** GitHub repository.
- The page refuses to upload unless GitHub confirms that the target repository is private and the token has push access.
- The fine-grained token is kept in page memory only; it is not written to localStorage, cookies, or this repository.
- The ZIP is removed from the private repository after a successful build. If a build fails, the ZIP is retained for diagnosis/retry.
- Build logs, diagnostics, APK artifacts, and releases remain private when the target repository is private.

## Open on Android

Open the GitHub Pages page:

https://ohmika.github.io/build/zip-to-apk.html

The root page also links to it.

## One-time GitHub setup

1. Create a dedicated **private** repository for APK builds (or choose an existing private repository that you are comfortable using for this purpose).
2. Create a fine-grained personal access token, restricted to that one repository.
3. Grant:
   - Contents: Read and write
   - Workflows: Read and write
   - Actions: Read
   - Metadata: Read (mandatory repository metadata permission)
4. Paste the repository as `owner/repo` and the token into the builder.
5. Choose a ZIP and tap **Uploaden en APK bouwen**.

The page installs `.github/workflows/zip-to-apk-builder.yml` automatically, then uploads the ZIP to `incoming/`. A push event starts GitHub Actions.

## Current build behavior

- Automatically detects an existing Gradle Android project.
- Uses an existing Gradle wrapper when present; otherwise installs Gradle 8.9.
- Installs Android SDK platform/build tools and uses JDK 17.
- For a simple Android source ZIP without Gradle files, it attempts to generate a minimal Android Gradle application configuration for Java/Kotlin sources.
- Runs `assembleDebug` and collects APK files from Gradle output folders.
- Publishes APKs as a private GitHub Release and as a seven-day Actions artifact.
- Removes the uploaded source ZIP only after a successful build.

## Limits and caveats

- ZIP upload limit in the mobile page: 35 MB.
- The default output is a **debug APK**, not a signed production/release APK.
- Auto-generated Gradle configuration is a best-effort fallback. Projects using AndroidX, Compose, native code, custom build plugins, proprietary dependencies, or unusual layouts may need their original Gradle configuration or manual repair.
- Legacy projects requiring a JDK other than 17 or an old Gradle/Android Gradle Plugin combination may fail and need a compatibility adjustment.
- A build failure keeps the ZIP in the private repository and provides the full GitHub Actions run logs plus diagnostic artifacts.
- A GitHub fine-grained token is powerful within the selected repository. Keep it private and revoke it in GitHub settings when no longer needed.
- Build scripts from the uploaded project execute on a temporary GitHub-hosted runner. The workflow does not expose its release token to the Gradle build step; checkout credentials are not persisted.

This is an initial implementation. Test it with a small, non-sensitive Android project before relying on it for important builds.
