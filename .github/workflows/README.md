# Workflows

| Workflow | Trigger | What it does |
| --- | --- | --- |
| [`android-ci.yml`](android-ci.yml) | Push to `main`, pull request, manual | Builds the `github` flavor on JDK 17 with pinned NDK/CMake, restores the ICCOA SDK dex from a SHA-256-pinned public MiAuto release APK (without committing the dex), verifies that the debug APK contains the SDK, runs the JVM unit tests, and uploads the test APK and test report. |
| [`pages.yml`](pages.yml) | Push to `main` touching site files; manual | Regenerates `site/` and deploys it to GitHub Pages. |

## ICCOA SDK handling

The proprietary/prebuilt ICCOA SDK dex is intentionally absent from git. For the test build, the workflow downloads the upstream project's public `MiAuto-0.1.1.apk` release, verifies its published SHA-256, and extracts the SDK dex into the runner workspace. The dex is not committed to this repository. The debug APK is uploaded as the `miauto-iccoa-fixed-debug-apk` artifact for 14 days.

## Signing

The test APK is the `githubDebug` variant. It is signed with a temporary GitHub Actions debug key and uses application ID `com.miauto.app.debug`, so it can be installed alongside a separately installed `com.miauto.app` release. It is a test build, not a replacement for an APK signed with the project's release key.

The release variant remains an unsigned compile/R8 check because `key.properties` and the release keystore must not be committed to the repository.

## Notes

- CI requires network access to the pinned upstream public release asset. The APK's SHA-256 is checked before extraction.
- If GitHub Actions are disabled for this fork, enable them in the repository's **Actions** tab before expecting a workflow run.
- Only the `github` flavor is built here; the `playstore` flavor is not covered.
