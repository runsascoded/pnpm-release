# pnpm-release

GitHub Action for publishing npm packages with pnpm and creating GitHub releases.

## Quick Start

### Option 1: Reusable Workflow (recommended)

```yaml
# .github/workflows/release.yml
name: Release
on:
  push:
    tags: ['v*']
jobs:
  release:
    uses: runsascoded/pnpm-release/.github/workflows/release.yml@v1
    secrets:
      npm_token: ${{ secrets.NPM_TOKEN }}
```

### Option 2: Composite Action

```yaml
# .github/workflows/release.yml
name: Release
on:
  push:
    tags: ['v*']
jobs:
  release:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      id-token: write
    steps:
      - uses: runsascoded/pnpm-release@v1
        with:
          npm_token: ${{ secrets.NPM_TOKEN }}
```

## How It Works

1. Checks out your code at the tag
2. Sets up pnpm and Node.js, installs dependencies
3. Runs your build command (default: `pnpm run build`)
4. Publishes to npm with `pnpm publish`
5. Creates a GitHub release with auto-generated release notes
6. Outputs links to NPM and GitHub releases (in logs and as workflow annotations)

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `npm_token` | NPM authentication token | (required) |
| `node_version` | Node.js version | `'20'` |
| `pnpm_version` | pnpm version | `'10'` |
| `build_command` | Build command to run | `'pnpm run build'` |
| `publish_args` | Additional args for `pnpm publish` | `'--access public --no-git-checks'` |

## Usage

Just push a tag:

```bash
git tag v1.0.0
git push origin v1.0.0
```

The action handles building, publishing to npm, and creating the GitHub release with auto-generated notes.

## Used By

- [use-url-params] ([workflow][use-url-params-workflow])
- [use-hotkeys] ([workflow][use-hotkeys-workflow])
- [og-lambda] ([workflow][og-lambda-workflow])

## See Also

- [pnpm-dist] - Sibling action for building and maintaining npm package distribution branches

[use-url-params]: https://github.com/runsascoded/use-url-params
[use-url-params-workflow]: https://github.com/runsascoded/use-url-params/blob/main/.github/workflows/release.yml
[use-hotkeys]: https://github.com/runsascoded/use-hotkeys
[use-hotkeys-workflow]: https://github.com/runsascoded/use-hotkeys/blob/main/.github/workflows/release.yml
[og-lambda]: https://github.com/runsascoded/og-lambda
[og-lambda-workflow]: https://github.com/runsascoded/og-lambda/blob/main/.github/workflows/release.yml
[pnpm-dist]: https://github.com/runsascoded/pnpm-dist

## License

MIT
