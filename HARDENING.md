<!-- markdownlint-disable -->

# Hardening Report: irongut--CodeCoverageSummary/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **irongut--CodeCoverageSummary/v1.1.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files and action.yml reference actions and Docker images by mutable tags or branch names instead of immutable 40-character SHA commit digests, making them vulnerable to supply-chain attacks.

• action.yml: `image: docker://ghcr.io/irongut/codecoveragesummary:v1.1.0` — Docker image pinned by tag, not SHA digest.
• auto-assign-pr.yml: `samspills/assign-pr-to-author@v1.0`
• ci-build.yml: `actions/checkout@v2`, `actions/setup-dotnet@v1`
• mark-stale.yml: `actions/stale@v3`
• release-build.yml: `actions/checkout@v2`, `actions/setup-dotnet@v1`, `nblagoev/pull-release-notes-action@v1.0.2`, `irongut/EditRelease@v1.0.0`, `docker/login-action@v1`, `docker/metadata-action@v3`, `docker/build-push-action@v2`
• test-linux.yml: `irongut/CodeCoverageSummary@master`
• test-macos.yml: `irongut/CodeCoverageSummary@master`
• test-windows.yml: `irongut/CodeCoverageSummary@master`

Locations:

- `action.yml:38`
- `.github/workflows/auto-assign-pr.yml:12`
- `.github/workflows/ci-build.yml:14`
- `.github/workflows/ci-build.yml:17`
- `.github/workflows/mark-stale.yml:10`
- `.github/workflows/release-build.yml:17`
- `.github/workflows/release-build.yml:22`
- `.github/workflows/release-build.yml:43`
- `.github/workflows/release-build.yml:49`
- `.github/workflows/release-build.yml:59`
- `.github/workflows/release-build.yml:65`
- `.github/workflows/release-build.yml:72`
- `.github/workflows/release-build.yml:79`
- `.github/workflows/test-linux.yml:13`
- `.github/workflows/test-macos.yml:13`
- `.github/workflows/test-windows.yml:13`

### script-injection (severity: high)

Sub-rule (a): In release-build.yml, the `Get Previous Tag` step directly interpolates `${{ github.ref }}` inside a `run:` shell command string. Before the shell executes the script, GitHub Actions performs YAML template substitution, allowing an attacker-controlled ref value to inject arbitrary shell commands.

Offending line:
  `PREV_TAG=$(git describe --abbrev=0 --tags "${{ github.ref }}^")`

Fix: pass the value via an `env:` variable and double-quote the shell expansion:
  ```yaml
  env:
    GIT_REF: ${{ github.ref }}
  run: |
    PREV_TAG=$(git describe --abbrev=0 --tags "${GIT_REF}^")
  ```

Locations:

- `.github/workflows/release-build.yml:39`

### missing-permissions (severity: medium)

Several workflow files have no top-level `permissions:` block and no job-level `permissions:` on every job, meaning jobs run with the default (potentially broad) token permissions:

• auto-assign-pr.yml — no top-level or job-level permissions defined.
• ci-build.yml — no top-level or job-level permissions defined.
• mark-stale.yml — no top-level or job-level permissions defined.
• release-build.yml — the `build` job has no `permissions:` block (only the `deploy` job does), and there is no top-level `permissions:` block.

Each file should declare a top-level `permissions:` block with the minimal required scopes (e.g. `contents: read`), or add explicit `permissions:` to every job.

Locations:

- `.github/workflows/auto-assign-pr.yml:1`
- `.github/workflows/ci-build.yml:1`
- `.github/workflows/mark-stale.yml:1`
- `.github/workflows/release-build.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three finding categories:

1. unpinned-uses: Pinned all action references to immutable SHA digests across all workflow files and action.yml. Docker image in action.yml pinned with sha256 digest while preserving the docker:// scheme and tag. All GitHub Actions pinned with full 40-char SHAs and original tag preserved in comments.

2. script-injection: In release-build.yml 'Get Previous Tag' step, moved `${{ github.ref }}` into an env: block as GIT_REF and referenced it as `"${GIT_REF}^"` in the shell script to prevent shell command injection.

3. missing-permissions: Added top-level permissions blocks to auto-assign-pr.yml (contents: read, pull-requests: write), ci-build.yml (contents: read), mark-stale.yml (contents: read, issues: write, pull-requests: write), and release-build.yml (top-level contents: read plus explicit job-level permissions for both build and deploy jobs).

