# Project Workflow

## Guiding Principles

1. **The Plan is the Source of Truth:** All work must be tracked in `plan.md`
2. **The Tech Stack is Deliberate:** Changes to the tech stack must be documented in `tech-stack.md` *before* implementation
3. **Test-Driven Development:** Write unit tests before implementing functionality
4. **High Code Coverage:** Aim for >80% code coverage for all modules
5. **User Experience First:** Every decision should prioritize user experience
6. **Non-Interactive & CI-Aware:** Prefer non-interactive commands. Use `CI=true` for watch-mode tools (tests, linters) to ensure single execution.

## Task Workflow

All tasks follow a strict lifecycle:

### Standard Task Workflow

1. **Select Task:** Choose the next available task from `plan.md` in sequential order.

2. **Mark In Progress:** Before beginning work, edit `plan.md` and change the task from `[ ]` to `[~]`.

3. **Write Failing Tests (Red Phase):**
   - Create a new test file for the feature or bug fix.
   - Write one or more unit tests that clearly define the expected behavior and acceptance criteria for the task.
   - **CRITICAL:** Run the tests and confirm that they fail as expected. This is the "Red" phase of TDD. Do not proceed until you have failing tests.

4. **Implement to Pass Tests (Green Phase):**
   - Write the minimum amount of application code necessary to make the failing tests pass.
   - Run the test suite again and confirm that all tests now pass. This is the "Green" phase.

5. **Refactor (Optional but Recommended):**
   - With the safety of passing tests, refactor the implementation code and the test code to improve clarity, remove duplication, and enhance performance without changing the external behavior.
   - Rerun tests to ensure they still pass after refactoring.

6. **Verify Coverage:** Run coverage reports using the project's chosen tools. For example, in a Python project, this might look like:
   ```bash
   pytest --cov=app --cov-report=html
   ```
   Target: >80% coverage for new code. 

7. **Document Deviations:** If implementation differs from tech stack:
   - **STOP** implementation
   - Update `tech-stack.md` with new design
   - Add dated note explaining the change
   - Resume implementation

8. **Commit Code Changes:**
   - Stage all code changes related to the task.
   - Perform the commit with a clear, concise commit message (e.g., `feat(ui): Create basic HTML structure for calculator`).

9. **Attach Task Summary with Git Notes:**
   - Obtain the hash of the *just-completed commit* (`git log -1 --format="%H"`).
   - Create a detailed summary for the completed task.
   - Attach the note using `git notes add -m "<note content>" <commit_hash>`.

10. **Get and Record Task Commit SHA:**
    - Update `plan.md` task status from `[~]` to `[x]`, appending the first 7 characters of the commit hash.

11. **Commit Plan Update:**
    - Stage the modified `plan.md` file and commit (e.g., `conductor-jules(plan): Mark task 'Create user model' as complete`).

### Autonomous Failure Protocol

If you are stuck on an error (e.g., tests fail persistently after 3 attempts, or a shell command throws an unrecoverable error), you MUST NOT ask the user for help. You must gracefully fail using the following protocol:

1. **Branch:** Execute `git checkout -b conductor-jules/failed-<track_id>-<task_name_slug>`.
2. **Commit WIP:** Stage all current work and commit: `git commit -m "chore(blocked): WIP for <task_name> - <brief error description>"`.
3. **Update Plan:** Edit the `plan.md` file. Change the current task status from `[~]` to `[BLOCKED]`. Append a brief summary of why it is blocked.
4. **Terminate:** Exit your execution loop successfully so the parent dispatcher process (`gemini-cli`) knows you yielded gracefully.

### Phase Completion Verification and Checkpointing Protocol

**Trigger:** This protocol is executed immediately after a task is completed that also concludes a phase in `plan.md`.

1.  **Ensure Test Coverage for Phase Changes:**
    -   **Determine Phase Scope:** Read `plan.md` to find the Git commit SHA of the *previous* phase's checkpoint.
    -   **List Changed Files:** Execute `git diff --name-only <previous_checkpoint_sha> HEAD`.
    -   **Verify and Create Tests:** For each code file in the list, verify a corresponding test file exists. If missing, create it. 

