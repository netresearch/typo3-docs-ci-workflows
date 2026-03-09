# Reusable GitHub Actions Workflows for TYPO3-Documentation

Centralized, reusable CI workflows for repositories in the
[TYPO3-Documentation](https://github.com/TYPO3-Documentation) organization.

## Why

The TYPO3-Documentation org enforces a GitHub Actions **allow-list** with
SHA-pinned actions. This creates two maintenance challenges:

1. **Composite actions break the allow-list.** Actions like
   `ramsey/composer-install` internally call `actions/cache@v4`, which the
   caller's allow-list must also approve. When the inner action updates its
   SHA, all callers break silently.

2. **~60 workflow files across ~29 repos** must each be updated when action
   SHAs change, backport tooling breaks, or CI patterns evolve.

Reusable workflows solve both problems: they execute in their **own**
context (this repository), so only *this* repo's action references need to
stay current. Callers reference a single workflow by tag/SHA and inherit
all updates automatically.

## Available Workflows

| Workflow | Purpose |
|----------|---------|
| [`php-tests.yml`](.github/workflows/php-tests.yml) | PHP test matrix (unit + integration) |
| [`php-quality.yml`](.github/workflows/php-quality.yml) | Code quality: CS Fixer, PHPStan, XML lint |
| [`backport.yml`](.github/workflows/backport.yml) | Backport merged PRs via `korthout/backport-action` |
| [`docs-render.yml`](.github/workflows/docs-render.yml) | Documentation rendering check |

## Usage

Call a workflow from your repository's workflow file:

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:

jobs:
  tests:
    uses: CybotTM/typo3-docs-ci-workflows/.github/workflows/php-tests.yml@main
    with:
      php-versions: '["8.2", "8.3", "8.4"]'
      test-unit-command: 'make test-unit'

  quality:
    uses: CybotTM/typo3-docs-ci-workflows/.github/workflows/php-quality.yml@main
    with:
      php-version: '8.2'

  backport:
    uses: CybotTM/typo3-docs-ci-workflows/.github/workflows/backport.yml@main

  docs:
    uses: CybotTM/typo3-docs-ci-workflows/.github/workflows/docs-render.yml@main
```

### Inputs

Each workflow accepts optional inputs with sensible defaults.
See the individual workflow files for the full list of inputs.

## Action SHA Pins

All actions are SHA-pinned to verified commits. Current pins:

| Action | Version | SHA |
|--------|---------|-----|
| `actions/checkout` | v6.0.2 | `de0fac2e4500dabe0009e67214ff5f5447ce83dd` |
| `actions/cache` | v5.0.3 | `cdf6c1fa76f9f475f3d7449005a359c84ca0f306` |
| `actions/setup-python` | v6.2.0 | `a309ff8b426b58ec0e2a45f0f869d46889d02405` |
| `shivammathur/setup-php` | 2.36.0 | `44454db4f0199b8b9685a5d763dc37cbf79108e1` |
| `korthout/backport-action` | v4.2.0 | `4aaf0e03a94ff0a619c9a511b61aeb42adea5b02` |

## Status

Proposed for adoption by the TYPO3-Documentation organization.
Currently maintained under [CybotTM](https://github.com/CybotTM) for
evaluation and testing.

## License

GPL-3.0-or-later — same as the TYPO3 documentation toolchain.
