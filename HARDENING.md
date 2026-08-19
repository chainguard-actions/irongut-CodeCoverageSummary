<!-- markdownlint-disable -->

# Hardening Report: irongut--CodeCoverageSummary/v1.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **irongut--CodeCoverageSummary/v1.3.0** was hardened automatically. 6 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a Docker image referenced by a mutable tag (`docker://ghcr.io/irongut/codecoveragesummary:v1.3.0`) instead of an immutable SHA digest. This is vulnerable to supply-chain attacks if the tag is moved to a different image.

Locations:

- `action.yml:44`

### unpinned-uses (severity: high)

test-linux.yml references actions by mutable tag/branch instead of a full 40-character commit SHA: `actions/checkout@v2` (line 15) and `irongut/CodeCoverageSummary@master` (line 17). These are vulnerable to supply-chain attacks.

Locations:

- `.github/workflows/test-linux.yml:15`
- `.github/workflows/test-linux.yml:17`

### unpinned-uses (severity: high)

test-macos.yml references `irongut/CodeCoverageSummary@master` — a mutable branch ref instead of a full 40-character commit SHA. This is vulnerable to supply-chain attacks.

Locations:

- `.github/workflows/test-macos.yml:13`

### unpinned-uses (severity: high)

test-windows.yml references `irongut/CodeCoverageSummary@master` — a mutable branch ref instead of a full 40-character commit SHA. This is vulnerable to supply-chain attacks.

Locations:

- `.github/workflows/test-windows.yml:13`

### missing-permissions (severity: medium)

auto-assign-pr.yml has no top-level `permissions:` key and the only job (`assignAuthor`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad.

Locations:

- `.github/workflows/auto-assign-pr.yml:1`

### script-injection (severity: high)

release-build.yml contains a `run:` block that directly interpolates `${{ steps.meta.outputs.tags }}` and `${{ steps.build-and-push.outputs.digest }}` (both `steps.*.outputs.*` context values) into a shell command string. These values flow through YAML template substitution before the shell parses them, enabling script injection. Offending line: `run: echo "${{ steps.meta.outputs.tags }}" | xargs -I {} cosign sign {}@${{ steps.build-and-push.outputs.digest }}`

Locations:

- `.github/workflows/release-build.yml:79`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 6 findings:
1. action.yml (line 44): Pinned docker://ghcr.io/irongut/codecoveragesummary:v1.3.0 to immutable digest sha256:daebdede906ca84788b94378a1504a88d38621198d3836df24d60d6215e64a86
2. test-linux.yml (lines 15, 17): Pinned actions/checkout@v2 to SHA ee0669bd1cc54295c223e0bb666b733df41de1c5 and irongut/CodeCoverageSummary@master to SHA f13848964841ca2f7a63343b287493e17dc645de
3. test-macos.yml (line 13): Pinned irongut/CodeCoverageSummary@master to SHA f13848964841ca2f7a63343b287493e17dc645de
4. test-windows.yml (line 13): Pinned irongut/CodeCoverageSummary@master to SHA f13848964841ca2f7a63343b287493e17dc645de
5. auto-assign-pr.yml (line 1): Added top-level permissions block with pull-requests: write (needed for PR assignment)
6. release-build.yml (line 79): Fixed script injection by moving ${{ steps.meta.outputs.tags }} and ${{ steps.build-and-push.outputs.digest }} into env block as TAGS and DIGEST variables, referenced as plain shell variables in the run command

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed the unquoted $DIGEST variable in the 'Sign the Docker image' step of .github/workflows/release-build.yml. Changed `cosign sign {}@$DIGEST` to `cosign sign "{}@$DIGEST"` so that the workflow-controllable DIGEST value (sourced from steps.build-and-push.outputs.digest) is properly quoted, preventing shell metacharacter injection.

