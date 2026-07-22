<!-- markdownlint-disable -->

# Hardening Report: actions--download-artifact/v4.1.8

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **actions--download-artifact/v4.1.8** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tag/version refs instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the tag is moved.

check-dist.yml: actions/checkout@v4, actions/setup-node@v4, actions/upload-artifact@v4
codeql-analysis.yml: actions/checkout@v3, github/codeql-action/init@v2, github/codeql-action/autobuild@v2, github/codeql-action/analyze@v2
licensed.yml: actions/checkout@v4
release-new-action-version.yml: actions/publish-action@v0.3.0
test.yml: actions/checkout@v4, actions/setup-node@v4, actions/upload-artifact@v4 (×2)

Locations:

- `.github/workflows/check-dist.yml:21`
- `.github/workflows/check-dist.yml:24`
- `.github/workflows/check-dist.yml:47`
- `.github/workflows/codeql-analysis.yml:18`
- `.github/workflows/codeql-analysis.yml:22`
- `.github/workflows/codeql-analysis.yml:28`
- `.github/workflows/codeql-analysis.yml:34`
- `.github/workflows/licensed.yml:13`
- `.github/workflows/release-new-action-version.yml:18`
- `.github/workflows/test.yml:22`
- `.github/workflows/test.yml:26`
- `.github/workflows/test.yml:42`
- `.github/workflows/test.yml:47`

### missing-permissions (severity: medium)

Four workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, the GITHUB_TOKEN is granted its default (potentially broad) permissions. Each file should declare minimal required permissions.

Locations:

- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/licensed.yml:1`
- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Sub-rule (a): `${{ matrix.runs-on }}` is interpolated directly inside a `run:` shell (PowerShell) block in test.yml. Although `matrix.runs-on` is workflow-controlled and not directly attacker-supplied via a PR, any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted into the shell command string before the shell parses it. Offending lines:
  $fileA = "some/other/path/Artifact-A-${{ matrix.runs-on }}/file-A.txt"
  $fileB = "some/other/path/Artifact-B-${{ matrix.runs-on }}/file-B.txt"

Locations:

- `.github/workflows/test.yml:75`
- `.github/workflows/test.yml:76`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all findings across 5 workflow files:

**unpinned-uses** — Pinned all action references to full SHA commits:
- actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 (check-dist.yml, licensed.yml, test.yml)
- actions/checkout@v3 → @a37ce9120846195fa4ece8f58b268e6043cb2f26 (codeql-analysis.yml)
- actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020 (check-dist.yml, test.yml)
- actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02 (check-dist.yml, test.yml ×2)
- github/codeql-action/init@v2, autobuild@v2, analyze@v2 → @b8d3b6e8af63cde30bdc382c0bc28114f4346c88 (codeql-analysis.yml)
- actions/publish-action@v0.3.0 → @f784495ce78a41bac4ed7e34a73f0034015764bb (release-new-action-version.yml)

**missing-permissions** — Added top-level `permissions: contents: read` to check-dist.yml, licensed.yml, and test.yml. Added `permissions: contents: read` + `security-events: write` to codeql-analysis.yml (CodeQL requires security-events write to upload SARIF results). release-new-action-version.yml already had `permissions: contents: write`.

**script-injection** — In test.yml, moved `${{ matrix.runs-on }}` out of the PowerShell `run:` block into an `env:` block as `RUNS_ON`, then referenced it as `$env:RUNS_ON` in the PowerShell script. The `${{ matrix.runs-on }}` expressions in `with:` fields (name/path) and `runs-on:` are not shell injection risks and were left as-is.

