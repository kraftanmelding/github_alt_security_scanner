# Security Scanner

Reusable GitHub Actions workflows for security scanning. Covers secrets, Ruby/Rails, JavaScript/Vue, and Python — no paid GitHub features required.

## What it runs

| Scanner | Tool | Catches |
|---------|------|---------|
| Secrets | [Gitleaks](https://github.com/gitleaks/gitleaks) | Hardcoded API keys, tokens, passwords in git history |
| Ruby/Rails | [Brakeman](https://brakemanscanner.org) | SQL injection, XSS, unsafe redirects, mass assignment |
| JS/Vue | [ESLint security plugin](https://github.com/eslint-community/eslint-plugin-security) | `eval()`, unsafe regex, injection risks |
| Python | [Bandit](https://bandit.readthedocs.io) | SQL injection, shell injection, weak crypto, hardcoded passwords |

Each scanner is language-aware — if a repo has no Python files, the Bandit job skips itself automatically.

## How findings are reported

- A pdf report is sent to Discord channel

Scans never fail a PR or block a merge.

## Setup

### 1. Add this repo to your GitHub organisation

Push this repo as `YOUR_ORG/security-scanner` (or whatever name you choose).

### 2. Allow reusable workflows

In your GitHub organisation settings:
`Settings → Actions → General → Allow GitHub Actions to create and approve pull requests` — enable this so the report job can open issues in other repos.

Also ensure the organisation allows workflows to be called across repos:
`Settings → Actions → General → Access → Accessible from repositories in the 'YOUR_ORG' organization`.

### 3. Add the caller workflow to each repo you want scanned

Copy `example/security-scan.yml` to `.github/workflows/security-scan.yml` in the target repo, then replace the placeholder:

```yaml
jobs:
  security:
    uses: YOUR_ORG/security-scanner/.github/workflows/full-scan.yml@main
    #     ^^^^^^^^ replace with your actual org name
```

That's it. The scan runs at 02:00 UTC every night and you can also trigger it manually from the Actions tab.

## Running a single scanner

If a repo only needs one scanner, call it directly instead of `full-scan.yml`:

```yaml
jobs:
  secrets:
    uses: YOUR_ORG/security-scanner/.github/workflows/scan-secrets.yml@main
    secrets: inherit

  python:
    uses: YOUR_ORG/security-scanner/.github/workflows/scan-python.yml@main
    secrets: inherit
```

Available individual workflows:
- `scan-secrets.yml` — Gitleaks
- `scan-ruby.yml` — Brakeman
- `scan-javascript.yml` — ESLint security
- `scan-python.yml` — Bandit

## Pinning to a specific version

Replace `@main` with a tag or commit SHA for reproducible builds:

```yaml
uses: YOUR_ORG/security-scanner/.github/workflows/full-scan.yml@v1.0.0
```
