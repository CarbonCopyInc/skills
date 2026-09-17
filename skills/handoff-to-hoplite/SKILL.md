---
name: handoff-to-hoplite
description: Continue a local Claude Code, Codex, or OpenCode coding session in a live Hoplite cloud thread by safely running hoplite handoff. Use when the user asks to hand off, transfer, move, or continue the current local agent session in Hoplite, including requests to carry the current Git branch or local changes into the cloud.
---

# Handoff to Hoplite

Start one Hoplite cloud thread from the current local coding session and return its URL. Preserve the user's continuation instructions while keeping local Git mutations explicit.

## Prepare the handoff

1. Run `command -v hoplite` and `hoplite handoff --help`. Treat the installed help output as the source of truth for supported flags. If the command is unavailable, stop and tell the user to install the Hoplite CLI.
2. Confirm the intended checkout with `git rev-parse --show-toplevel`, `git branch --show-current`, and `git status --short --branch`. Do not switch branches, rewrite commits, or change remotes for a handoff.
3. Use the current directory, detected repository, current branch, most recently active supported session, and default model unless the user specifies an override. Add `--cwd`, `--harness`, `--repo`, `--branch`, `--model`, or `--title` only when the user asks for it or automatic detection cannot identify the intended value safely.
4. Convert any requested follow-up work into one concise `--prompt` argument. Do not copy the conversation into the prompt; `hoplite handoff` uploads the safe user/assistant transcript separately.

## Decide whether to push

Treat an explicit handoff request as authorization to upload the transcript and create one Hoplite thread. Do not treat it as authorization to commit or push Git changes.

- If the working tree is clean and the required commits are already available on GitHub, run without `--autopush`.
- If files are modified or commits appear unpushed, explain that the cloud workspace cannot see local-only code and ask whether to include it with `--autopush`, unless the user already explicitly asked to commit, push, or include those changes.
- Before using `--autopush`, inspect `git status --short` and stop if it contains unrelated changes, generated artifacts, or files that may contain secrets. The flag stages all non-ignored changes, creates a commit when needed, and pushes the selected branch.
- Never add `--autopush` merely to suppress a warning.

Use `--dry-run` only when the user requests a preview or session-selection diagnosis. It prints the generated transcript, so do not run it routinely or paste its transcript into chat.

## Run the command

Pass values as individual, safely quoted arguments and never through `eval` or a dynamically assembled shell program.

For a normal handoff:

```bash
hoplite handoff --prompt 'Finish the PR and run the relevant tests'
```

When the user has explicitly authorized committing and pushing all reviewed local changes:

```bash
hoplite handoff --autopush --prompt 'Finish the PR and run the relevant tests'
```

Omit `--prompt` when the user has no additional continuation instruction. Do not run a second handoff after the command returns a thread URL.

## Handle failures

- If authentication is missing, report the error and instruct the user to run `hoplite onboard`; do not onboard or import local data without authorization.
- If Hoplite's GitHub App cannot access the repository, report the exact repository named by the error and ask the user to update the installation.
- If more than one organization or project matches, stop rather than guessing.
- After a transient upload or network failure, retry the exact same command once. Keep every argument unchanged so the command can reuse its stable operation identifier.
- Do not claim success unless the command prints a Hoplite thread URL.

## Report the result

Return the thread URL, the selected repository and branch, and whether local changes were committed or pushed. If `--autopush` was used, run `git rev-parse HEAD` and include the resulting commit SHA. Do not paste the uploaded transcript or claim that ignored files, running processes, tool state, or hidden reasoning were transferred.
