# Project Workflow (Autonomous Agent Edition)

## Guiding Principles

1. **Strict Autonomy:** All development is executed by an autonomous agent (Jules). You operate in a headless cloud environment. You DO NOT have access to a web browser, a UI, or a human to answer questions.
2. **The Plan is the Source of Truth:** All work must be strictly tracked in `plan.md`. Never hallucinate tasks outside of this plan.
3. **Non-Interactive Execution:** Every shell command executed must be non-interactive. Always use flags like `CI=true`, `--yes`, `--quiet`, `--no-input`, or `--non-interactive` to prevent the terminal from hanging. **NEVER start long-running watch servers (e.g., `npm run dev`, `npm run watch`).**
4. **Test-Driven Development (TDD):** Automated tests are the ONLY way to verify code. Write unit tests before implementing functionality.
5. **The Failsafe Protocol:** If you encounter an unresolvable error after a maximum of 3 attempts, you MUST commit your current progress and push to the remote branch rather than looping indefinitely or waiting for user input.

## Task Workflow

All tasks follow a strict, headless lifecycle:

### Standard Task Workflow

1. **Select Task:** Choose the next available pending task (`[ ]`) from `plan.md` in sequential order.
2. **Mark In Progress:** Before beginning work, edit `plan.md` and change the task from `[ ]` to `[~]`.
3. **Write Failing Tests (Red Phase):**
   - Create or update a test file for the feature or bug fix.
   - Run the tests and confirm they fail. Do not proceed until you have failing tests validating the requirement.
4. **Implement to Pass Tests (Green Phase):**
   - Write the minimum application code necessary to make the failing tests pass.
   - Run the test suite again to confirm passing status.
5. **Refactor & Verify:**
   - Refactor code to improve clarity without changing external behavior.
   - Run the project's linter and formatter. Fix any auto-detectable errors.
6. **Failsafe Enforcement:**
   - If tests, linters, or builds fail, attempt to debug and fix the code a **maximum of 3 times**.
   - **FAILSAFE TRIGGER:** If the issue persists after 3 attempts, STOP debugging. Execute `git add . && git commit -m "chore(jules): failsafe triggered - saving partial work" && git push`, then halt execution entirely.
7. **Commit Code Changes:**
   - Stage all code changes related to the task.
   - Perform the commit with a clear, conventional commit message (e.g., `feat(api): implement user auth endpoint`).
8. **Attach Task Summary with Git Notes:**
   - Obtain the hash of the commit: `git log -1 --format="%H"`.
   - Attach a brief, automated summary: `git notes add -m "Task Complete. All tests passing headlessly." <commit_hash>`.
9. **Record Completion:**
   - Edit `plan.md` to change the task status from `[~]` to `[x]`. Append the short commit SHA to the task line.
   - Commit this change: `git commit -am "conductor-jules(plan): mark task complete"`.

### Phase Completion Verification and Checkpointing Protocol

**Trigger:** This protocol is executed automatically after the final task in a Phase is completed in `plan.md`.

1. **Calculate Phase Scope:** Execute `git diff --name-only <previous_checkpoint_sha> HEAD` (or against the first commit if no previous checkpoint) to list all code files modified during this phase.
2. **Verify Test Coverage:** Ensure every modified source code file has a corresponding test file.
3. **Execute Full Suite:** Run the ENTIRE automated test suite for the project (e.g., `CI=true npm test`) to ensure no regressions were introduced across the codebase.
4. **Create Phase Checkpoint:**
   - If all tests pass, create an empty commit: `git commit --allow-empty -m "conductor-jules(checkpoint): Automated verification complete for Phase X"`.
   - Obtain the hash of the checkpoint commit (`git log -1 --format="%H"`).
   - Update `plan.md` by appending the hash to the Phase heading in the format `[checkpoint: <sha>]`.
5. **Final Push:** Execute `git push` to ensure the completed, verified phase is synced to the remote repository.

## Quality Gates

Before marking any task complete, you must internally verify:

- [ ] All automated tests pass in a CI environment (exit code 0).
- [ ] Code coverage meets the project target (>80%).
- [ ] No linting or static analysis errors exist.
- [ ] Type safety is strictly enforced and compiles without warnings.
- [ ] No interactive prompts or debugging statements (`console.log`, `print`) are left in the code.
- [ ] You did not start any long-running web servers (e.g., `npm start`) that block the terminal.

## Development Commands

**AI AGENT INSTRUCTION:** Always append non-interactive flags to these commands based on the project framework.

### Setup Example
```bash
# Node.js:
npm ci --prefer-offline --no-audit

# Python:
pip install -r requirements.txt --no-input