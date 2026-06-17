<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.3.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cbrgm--pr-size-labeler-action/v1.3.9** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image reference with a mutable tag instead of a SHA digest. `image: 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1'` uses the tag `:v1`, which can be silently updated to point to different (potentially malicious) content. It should be pinned to an immutable SHA digest, e.g. `image: 'docker://ghcr.io/cbrgm/pr-size-labeler-action@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:23`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker image reference in action.yml line 23 from the mutable tag `ghcr.io/cbrgm/pr-size-labeler-action:v1` to the immutable digest `ghcr.io/cbrgm/pr-size-labeler-action@sha256:eee8dcd2a1ab1ab973c418f122131a956fbd3d3efe2e1e3d5a28e40eacc3b183 # v1`. The original tag is preserved as a comment for readability.

