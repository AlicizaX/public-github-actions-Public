# AlicizaX Public GitHub Actions

Reusable GitHub Actions workflows for AlicizaX Unity packages.

## publish-upm-release.yml

Creates a GitHub Release for a Unity UPM package when the package version does
not already have a matching tag.

Behavior:

- Reads `package.json` and uses `version` as both the Git tag and release title.
- Skips release creation when the version tag already exists.
- Generates a source archive from the current commit.
- Builds release notes from commits since the previous tag, or from recent
  commits when no previous tag exists.

Caller workflow:

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
    secrets: inherit
```

Repository settings:

- Actions workflow permissions must allow `Read and write permissions`.
- Tags use the raw package version, for example `2.0.1`.
