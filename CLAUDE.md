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

> All `pnpm` commands must be run from the back-office app directory.

**Required tools**: git, gh (GitHub CLI), pnpm, kubectl, Playwright, curl, Linear (MCP), Slack (MCP).

> Playwright must be launched with `playwright-cli open --extension --headed` to reuse the user's Chrome session and cookies. The user should be logged in to the back-office (locally or on staging) before starting.
> The user must be connected to the VPN.

1. **Read** the Linear ticket to understand the requirements. Also read the associated project and related tickets to gather context. Ask questions if anything is unclear.
2. **Setup workspace**: if not already inside the `clear-frontend` repo, clone `https://github.com/vibe-ad/clear-frontend` into a subdirectory named `clear-frontend-<ticket-id>` (e.g. `clear-frontend-dsp-123`) and `cd` into it.
3. **Branch**: checkout `staging`, pull latest, then create a branch named `<type>/back-office/<ticket-id>/<short-description>` (e.g. `feat/back-office/DSP-123/add-filter`).
4. **Implement** the feature/fix:
    - Commit with conventional commits including the ticket ref.
    - Use components from `@clear/ui` as much as possible.
    - Write unit tests for pure utility functions if the algorithm has edge cases or complexity.
    - Environment config is in `/charts/back-office/config` for dev, staging, and prod. If modified, run `pnpm envfile` to regenerate env files.
    - **NEVER** implement features from other tickets — if a dependency on another ticket's work is needed, use placeholders or stubs instead.
5. **Local CI**: run `pnpm run ci`. If it fails, fix the issues and re-run. Iterate until the command passes.
6. **Start dev server**: run `pnpm dev` in the background to start the local dev server. Monitor its logs for errors throughout the following steps.
7. **Push** the branch and open a **draft PR**.
8. **End-to-end testing** and **CI check** (in parallel):
    - **CI**: use `gh pr checks --watch` to monitor the CI pipeline. If it fails, fix, amend the commit, and force push. Iterate until CI is green.
    - **Browser**: if the PR has a test checklist, use Playwright to test each item against the running dev app. Navigate, click, inspect the DOM, and verify expected behavior. Check off each checklist item in the PR description as it passes. Take screenshots of the main screens during testing. Report any failures.
    - **API endpoints**: if the feature adds new endpoints, test them with `curl` against the local dev server. For endpoints under `/api/internal`, add the header `x-api-key: dev`. Check off each checklist item in the PR description as it passes. Report any failures.
9. **Ready for review**: once CI passes and browser tests look good, mark the PR as ready for review.
10. **PR feedback**: use `gh pr checks --watch` to wait for all CI checks to pass, including `Claude Code Review`. Once done, address all open review conversations on the PR. Fix the code when needed, amend the commit, and force push. Resolve conversations that are inconsistent. All conversations must be resolved before moving to the next step.
11. **Merge**: once no more feedback comes in, merge the branch into `staging` locally, bypassing branch protection rules, and push `staging` to the remote.
12. **Stop dev server**: stop the local dev server started in step 6.
13. **Deploy watch**: use `gh run watch` to monitor only the Docker build on `staging` CI. Then use `kubectl` to watch the `back-office` service pods until the new version is fully rolled out.
14. **DB migration**: if the PR includes a Postgres migration, run `pnpm db:migrate:staging` to apply it once the deploy is complete.
15. **Announce**: post a message on Slack in `#project-back-office` announcing the new version is live on staging. Include:
    - A clickable link to the new feature 
    - A link to the GitHub PR
    - A link to the Linear issue
    - Post the Playwright screenshots in a thread on this message.
    - Ask for feedback in thread on this message.
16. **Post-deploy monitoring** (30 minutes, in parallel):
    - **Logs**: watch `kubectl` logs for the `back-office` pods. If an error is detected, create a new branch from `staging` and open a fix PR.
    - **Slack feedback**: monitor the thread on the Slack announcement message. If pertinent feedback is received, create a new branch from `staging` and open an improvement PR.
17. **Cleanup**: delete the local screenshot files. If the workspace was cloned in step 2, `cd` out and delete the `clear-frontend-<ticket-id>` directory.
18. **Close ticket**: mark the Linear ticket as done if not already.

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
