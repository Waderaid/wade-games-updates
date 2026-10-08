# wade-games-updates

Update manifest for **Wade's Games** (`com.wade.games`), the 2D launcher for
Wade's Quest 3 games and apps.

## Single source of truth

`manifest.json` is the **single source of truth** for every future release of
every app in the launcher. The launcher's "Check for updates" button fetches

    https://raw.githubusercontent.com/Waderaid/wade-games-updates/main/manifest.json

compares each installed app's `versionCode` against the manifest, and offers
one-tap download + install for anything newer — including the launcher itself
(the `com.wade.games` entry).

## Releasing a new version

1. Build, sign (same keystore as before), and upload the APK.
2. Compute its SHA-256 (`sha256sum app.apk`).
3. Edit `manifest.json`: bump `version`, `versionCode`, `url`, `sha256`
   for the app's entry.
4. Commit and push. Verify the raw URL serves the new JSON:
   `curl https://raw.githubusercontent.com/Waderaid/wade-games-updates/main/manifest.json`
5. The launcher's "Check for updates" will offer the new version on next run.

## Schema

```json
{
  "apps": [
    {
      "package": "com.example.app",
      "name": "Example App",
      "version": "1.2.3",
      "versionCode": 7,
      "url": "https://files.catbox.moe/xxxxxx.apk",
      "sha256": "<64 hex chars, may be empty>"
    }
  ]
}
```

- `versionCode` is the comparison key: the launcher updates an app only when
  the manifest's `versionCode` is **greater** than the installed one.
- `sha256` may be omitted/empty; when present the launcher verifies the
  download before offering install.
- Malformed entries are skipped; a malformed document is reported as an
  error, never applied.

## Keystores

Each app keeps its own signing key. **Wade's Games** is signed with a new key
stored at `~/workspace/wades-games/keystore/wadesgames.keystore` — all future
`com.wade.games` builds MUST reuse it or users will have to uninstall first.
