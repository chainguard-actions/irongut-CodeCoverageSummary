<!-- markdownlint-disable -->

# Hardening Report: irongut--CodeCoverageSummary/v1.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **irongut--CodeCoverageSummary/v1.3.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml Docker action references the container image 'docker://ghcr.io/irongut/codecoveragesummary:v1.3.0' using a mutable version tag (v1.3.0) rather than an immutable SHA digest. A tag can be silently repointed to a different image, enabling a supply-chain attack. The image reference should use a SHA digest, e.g. 'docker://ghcr.io/irongut/codecoveragesummary@sha256:<64-hex-char-digest>'.

Locations:

- `action.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable container image tag 'docker://ghcr.io/irongut/codecoveragesummary:v1.3.0' with the immutable SHA digest 'docker://ghcr.io/irongut/codecoveragesummary@sha256:daebdede906ca84788b94378a1504a88d38621198d3836df24d60d6215e64a86' in action.yml line 46. The original tag is preserved as a comment outside the YAML string for readability.

