# brickhouse-tech/.github

Shared workflows and org-level config for brickhouse-tech.

## Reusable Workflows

### tests.yml
Node.js test matrix. Runs install, lint, test.

```yaml
jobs:
  tests:
    uses: brickhouse-tech/.github/.github/workflows/tests.yml@main
    # Optional overrides:
    # with:
    #   node-versions: '["20.x", "22.x"]'
    #   lint: false
```

### release.yml
Conventional commits → commit-and-tag-version → push tags. Requires `GH_TOKEN` secret.

```yaml
jobs:
  release:
    needs: [tests]
    uses: brickhouse-tech/.github/.github/workflows/release.yml@main
    secrets:
      GH_TOKEN: ${{ secrets.GH_TOKEN }}
```

### publish.yml
OIDC npm publish on `v*` tag. No NPM_TOKEN needed.

```yaml
jobs:
  publish:
    needs: [tests]
    uses: brickhouse-tech/.github/.github/workflows/publish.yml@main
```

## Example: Thin repo workflows

**`.github/workflows/tests.yml`**
```yaml
name: tests
on:
  workflow_call:
  push:
    branches: ["master"]
  pull_request:
    branches: ["master"]
jobs:
  tests:
    uses: brickhouse-tech/.github/.github/workflows/tests.yml@main
```

**`.github/workflows/release.yml`**
```yaml
name: release
on:
  push:
    branches: ["master"]
    tags-ignore: ['**']
jobs:
  tests:
    uses: brickhouse-tech/.github/.github/workflows/tests.yml@main
  release:
    needs: [tests]
    uses: brickhouse-tech/.github/.github/workflows/release.yml@main
    secrets:
      GH_TOKEN: ${{ secrets.GH_TOKEN }}
```

**`.github/workflows/publish.yml`**
```yaml
name: publish
on:
  push:
    tags:
      - "v*"
jobs:
  tests:
    uses: brickhouse-tech/.github/.github/workflows/tests.yml@main
  publish:
    needs: [tests]
    uses: brickhouse-tech/.github/.github/workflows/publish.yml@main
```
