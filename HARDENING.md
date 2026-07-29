<!-- markdownlint-disable -->

# Hardening Report: actions--download-artifact/v3.1.0-node20

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **actions--download-artifact/v3.1.0-node20** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable tag/version references instead of pinned full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced tag is moved or overwritten.

- check-dist.yml: `actions/checkout@v3`, `actions/setup-node@v3`, `actions/upload-artifact@v3`
- codeql-analysis.yml: `actions/checkout@v3`, `github/codeql-action/init@v2`, `github/codeql-action/autobuild@v2`, `github/codeql-action/analyze@v2`
- licensed.yml: `actions/checkout@v3`
- release-new-action-version.yml: `actions/publish-action@v0.2.1`
- test.yml: `actions/checkout@v3`, `actions/setup-node@v3`, `actions/upload-artifact@v1`

Locations:

- `.github/workflows/check-dist.yml:22`
- `.github/workflows/check-dist.yml:25`
- `.github/workflows/check-dist.yml:44`
- `.github/workflows/codeql-analysis.yml:20`
- `.github/workflows/codeql-analysis.yml:24`
- `.github/workflows/codeql-analysis.yml:29`
- `.github/workflows/codeql-analysis.yml:40`
- `.github/workflows/licensed.yml:14`
- `.github/workflows/release-new-action-version.yml:20`
- `.github/workflows/test.yml:22`
- `.github/workflows/test.yml:26`
- `.github/workflows/test.yml:47`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` key and no job-level `permissions:` keys on any job. Without explicit permissions, the GITHUB_TOKEN is granted its default (potentially broad) permissions, which violates the principle of least privilege.

- check-dist.yml: no permissions defined
- codeql-analysis.yml: no permissions defined
- licensed.yml: no permissions defined
- test.yml: no permissions defined

Locations:

- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/licensed.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all 5 workflow files:

**unpinned-uses fixes:**
- check-dist.yml: Pinned actions/checkout@v3 → @a37ce9120846195fa4ece8f58b268e6043cb2f26, actions/setup-node@v3 → @3235b876344d2a9aa001b8d1453c930bba69e610, actions/upload-artifact@v3 → @ff15f0306b3f739f7b6fd43fb5d26cd321bd4de5
- codeql-analysis.yml: Pinned actions/checkout@v3 → @a37ce9120846195fa4ece8f58b268e6043cb2f26, github/codeql-action/init@v2, autobuild@v2, analyze@v2 → @b8d3b6e8af63cde30bdc382c0bc28114f4346c88
- licensed.yml: Pinned actions/checkout@v3 → @a37ce9120846195fa4ece8f58b268e6043cb2f26
- release-new-action-version.yml: Pinned actions/publish-action@v0.2.1 → @c79b33d62c07b589fb3da4b599ba694977974d10
- test.yml: Pinned actions/checkout@v3 → @a37ce9120846195fa4ece8f58b268e6043cb2f26, actions/setup-node@v3 → @3235b876344d2a9aa001b8d1453c930bba69e610, actions/upload-artifact@v1 → @3446296876d12d4e3a0f3145a3c87e67bf0a16b5

**missing-permissions fixes:**
- check-dist.yml: Added `permissions: contents: read`
- codeql-analysis.yml: Added `permissions: contents: read, security-events: write` (security-events: write is required for CodeQL to upload SARIF results)
- licensed.yml: Added `permissions: contents: read`
- test.yml: Added `permissions: contents: read`

All original tag names preserved as inline comments for readability.

