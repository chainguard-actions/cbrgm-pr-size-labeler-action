<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.3.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cbrgm--pr-size-labeler-action/v1.3.7** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action's Docker image reference uses a mutable tag `:v1` instead of an immutable SHA digest. This means the image could be silently replaced with a different (potentially malicious) version without any change to the action.yml file. The reference `docker://ghcr.io/cbrgm/pr-size-labeler-action:v1` should be pinned to a specific SHA digest, e.g. `docker://ghcr.io/cbrgm/pr-size-labeler-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:23`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker image reference in action.yml from the mutable tag 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1' to the immutable digest 'docker://ghcr.io/cbrgm/pr-size-labeler-action@sha256:eee8dcd2a1ab1ab973c418f122131a956fbd3d3efe2e1e3d5a28e40eacc3b183' with '# v1' comment for readability.

