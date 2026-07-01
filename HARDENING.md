<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.3.10

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cbrgm--pr-size-labeler-action/v1.3.10** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses a Docker image pinned to a mutable tag (':v1') rather than an immutable SHA digest. This means the image could be silently replaced with a different (potentially malicious) version without any change to the action.yml. The failing reference is: `image: 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1'`. It should be replaced with a SHA-digest reference such as `image: 'docker://ghcr.io/cbrgm/pr-size-labeler-action@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:23`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag ':v1' in action.yml line 23 with the immutable SHA digest 'ghcr.io/cbrgm/pr-size-labeler-action@sha256:77ee5bf485d4e2aab13a5eb571117a60a0691983b2fb19d240ed137d2d055609', with '# v1' comment for readability.

