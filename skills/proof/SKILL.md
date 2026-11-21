---
name: proof
description: Proof of an observable change, as before/after captures, a screen recording, or command output, shown in the conversation and attached to the PR. Use before reporting a UI or behavior change done, or when asked for proof.
---

# Proof

**Proof** is evidence observed in this session that shows the changed behavior: an image, a recording, or command output. It covers every surface the diff changes, in every state the diff touches. A sentence such as "visually verified" and a green mocked test are claims about proof; deliver the capture itself.

## 1. Map the surfaces

Read the diff against its base (the one the caller supplies, else the PR's base branch, the merge base with the remote default branch, or `HEAD` for uncommitted work), untracked files included. List every surface it changes: each screen, component, and route, each command, endpoint, and job. Follow a changed component to every place it renders. For each UI surface, list the states the diff touches: hover, tooltip, dialog or confirmation, empty, loading, error, dark mode, mobile width. A card action, for example, brings its card, its tooltip, and its confirmation dialog.

Pick the form per row:

- **Static UI**: a before/after pair, combined into one labelled collage per surface.
- **Motion or a multi-step flow**: a screen recording of the flow, before and after when the change alters the flow itself.
- **Non-UI behavior**: a reproduction or command output, run before and after.

Completion: a table of surface, state, form, and the observation that would show the change works, in which every diff hunk maps to at least one row or is marked as having no observable effect, with the reason (types only, internal rename).

## 2. Prepare the drive

Find the project's verification method: a verification or QA skill, a documented way to start the app, seed data, and sign in. Follow it as written. Without one, use a browser automation tool the harness offers. Start servers the way the repository's instructions say, and record the handle of every process you start.

Get the before state from a separate worktree of the base (`git worktree add <dir> <base-sha>`), or by capturing before the change is applied. Keep every evidence file in a directory outside the repository: the harness scratchpad, or a fresh `mktemp -d`.

Completion: the app (or command) runs in the after state, the before state runs or is recorded as unavailable with the reason, and the evidence directory lies outside every checkout.

## 3. Capture

For each row, drive to the state and capture what its form calls for, with the same trigger, viewport, data, and theme on both sides of a comparison. Combine static pairs into a collage labelled with the surface name, "Before", and "After" (ImageMagick `montage`, or a small HTML page screenshotted with the browser tool). Start each recording from a recognizable state and keep it to the flow.

Open each capture and look at it: the named state is visible (the tooltip shown, the dialog open, the error rendered) and the after side shows the row's expected observation. Recapture any that miss the state; an after side that shows the change failing is a bug to fix before proof continues.

Completion: every row has a capture you looked at that shows its expected observation, or a missing side recorded with its reason.

## 4. Deliver and clean up

Show each capture in the conversation with a one-line caption. When a PR exists, its body ends with a `## Proof` section: command evidence in captioned fenced blocks, then the uploaded media. On GitHub, `gh pr edit <number> --attach '<file>#<caption>'` (gh 2.99+) uploads images and video and appends them to the end of the body, so write the heading and the command evidence as the last section first (`--body-file`), then attach. On other hosts, use their attachment upload. Re-read the PR body and confirm every capture sits under the heading. When no PR exists yet, return each media file's path and caption and the command evidence as text, so the caller that opens the PR delivers them this same way. When an upload fails, say so and give the paths.

Stop every server this run started, by its recorded handle, and remove any worktree you added; leave everything else running. Confirm `git status` lists no evidence files.

For each change that has no proof, name the change and why it cannot be proven (no runnable environment, credentials required, production-only data).

Completion: the proof is shown, the PR body carries it when a PR exists, this run's servers and worktrees are gone, and every unproven change is named with its reason.