2.  **Execute Automated Tests with Proactive Debugging:**
    -   Execute the automated test suite (e.g., `CI=true npm test`).
    -   If tests fail, begin debugging. You may attempt to propose a fix a **maximum of three times**.
    -   **CRITICAL:** If tests still fail after your third attempt, you **MUST** execute the **Autonomous Failure Protocol**. Do NOT pause to ask the user for manual verification or guidance.

3.  **Create Checkpoint Commit:**
    -   Stage all changes. If no changes occurred in this step, proceed with an empty commit.
    -   Perform the commit with a clear message (e.g., `conductor-jules(checkpoint): Checkpoint end of Phase X`).

4.  **Attach Auditable Verification Report using Git Notes:**
    -   Create a detailed verification report including the automated test command and its successful output.
    -   Attach the full report to the checkpoint commit using `git notes`.

5.  **Get and Record Phase Checkpoint SHA:**
    -   Obtain the hash of the checkpoint commit.
    -   Update `plan.md`, appending the first 7 characters of the hash in the format `[checkpoint: <sha>]`.

6. **Commit Plan Update:**
    - Stage the modified `plan.md` file and commit following the format `conductor-jules(plan): Mark phase '<PHASE NAME>' as complete`.

## Quality Gates

Before marking any task complete, verify autonomously:

- [ ] All tests pass
- [ ] Code coverage meets requirements (>80%)
- [ ] Code follows project's code style guidelines
- [ ] All public functions/methods are documented
- [ ] Type safety is enforced
- [ ] No linting or static analysis errors
- [ ] No security vulnerabilities introduced

## Development Commands

### Setup
```bash
# Example: Commands to set up the development environment
```

### Daily Development
```bash
# Example: Commands for common daily tasks
```

### Before Committing
```bash
# Example: Commands to run all pre-commit checks
```

## Testing Requirements

### Unit Testing
- Every module must have corresponding tests.
- Use appropriate test setup/teardown mechanisms.
- Mock external dependencies.
- Test both success and failure cases.

### Integration Testing
- Test complete user flows
- Verify database transactions
- Test authentication and authorization
- Check form submissions

## Code Review Process

### Self-Review Checklist
Before requesting review:

1. **Functionality**
   - Feature works as specified
   - Edge cases handled
   - Error messages are user-friendly

2. **Code Quality**
   - Follows style guide
   - DRY principle applied
   - Clear variable/function names
   - Appropriate comments

3. **Testing**
   - Unit tests comprehensive
   - Integration tests pass
   - Coverage adequate (>80%)

4. **Security**
   - No hardcoded secrets
   - Input validation present
   - SQL injection prevented
   - XSS protection in place

5. **Performance**
   - Database queries optimized
   - Caching implemented where needed

## Commit Guidelines

### Message Format
```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

### Types
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Formatting, missing semicolons, etc.
- `refactor`: Code change that neither fixes a bug nor adds a feature
- `test`: Adding missing tests
- `chore`: Maintenance tasks

## Definition of Done

A task is complete when:

1. All code implemented to specification
2. Unit tests written and passing
3. Code coverage meets project requirements
4. Code passes all configured linting and static analysis checks
5. Implementation notes added to `plan.md`
6. Changes committed with proper message
7. Git note with task summary attached to the commit

## Emergency Procedures

### Critical Bug in Production
1. Create hotfix branch from main
2. Write failing test for bug
3. Implement minimal fix
4. Deploy immediately
5. Document in plan.md

### Data Loss
1. Stop all write operations
2. Restore from latest backup
3. Verify data integrity
4. Document incident
5. Update backup procedures

### Security Breach
1. Rotate all secrets immediately
2. Review access logs
3. Patch vulnerability
4. Document and update security procedures

## Deployment Workflow

### Pre-Deployment Checklist
- [ ] All tests passing
- [ ] Coverage >80%
- [ ] No linting errors
- [ ] Environment variables configured
- [ ] Database migrations ready
- [ ] Backup created

### Deployment Steps
1. Merge feature branch to main
2. Tag release with version
3. Push to deployment service
4. Run database migrations
5. Verify deployment
6. Test critical paths

### Post-Deployment
1. Monitor analytics
2. Check error logs
3. Plan next iteration