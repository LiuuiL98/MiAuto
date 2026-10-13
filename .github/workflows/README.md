# Workflows

| Workflow | Trigger | What it does |
| --- | --- | --- |
| [`android-ci.yml`](android-ci.yml) | push to `main`, any pull request, manual | Compiles the `github` flavor (debug and release) on JDK 17 with pinned NDK/CMake, checks translation consistency, runs the JVM unit tests, and uploads the APKs and the test report as artifacts. |
| [`pages.yml`](pages.yml) | push to `main` touching `site/**`, `scripts/build_site.py` or itself; manual | Regenerates `site/` from `site/content.json` and deploys it to GitHub Pages. |

## Secrets

| Secret | Required | Purpose |
| --- | --- | --- |
| `ICCOA_SDK_DEX_BASE64` | No | The CarWith / CarLink SDK dex, base64-encoded. Not in git (see [`app/libs/README.md`](../../app/libs/README.md)). Without it the build still succeeds, but the uploaded APK cannot connect to CarWith and CI emits a warning. |

To create the value:

```sh
base64 -w0 app/libs/icar-sdk.dex   # macOS: base64 -i app/libs/icar-sdk.dex
```

Paste the output into *Settings → Secrets and variables → Actions → New repository secret*.
Pull requests opened from forks receive no secrets, so they exercise the no-SDK path.

## Notes

- CI does **not** sign release APKs. `key.properties` and the keystore are untracked by design,
  so the `githubRelease` artifact is an unsigned R8/shrinking check rather than a distributable.
  Release assets on the releases page are built and signed locally.
- Only the `github` flavor is built. The `playstore` flavor is not covered here.
