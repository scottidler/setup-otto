# setup-otto

GitHub Action to install [otto](https://github.com/scottidler/otto) — a make-like task runner with YAML configuration.

## Usage

### Basic

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: scottidler/setup-otto@v1
  - run: otto --help
```

### With specific version

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: scottidler/setup-otto@v1
    with:
      version: 'v0.1.3'
  - run: otto ci
```

### Multi-platform CI

```yaml
jobs:
  test:
    strategy:
      matrix:
        os: [ubuntu-latest, macos-14, macos-13]
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v4
      - uses: scottidler/setup-otto@v1
      - run: otto ci
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `version` | Version of otto to install (`latest` or `vX.Y.Z`) | `latest` |
| `github-token` | GitHub token for API requests (avoids rate limits) | `${{ github.token }}` |

## Outputs

| Output | Description |
|--------|-------------|
| `version` | The installed version of otto |
| `cache-hit` | Whether the binary was restored from cache (`true`/`false`) |

## Supported Platforms

| OS | Architecture | Runner |
|----|--------------|--------|
| Linux | x86_64 | `ubuntu-latest` |
| macOS | x86_64 | `macos-13` |
| macOS | ARM64 | `macos-14` |

## Example: Full CI Pipeline

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup otto
        uses: scottidler/setup-otto@v1

      - name: Run CI tasks
        run: otto ci

      - name: Show otto version
        run: |
          echo "Otto version: ${{ steps.setup-otto.outputs.version }}"
          echo "Cache hit: ${{ steps.setup-otto.outputs.cache-hit }}"
```

## Caching

This action automatically caches the otto binary using `actions/cache`. The cache key is based on:
- Otto version
- Runner OS and architecture

Subsequent runs with the same version will restore from cache, significantly speeding up workflows.

## License

MIT

