<p align="center">
  <a href="https://github.com/nickatnight/releases-action"><img alt="GitHub Actions status" src="https://github.com/nickatnight/releases-action/workflows/release-it/badge.svg"></a>
  <a href="https://github.com/nickatnight/releases-action/releases"><img alt="Release Status" src="https://img.shields.io/github/v/release/nickatnight/releases-action"></a>
</p>


# releases-action
A GitHub action to create a release from branch when pushing a new tag.

Other notable packages that did not satisfy my use case:
* https://github.com/softprops/action-gh-release (can not release from branch)
* https://github.com/release-it/release-it (npm package rather than an action, but still could not release from branch)
* https://github.com/aaiezza/create-release (too bloated with features/libraries I don't need)

## Usage
```yaml
name: Tag it

on: push

jobs:
  create-release:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v2
        with:
          fetch-depth: 0  # need this for all history for all branches and tags
      - name: Create Release
        id: create_release
        uses: nickatnight/releases-action@master
        if: startsWith(github.ref, 'refs/tags/')
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

    outputs:
      ReleaseTag: ${{ steps.create_release.outputs.release_tag }}

  # pass in as env variable
  explore-essos:
    runs-on: ubuntu-latest
    needs: [create-release]
    env:
      RELEASE_TAG: ${{ needs.create-release.outputs.ReleaseTag }}
    steps:
      ...

```

## Supported Parameters

| Parameter               | Description                                                | Default       |
| ----------------------- | ---------------------------------------------------------- | ------------- |
| `branch`                | Which branch do we want to release from?                   |    `master`   |
| `name`                  | Name of the release                                        | `name of tag` |
| `message`               | Body of the release                                        |  `changelog`  |
| `draft`                 | Is this a draft?                                           |    `false`    |
| `prerelease`            | Is this a prerelease?                                      |    `false`    |
| `tag`                   | Name of tag                                                |  `GITHUB_REF` |
