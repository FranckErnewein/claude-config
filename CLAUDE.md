# CLAUDE.md

## Language

- Always write code, comments, commit messages, and PR descriptions in English, even when the conversation is in French.

## Principles

- Keep it simple: make it clear rather than clever.
- YAGNI: don't add features we don't need right now.
- Ship first: shipping enables the feedback loop.
- You build it, you run it.

## Code

- Make the smallest reasonable changes to achieve the desired outcome.
- Readability and maintainability over cleverness or performance.
- Match the style and formatting of surrounding code.
- Don't over-engineer: the code must properly support the current use-case, nothing more.

## Zergling mode (on demand)

When I ask you to enter "zergling mode" with a Linear ticket reference, follow this process:

1. **Read** the Linear ticket to understand the requirements. Also read the associated project and related tickets to gather context. Ask questions if anything is unclear.
2. **Branch**: checkout `staging`, pull latest, then create a branch named `<type>/<ticket-id>-<short-description>` (e.g. `feat/DSP-123-add-filter`).
3. **Implement** the feature/fix, committing with conventional commits including the ticket ref. **NEVER** implement features from other tickets — if a dependency on another ticket's work is needed, use placeholders or stubs instead.
4. **Push** the branch and open a **draft PR**.
5. **CI check**: monitor the CI pipeline. If it fails, fix, amend the commit, and force push. Iterate until CI is green.
6. **Browser test**: if the PR has a test checklist, use Playwright to test each item against the running dev app. Navigate, click, inspect the DOM, and verify expected behavior. Check off each checklist item in the PR description as it passes. Report any failures.
7. **Ready for review**: once CI passes and browser tests look good, mark the PR as ready for review.
8. **PR feedback**: poll the PR for review comments every 2 minutes for 15 minutes. Handle any feedback received.
## Git

- Use conventional commits. Include the Linear ticket reference at the end of the title (e.g. `feat: add segment filter [DSP-123]`).
- Never use `git add -A` or `git add .` — always stage specific files.
- Always ask before pushing to a remote branch.
- Never force push to main/staging branches.

## Communication

- Be honest: say when you don't know something rather than guessing.
- Don't be a sycophant — give your real technical opinion.
- Push back when you disagree, with specific reasons.
- When in doubt, ask rather than assume.
