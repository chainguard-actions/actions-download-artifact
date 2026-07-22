<!-- markdownlint-disable -->

# Hardening Report: actions--download-artifact/v4.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **actions--download-artifact/v4.3.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tags (e.g. @v4, @v3, @v2, @v0.3.0, @0.0.3) instead of immutable 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the tag is moved to a malicious commit. Affected references:
- check-dist.yml: actions/checkout@v4 (line 20), actions/setup-node@v4 (line 23), actions/upload-artifact@v4 (line 43)
- codeql-analysis.yml: actions/checkout@v3 (line 23), github/codeql-action/init@v2 (line 27), github/codeql-action/autobuild@v2 (line 33), github/codeql-action/analyze@v2 (line 43)
- licensed.yml: actions/checkout@v4 (line 16)
- publish-immutable-actions.yml: actions/checkout@v4 (line 14), actions/publish-immutable-action@0.0.3 (line 17)
- release-new-action-version.yml: actions/publish-action@v0.3.0 (line 20)
- test.yml: actions/checkout@v4 (line 23), actions/setup-node@v4 (line 26), actions/upload-artifact@v4 (line 53), actions/upload-artifact@v4 (line 58)

Locations:

- `.github/workflows/check-dist.yml:20`
- `.github/workflows/check-dist.yml:23`
- `.github/workflows/check-dist.yml:43`
- `.github/workflows/codeql-analysis.yml:23`
- `.github/workflows/codeql-analysis.yml:27`
- `.github/workflows/codeql-analysis.yml:33`
- `.github/workflows/codeql-analysis.yml:43`
- `.github/workflows/licensed.yml:16`
- `.github/workflows/publish-immutable-actions.yml:14`
- `.github/workflows/publish-immutable-actions.yml:17`
- `.github/workflows/release-new-action-version.yml:20`
- `.github/workflows/test.yml:23`
- `.github/workflows/test.yml:26`
- `.github/workflows/test.yml:53`
- `.github/workflows/test.yml:58`

### missing-permissions (severity: medium)

Four workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the GITHUB_TOKEN is granted its default (often broad) permissions, violating the principle of least privilege.
- check-dist.yml: no permissions declared
- codeql-analysis.yml: no permissions declared
- licensed.yml: no permissions declared
- test.yml: no permissions declared

Locations:

- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/licensed.yml:1`
- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Sub-rule (a): Two `run:` blocks in test.yml directly interpolate `${{ matrix.runs-on }}` inside PowerShell script strings. Any `${{ ... }}` expression interpolated directly into a `run:` block is a script-injection risk because the value is substituted by the YAML template engine before the shell ever sees it, bypassing shell quoting. Offending lines:
- Line 97: `$fileA = "some/other/path/Artifact-A-${{ matrix.runs-on }}/file-A.txt"`
- Line 98: `$fileB = "some/other/path/Artifact-B-${{ matrix.runs-on }}/file-B.txt"`
These should be replaced with environment variable references (e.g. set `RUNS_ON: ${{ matrix.runs-on }}` in an `env:` block and use `$env:RUNS_ON` in the PowerShell script).

Locations:

- `.github/workflows/test.yml:97`
- `.github/workflows/test.yml:98`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

1. Pinned all 15 unpinned action references across 6 workflow files to full 40-char commit SHAs with original tag preserved as comment. For actions/publish-immutable-action, the tag '0.0.3' resolved as 'v0.0.3' (SHA: 4b1aa5c1cde5fedc80d52746c9546cb5560e5f53). 2. Added top-level permissions blocks to check-dist.yml (contents: read), codeql-analysis.yml (contents: read + security-events: write for CodeQL), licensed.yml (contents: read), and test.yml (contents: read). publish-immutable-actions.yml and release-new-action-version.yml already had permissions. 3. Fixed script-injection in test.yml by moving ${{ matrix.runs-on }} out of the PowerShell run: block into an env: block as RUNS_ON, then referencing it as $env:RUNS_ON in the PowerShell script.

