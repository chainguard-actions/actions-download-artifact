<!-- markdownlint-disable -->

# Hardening Report: actions--download-artifact/v8.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **actions--download-artifact/v8.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files reference actions using mutable tags instead of pinned full-length SHA commits, making them vulnerable to supply-chain attacks. Failing references include: actions/checkout@v4, actions/setup-node@v4, actions/upload-artifact@v4, actions/upload-artifact@v7, github/codeql-action/init@v2, github/codeql-action/autobuild@v2, github/codeql-action/analyze@v2, actions/publish-immutable-action@0.0.3, actions/publish-action@v0.3.0.

Locations:

- `.github/workflows/check-dist.yml:20`
- `.github/workflows/check-dist.yml:23`
- `.github/workflows/check-dist.yml:44`
- `.github/workflows/codeql-analysis.yml:18`
- `.github/workflows/codeql-analysis.yml:22`
- `.github/workflows/codeql-analysis.yml:28`
- `.github/workflows/codeql-analysis.yml:36`
- `.github/workflows/licensed.yml:13`
- `.github/workflows/publish-immutable-actions.yml:14`
- `.github/workflows/publish-immutable-actions.yml:17`
- `.github/workflows/release-new-action-version.yml:21`
- `.github/workflows/test.yml:18`
- `.github/workflows/test.yml:21`
- `.github/workflows/test.yml:40`
- `.github/workflows/test.yml:44`
- `.github/workflows/test.yml:119`
- `.github/workflows/test.yml:155`
- `.github/workflows/test.yml:160`
- `.github/workflows/test.yml:165`
- `.github/workflows/test.yml:170`
- `.github/workflows/test.yml:175`
- `.github/workflows/test.yml:180`

### missing-permissions (severity: medium)

These workflow files have no top-level 'permissions:' key and no job-level 'permissions:' key on any job, meaning they run with the default (potentially broad) GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/licensed.yml:1`
- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Sub-rule (a): '${{ matrix.runs-on }}' is interpolated directly inside run: shell command strings in multiple steps. Although 'matrix.*' values are typically controlled by the workflow author, any ${{ ... }} expression inside a run: block is a script-injection risk because the value is substituted into the shell command string before the shell parses it. Affected run: blocks include PowerShell steps constructing file paths (e.g. '$fileA = "some/other/path/Artifact-A-${{ matrix.runs-on }}/file-A.txt"') and bash steps (e.g. 'expected="ext-test/raw/report.txt-${{ matrix.runs-on }}.json.zip"' and echo commands writing to CJK artifact paths). These should be passed via env: variables and referenced as quoted shell variables instead.

Locations:

- `.github/workflows/test.yml:57`
- `.github/workflows/test.yml:72`
- `.github/workflows/test.yml:86`
- `.github/workflows/test.yml:100`
- `.github/workflows/test.yml:113`
- `.github/workflows/test.yml:128`
- `.github/workflows/test.yml:143`
- `.github/workflows/test.yml:148`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings across 5 workflow files:

1. unpinned-uses: Pinned all 9 action references to full commit SHAs with tag comments preserved. Note: actions/publish-immutable-action@0.0.3 was resolved as v0.0.3 (SHA: 4b1aa5c1cde5fedc80d52746c9546cb5560e5f53).

2. missing-permissions: Added top-level 'permissions: contents: read' to check-dist.yml, licensed.yml, and test.yml. Added 'permissions: contents: read / security-events: write' to codeql-analysis.yml (security-events: write is required for CodeQL to upload SARIF results).

3. script-injection: Moved all ${{ matrix.runs-on }} expressions from run: shell script bodies into env: blocks as RUNS_ON variable. Updated PowerShell steps to use $env:RUNS_ON and bash steps to use ${RUNS_ON}. Expressions in with: (action inputs) and env: blocks are not injection risks and were left as-is.

