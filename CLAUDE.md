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

## Back-office-dev mode (on demand)

When I ask you to enter "back-office-dev mode" with a Linear ticket reference, follow this process:

1. **Read** the Linear ticket to understand the requirements. Also read the associated project and related tickets to gather context. Ask questions if anything is unclear.
2. **Branch**: checkout `staging`, pull latest, then create a branch named `<type>/back-office/<ticket-id>/<short-description>` (e.g. `feat/back-office/DSP-123/add-filter`).
3. **Implement** the feature/fix, committing with conventional commits including the ticket ref. Use components from `@clear/ui` as much as possible. **NEVER** implement features from other tickets — if a dependency on another ticket's work is needed, use placeholders or stubs instead.
4. **Local CI**: run `pnpm run ci`. If it fails, fix the issues and re-run. Iterate until the command passes.
5. **Start dev server**: run `pnpm dev` in the background to start the local dev server. Monitor its logs for errors throughout the following steps.
6. **End-to-end testing**:
    - **Browser**: if the PR has a test checklist, use Playwright to test each item against the running dev app. Navigate, click, inspect the DOM, and verify expected behavior. Check off each checklist item in the PR description as it passes. Report any failures.
    - **API endpoints**: if the feature adds new endpoints, test them with `curl` against the local dev server. For endpoints under `/api/internal`, add the header `x-api-key: dev`.
7. **Push** the branch and open a **draft PR**.
8. **CI check**: monitor the CI pipeline. If it fails, fix, amend the commit, and force push. Iterate until CI is green.
9. **Ready for review**: once CI passes and browser tests look good, mark the PR as ready for review.
10. **PR feedback**: poll the PR for review comments every 2 minutes for 15 minutes. Handle any feedback received. If there is feedback, address it, amend the commit, force push, and wait another 15 minutes for further comments. Repeat until no new feedback is received within a 15-minute window.
11. **Merge**: once no more feedback comes in, merge the branch into `staging` and push `staging` to the remote.
12. **Stop dev server**: stop the local dev server started in step 5.
13. **Deploy watch**: monitor CI on `staging`. Then use `kubectl` to watch the `back-office` service pods until the new version is fully rolled out.
14. **DB migration**: once the deploy is complete, run `pnpm db:migrate:staging` to apply pending Postgres migrations.
15. **Announce**: post a message on Slack in `#project-back-office` announcing the new version is live on staging. Include:
    - A link to the new feature (`bo.staging.vibe.co/[...]`)
    - A link to the GitHub PR
    - A link to the Linear issue
    - Ask for feedback in thread on this message.
16. **Post-deploy monitoring** (30 minutes, in parallel):
    - **Logs**: watch `kubectl` logs for the `back-office` pods. If an error is detected, create a new branch from `staging` and open a fix PR.
    - **Slack feedback**: monitor the thread on the Slack announcement message. If pertinent feedback is received, create a new branch from `staging` and open an improvement PR.

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
