<!-- markdownlint-disable -->

# Hardening Report: actions--download-artifact/v4.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **actions--download-artifact/v4.3.0** was hardened automatically. 11 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references in check-dist.yml use mutable version tags instead of pinned 40-character commit SHAs, making the workflow vulnerable to supply-chain attacks. Unpinned references: actions/checkout@v4 (line 20), actions/setup-node@v4 (line 23), actions/upload-artifact@v4 (line 38).

Locations:

- `.github/workflows/check-dist.yml:20`
- `.github/workflows/check-dist.yml:23`
- `.github/workflows/check-dist.yml:38`

### unpinned-uses (severity: high)

All `uses:` references in codeql-analysis.yml use mutable version tags instead of pinned 40-character commit SHAs. Unpinned references: actions/checkout@v3 (line 20), github/codeql-action/init@v2 (line 24), github/codeql-action/autobuild@v2 (line 31), github/codeql-action/analyze@v2 (line 39).

Locations:

- `.github/workflows/codeql-analysis.yml:20`
- `.github/workflows/codeql-analysis.yml:24`
- `.github/workflows/codeql-analysis.yml:31`
- `.github/workflows/codeql-analysis.yml:39`

### unpinned-uses (severity: high)

The `uses:` reference in licensed.yml uses a mutable version tag instead of a pinned 40-character commit SHA. Unpinned reference: actions/checkout@v4 (line 15).

Locations:

- `.github/workflows/licensed.yml:15`

### unpinned-uses (severity: high)

All `uses:` references in publish-immutable-actions.yml use mutable version tags instead of pinned 40-character commit SHAs. Unpinned references: actions/checkout@v4 (line 13), actions/publish-immutable-action@0.0.3 (line 16).

Locations:

- `.github/workflows/publish-immutable-actions.yml:13`
- `.github/workflows/publish-immutable-actions.yml:16`

### unpinned-uses (severity: high)

The `uses:` reference in release-new-action-version.yml uses a mutable version tag instead of a pinned 40-character commit SHA. Unpinned reference: actions/publish-action@v0.3.0 (line 21).

Locations:

- `.github/workflows/release-new-action-version.yml:21`

### unpinned-uses (severity: high)

All `uses:` references in test.yml use mutable version tags instead of pinned 40-character commit SHAs. Unpinned references: actions/checkout@v4 (line 23), actions/setup-node@v4 (line 26), actions/upload-artifact@v4 (lines 54, 60).

Locations:

- `.github/workflows/test.yml:23`
- `.github/workflows/test.yml:26`
- `.github/workflows/test.yml:54`
- `.github/workflows/test.yml:60`

### missing-permissions (severity: medium)

check-dist.yml has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the default (potentially write) token permissions.

Locations:

- `.github/workflows/check-dist.yml:1`

### missing-permissions (severity: medium)

codeql-analysis.yml has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the default (potentially write) token permissions.

Locations:

- `.github/workflows/codeql-analysis.yml:1`

### missing-permissions (severity: medium)

licensed.yml has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the default (potentially write) token permissions.

Locations:

- `.github/workflows/licensed.yml:1`

### missing-permissions (severity: medium)

test.yml has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the default (potentially write) token permissions.

Locations:

- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Sub-rule (a): `${{ matrix.runs-on }}` is interpolated directly inside a `run:` shell command block. Any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value flows through YAML template substitution before the shell sees it. Offending lines: `$fileA = "some/other/path/Artifact-A-${{ matrix.runs-on }}/file-A.txt"` (line 102) and `$fileB = "some/other/path/Artifact-B-${{ matrix.runs-on }}/file-B.txt"` (line 103).

Locations:

- `.github/workflows/test.yml:102`
- `.github/workflows/test.yml:103`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all findings across 6 workflow files: (1) Pinned all uses: references to full commit SHAs with tag comments: actions/checkout@v4→34e114876b0b11c390a56381ad16ebd13914f8d5, actions/checkout@v3→f43a0e5ff2bd294095638e18286ca9a3d1956744, actions/setup-node@v4→49933ea5288caeca8642d1e84afbd3f7d6820020, actions/upload-artifact@v4→ea165f8d65b6e75b540449e92b4886f43607fa02, github/codeql-action/*@v2→b8d3b6e8af63cde30bdc382c0bc28114f4346c88, actions/publish-immutable-action@v0.0.3→4b1aa5c1cde5fedc80d52746c9546cb5560e5f53, actions/publish-action@v0.3.0→f784495ce78a41bac4ed7e34a73f0034015764bb. (2) Added top-level permissions blocks to check-dist.yml (contents: read), codeql-analysis.yml (contents: read, security-events: write), licensed.yml (contents: read), and test.yml (contents: read). publish-immutable-actions.yml and release-new-action-version.yml already had job-level permissions. (3) Fixed script-injection in test.yml lines 102-103: moved ${{ matrix.runs-on }} out of the PowerShell run: block into an env: block as RUNS_ON, then referenced it as $env:RUNS_ON in the PowerShell script.

