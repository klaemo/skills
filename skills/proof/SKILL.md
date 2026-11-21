---
name: proof
description: Proof that a change works, as labelled before/after captures, a screen recording, or command output, shown in the conversation and attached to the PR. Use before reporting a UI or behavior change done, or when asked for proof.
---

# Proof

**Proof** is evidence observed in this session that shows the changed behavior: an image, a recording, or command output. It covers every surface the diff changes, in every state the diff touches. A sentence such as "visually verified" and a green mocked test are claims about proof; deliver the capture itself.

## 1. Map the surfaces

Read the diff against its base (the PR's base branch, the merge base with the remote default branch, or `HEAD` for uncommitted work), untracked files included. List every surface it changes: each screen, component, and route, each command, endpoint, and job. Follow a changed component to every place it renders. For each UI surface, list the states the diff touches: hover, tooltip, dialog or confirmation, empty, loading, error, dark mode, mobile width. A card action, for example, brings its card, its tooltip, and its confirmation dialog.

Pick the form per row:

- **Static UI**: a before/after pair, combined into one labelled collage per surface.
- **Motion or a multi-step flow**: a screen recording of the flow, before and after when the change alters the flow itself.
- **Non-UI behavior**: a reproduction or command output, run before and after.

Completion: a table of surface, state, and form in which every diff hunk maps to at least one row or is marked as having no observable effect, with the reason (types only, internal rename).

## 2. Prepare the drive

Find the project's verification method: a verification or QA skill, a documented way to start the app, seed data, and sign in. Follow it as written. Without one, use a browser automation tool the harness offers. Start servers the way the repository's instructions say, and record the handle of every process you start.

Get the before state from a separate worktree of the base (`git worktree add <dir> <base-sha>`), or by capturing before the change is applied. Keep every evidence file in a directory outside the repository: the harness scratchpad, or a fresh `mktemp -d`.

Completion: the app (or command) runs in both the before and after state, and the evidence directory lies outside every checkout.

## 3. Capture

For each row, drive to the state and capture before and after with the same viewport, data, and theme. Combine static pairs into a collage labelled with the surface name, "Before", and "After" (ImageMagick `montage`, or a small HTML page screenshotted with the browser tool). Start each recording from a recognizable state and keep it to the flow.

Open each capture and look at it: the named state is visible (the tooltip shown, the dialog open, the error rendered). Recapture any that miss.

Completion: every row has a capture you looked at that shows its state.

## 4. Deliver and clean up

Show each capture in the conversation with a one-line caption. When a PR exists, add a `## Proof` section to its body carrying the captures: on GitHub, `gh pr edit <number> --attach '<file>#<caption>'` (gh 2.99+); on other hosts, their attachment upload. When no PR exists yet, return each file's path and caption so the PR that follows can carry them. When no upload route works, say so and give the paths.

Stop every server this run started, by its recorded handle, and remove any worktree you added; leave everything else running. Confirm `git status` lists no evidence files.

For each change that has no proof, name the change and why it cannot be proven (no runnable environment, credentials required, production-only data).

Completion: the proof is shown, the PR body carries it when a PR exists, this run's servers and worktrees are gone, and every unproven change is named with its reason.
