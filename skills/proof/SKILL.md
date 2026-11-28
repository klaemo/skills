---
name: proof
description: Capture observed before/after proof (collages, screen recordings, or command output) for changed surfaces, shown in chat and attached to the PR. Use before reporting a UI or behavior change done, or when asked for proof.
---

# Proof

**Proof** is observed evidence from this session covering every surface and state the diff touches (not claims like "visually verified" or mocked tests).

## 1. Plan and capture

From the diff against its base (caller-supplied, PR base, default-branch merge-base, or `HEAD` for uncommitted work; untracked files included), map every changed surface (screens, components across all render sites, routes, commands, endpoints, jobs) and every touched UI state (hover, tooltip, dialog, empty, loading, error, dark mode, mobile width). Mark non-observable hunks (types, internal renames) with their reason.

Pick the form per surface:
- **Static UI**: labelled before/after collage per surface (matching trigger, viewport, data, theme — e.g. ImageMagick `montage` or screenshotted HTML).
- **Motion or multi-step flow**: screen recording (before and after if the flow changed).
- **Non-UI behavior**: before/after reproduction or command output.

Drive the app with the repo's verification/QA skill or documented setup if one exists; otherwise use browser automation. Get the before state via a separate base worktree (`git worktree add <dir> <base-sha>`) or before applying changes. Keep all evidence files **outside** every checkout (harness scratchpad or `mktemp -d`).

Inspect every capture to confirm the expected state and change are actually visible — fix any bug an after-capture reveals before continuing.

## 2. Deliver and clean up

Show every capture in chat with a one-line caption.

- **With a PR**: put `## Proof` as the **last** section of the PR body. On GitHub, write the heading and captioned fenced command blocks first (`--body-file`), then attach media with `gh pr edit <number> --attach '<file>#<caption>'` (`gh` 2.99+, which appends to the body end); re-read the PR body to confirm. Use the host's attachment upload elsewhere; if upload fails, say so and include paths.
- **Without a PR**: return media paths, captions, and command evidence so the caller can attach them when opening the PR.

Stop only servers started in this run (by recorded handle), remove any added worktree, and list any change that couldn't be proven with its reason.
