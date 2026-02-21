# Monorepo support

## Problem

The action assumes a single-package repo: it runs `pnpm publish` at the repo root and reads `package.json` from root for name/version. This fails for monorepos where:

- Root `package.json` is `private: true`
- The publishable package is in a subdirectory (e.g. `client/`)
- `pnpm publish` at root errors or is a no-op

## Proposed fix

Add a `pkg` input (package path within the monorepo):

```yaml
pkg:
  description: 'Package subdirectory to publish (monorepo mode). If set, build/publish/info steps target this directory.'
  required: false
  default: ''
```

When `pkg` is set:

1. **Build**: default `build_command` becomes `pnpm run build --filter ./<pkg>` (or user can override)
2. **Get package info**: read `<pkg>/package.json` instead of root `package.json`
3. **Publish**: run `pnpm publish $publish_args` from `<pkg>/` directory (or `pnpm --filter ./<pkg> publish $publish_args`)

When `pkg` is empty, behavior is unchanged (current single-package mode).

### Affected steps

```yaml
- name: Get package info
  run: |
    PKG_DIR="${{ inputs.pkg || '.' }}"
    PKG_NAME=$(jq -r .name "$PKG_DIR/package.json")
    PKG_VERSION=$(jq -r .version "$PKG_DIR/package.json")

- name: Publish to npm
  working-directory: ${{ inputs.pkg || '.' }}
  run: pnpm publish ${{ inputs.publish_args }}
```

### Reusable workflow

Also expose `pkg` in `.github/workflows/release.yml`:

```yaml
pkg:
  description: 'Package subdirectory to publish (monorepo mode)'
  type: string
  default: ''
```

## Context

Came up while setting up https://github.com/runsascoded/aws-static-sso — a pnpm monorepo with `worker/` (CF Worker, private) and `client/` (published to npm as `aws-static-sso`). Only `client` needs NPM publishing, but the current action can't target it.
