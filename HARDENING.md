# Hardening Report: irongut--CodeCoverageSummary/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **irongut--CodeCoverageSummary/v1.1.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses a Docker image referenced by a mutable tag rather than an immutable SHA digest. `image: 'docker://ghcr.io/irongut/codecoveragesummary:v1.1.0'` uses the tag `v1.1.0`, which can be silently replaced with a different (potentially malicious) image at any time. It should be pinned to a specific SHA256 digest, e.g. `image: 'ghcr.io/irongut/codecoveragesummary@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:37`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag reference `docker://ghcr.io/irongut/codecoveragesummary:v1.1.0` with the immutable SHA256 digest `docker://ghcr.io/irongut/codecoveragesummary@sha256:68c08a769a70e7b89e34f2c7729173bdf1a8dd1bb8034842550537b45e21f595` in action.yml line 37. The original tag `v1.1.0` is preserved as a comment for readability.

