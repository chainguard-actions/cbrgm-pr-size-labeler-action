<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--pr-size-labeler-action/v1.4.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `runs.using: docker` with a mutable image tag instead of a SHA digest. The image reference `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1` uses the tag `v1`, which can be silently updated to point to a different (potentially malicious) image at any time. It should be pinned to an immutable SHA digest, e.g. `docker://ghcr.io/cbrgm/pr-size-labeler-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:23`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker image reference in action.yml from `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1` to `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1@sha256:b277b1fe8f4d2fbc8485e8ceda9ecdee22c2ad701c90623a24facf88b87287ba`. The `docker://` scheme and `:v1` tag are preserved inline, with the immutable SHA digest appended to prevent silent image substitution.

