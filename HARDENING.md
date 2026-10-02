<!-- markdownlint-disable -->

# Hardening Report: anandg112--trivy-action/v0.0.10

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **anandg112--trivy-action/v0.0.10** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow.yml file (a supporting workflow bundled with the action) contains three `uses:` references pinned to mutable tags or branch names rather than immutable 40-character commit SHAs. This exposes the action to supply-chain attacks if any of those upstream actions are compromised or their tags are moved:
- Line 13: `uses: actions/checkout@v2` (tag ref)
- Line 20: `uses: aquasecurity/trivy-action@master` (branch ref)
- Line 31: `uses: github/codeql-action/upload-sarif@v1` (tag ref)
Each should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v2`.

Locations:

- `workflow.yml:13`
- `workflow.yml:20`
- `workflow.yml:31`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three mutable `uses:` references in hardened/action/workflow.yml to full commit SHAs:
- `actions/checkout@v2` → `actions/checkout@0717577d45739eb3c851188b29f50ed6c0b2194e # v2`
- `aquasecurity/trivy-action@master` → `aquasecurity/trivy-action@c03d123cb480c08c69b054af418ea5e69fe6e57e # master`
- `github/codeql-action/upload-sarif@v1` → `github/codeql-action/upload-sarif@231aa2c8a89117b126725a0e11897209b7118144 # v1`
Original tag/branch references preserved as inline comments for readability.

### Iteration 2

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed script-injection by moving `${{ github.sha }}` out of the `run:` shell string into an `env:` block as `GITHUB_SHA`, then referencing it as `${GITHUB_SHA}` in the shell command. Added a top-level `permissions:` block with `contents: read` (for checkout) and `security-events: write` (for uploading SARIF results to the GitHub Security tab). The `${{ github.sha }}` in the Trivy action's `with:` block was left as-is since `with:` inputs are not shell-interpolated and do not constitute a script-injection risk.

