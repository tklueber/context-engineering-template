# Enhanced Update Progress Command (with Git Integration)

## Purpose
Update task progress with automatic git commits at significant milestones.

## Usage
```bash
/update-progress TASK-ID STATUS "NOTES" [--commit]
Examples:
bash/update-progress TASK-003 "IN_PROGRESS" "Basic functionality complete" --commit
/update-progress TASK-003 "COMPLETED" "All acceptance criteria met" --commit
Implementation
I will:

Update TASKS.md with new status and notes
Validate status change and check dependencies
Update progress metrics and completion percentages
Generate changelog entry if completed
Create ADR if needed for technical decisions
Auto-commit changes if --commit flag provided or significant milestone
Unlock dependent tasks if completion criteria met

Automatic Commit Triggers
Auto-commit occurs for:

✅ Task Completion: When status changes to COMPLETED
✅ Major Milestones: When task reaches 50%, 75%, 90% completion
✅ Blocking/Unblocking: When task becomes BLOCKED or gets unblocked
✅ First Progress: When task moves from PENDING to IN_PROGRESS

Commit Message Generation
For different status changes:
IN_PROGRESS:
feat(TASK-003): start person card component implementation

- Initialized component structure and basic layout
- Added TypeScript interfaces for Person model
- Set up unit test framework

Related Task: TASK-003 (Person Card Component)
Progress: 25% → 40%
AI-Assisted: Claude Code

Co-authored-by: Claude Code <claude@anthropic.com>
COMPLETED:
feat(TASK-003): complete person card component with accessibility

- Implemented full CRUD functionality for person management
- Added accessibility compliance (WCAG AA)
- Achieved performance targets (<50ms render time)
- Added comprehensive unit tests (95% coverage)

Related Task: TASK-003 (Person Card Component) ✅ COMPLETED
Related ADR: ADR-003 (Accessibility-First Design)
Validation: ✅ All tests passing, ✅ A11y audit passed
AI-Assisted: Claude Code

Co-authored-by: Claude Code <claude@anthropic.com>
BLOCKED:
chore(TASK-005): block slot machine implementation pending design review

Current implementation blocked waiting for UX design feedback
on animation performance vs battery usage trade-offs.

Related Task: TASK-005 (Slot Machine Component) ❌ BLOCKED
Blocking Issue: Waiting for design feedback on user flow
Expected Resolution: 1-2 days
AI-Assisted: Claude Code

Co-authored-by: Claude Code <claude@anthropic.com>
File Staging Strategy
Automatically stage relevant files:

Always: TASKS.md, CHANGELOG.md (if updated)
Source Files: Files modified for the specific task
Tests: Related test files
Documentation: README.md, ADRs, decisions if updated
Configuration: package.json, angular.json if dependencies changed

Quality Gates
Before committing completion:

✅ All acceptance criteria met
✅ Tests written and passing
✅ Code follows style guidelines
✅ Documentation updated
✅ Performance targets achieved
✅ Security requirements satisfied
✅ Accessibility compliance verified
