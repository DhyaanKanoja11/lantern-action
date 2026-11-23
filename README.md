# Lantern Security Scan Action

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Lantern%20Action-blue.svg?logo=github)](https://github.com/marketplace/actions/lantern-security-scan)
[![GitHub Action](https://img.shields.io/badge/action-lantern--action-blue?logo=github-actions)](https://github.com/DhyaanKanoja11/lantern-action)
[![Lantern](https://img.shields.io/badge/security-Lantern-emerald)](https://github.com/DhyaanKanoja11/Lantern)

An official GitHub Action for [Lantern](https://github.com/DhyaanKanoja11/Lantern) to audit running services and Docker containers in CI, blocking pull requests that introduce unintentional wildcard port bindings (`0.0.0.0` or `::`).

## Quick Start

Add Lantern to your GitHub Actions test or integration pipeline:

```yaml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Start Docker Services
        run: docker compose up -d

      - name: Run Integration Tests
        run: npm test # or pytest, go test, etc.

      - name: Audit Port Exposures
        uses: DhyaanKanoja11/lantern-action@v1
        with:
          fail-on-wildcard: 'true'
          ignore-ports: '80,443'
```

## Inputs

| Input | Description | Required | Default |
| :--- | :--- | :---: | :---: |
| `version` | Version of Lantern to install (e.g. `latest`, `v0.2.1`) | No | `latest` |
| `fail-on-wildcard` | Fail the step if any service binds to `0.0.0.0` or `::` | No | `true` |
| `ignore-ports` | Comma-separated list of ports to whitelist (e.g. `80,443`) | No | `""` |

## How It Works

1. Automatically installs the standalone, zero-dependency `lantern` binary onto the runner.
2. Inspects local Linux kernel socket tables (`/proc/net/tcp`, `ss`) and queries running Docker containers.
3. If an unwhitelisted service is exposed on `0.0.0.0` or `::`:
   - Emits native GitHub Actions error annotations directly on the PR view.
   - Exits with a non-zero code to fail the CI job before accidental exposures reach production.

## Documentation

For full documentation and local CLI usage, visit the [Lantern repository](https://github.com/DhyaanKanoja11/Lantern).
