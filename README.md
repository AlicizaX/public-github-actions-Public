# AlicizaX Public GitHub Actions

Reusable GitHub Actions workflows for AlicizaX Unity packages.

## publish-upm-release.yml

Creates a GitHub Release for a Unity UPM package when the package version does
not already have a matching tag.

Behavior:

- Reads `package.json` and uses `version` as both the Git tag and release title.
- Skips release creation when the version tag already exists.
- Generates a source archive from the current commit.
- Packs and signs a Unity UPM tarball with the Unity UPM CLI (`upm pack`) when
  `sign_upm_package` is enabled (default: `true`).
- Uploads both the source archive and the signed `.tgz` as release assets.
- Builds release notes from commits since the previous tag, or from recent
  commits when no previous tag exists.
- Deletes older GitHub Releases after publishing, keeping only the newest three
  releases by default.
- Deletes the Git tags that belong to deleted GitHub Releases.

### Caller workflow

```yaml
name: Publish Release

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  publish-release:
    uses: AlicizaX/public-github-actions-Public/.github/workflows/publish-upm-release.yml@main
    with:
      package_json_path: package.json
      # sign_upm_package: true   # default
    secrets: inherit
```

### Required secrets

Set these on the **caller** repository, or as organization secrets that are
visible to the caller repositories:

| Secret | Description |
|---|---|
| `UPM_ORG_ID` | Unity Organization ID |
| `UPM_SERVICE_ACCOUNT_KEY_ID` | Unity service account Key ID |
| `UPM_SERVICE_ACCOUNT_KEY_SECRET` | Unity service account Secret |

The service account needs the **Package Manager Package Signer** organization
role. Caller workflows must pass `secrets: inherit` (or pass the three secrets
explicitly).

Notes:

- Free GitHub plans may not inject organization secrets into **private**
  repositories. Prefer public package repos, or copy the three secrets into
  each private repo.
- Historical versions stay unsigned. Bump `package.json` version to publish a
  newly signed release.

### Repository settings

- Actions workflow permissions must allow `Read and write permissions`.
- Tags use the raw package version, for example `2.0.1`.
- `keep_releases` defaults to `3`. Set it to `0` only if cleanup should be
  disabled. Deleted releases have their Git tags deleted too.
- To publish without signing: `with: { sign_upm_package: false }`.
