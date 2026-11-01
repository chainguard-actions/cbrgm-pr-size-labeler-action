<!-- markdownlint-disable -->

# Hardening Report: cbrgm--pr-size-labeler-action/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--pr-size-labeler-action/v1.4.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The Docker action's runs.image points to a mutable tag instead of a SHA digest: `image: 'docker://ghcr.io/cbrgm/pr-size-labeler-action:v1'`. Pin it to a digest, for example ghcr.io/cbrgm/pr-size-labeler-action@sha256:<digest> # v1.

Locations:

- `action.yml:25`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned runs.image to docker://ghcr.io/cbrgm/pr-size-labeler-action:v1@sha256:bf7128c4366e0d93c1849bc3e61a530ba6b19d9bef6c570fa4292ce4214a3a3d

