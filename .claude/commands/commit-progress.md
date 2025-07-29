# Commit Progress Command

## Purpose
Commit current progress with structured commit messages that link to tasks, ADRs, and decisions.

## Usage
```bash
/commit-progress TASK-ID "SUMMARY"
/commit-task-completion TASK-ID
/commit-adr ADR-ID
/commit-decision DEC-ID
Examples:
bash/commit-progress TASK-003 "person card component accessibility improvements"
/commit-task-completion TASK-003
/commit-adr ADR-002
/commit-decision DEC-005
Implementation
I will:

Analyze current changes with git status and git diff
Read relevant context from TASKS.md, ADRs, or DECISIONS.md
Generate structured commit message following conventional commits
Include references to tasks, ADRs, and decisions
Add co-author attribution for AI-assisted development
Execute git commit with proper message and metadata

Commit Message Format
<type>(<scope>): <description>

<body with details>

- Related Task: TASK-XXX
- Related ADR: ADR-XXX (if applicable)
- Related Decision: DEC-XXX (if applicable)
- AI-Assisted: Claude Code
- Validation: [tests passing/quality gates met]

Co-authored-by: Claude Code <claude@anthropic.com>
Commit Types

feat: New feature implementation
fix: Bug fixes and issue resolution
docs: Documentation updates (ADRs, decisions, changelog)
style: Code style changes (formatting, no logic changes)
refactor: Code refactoring without functionality changes
test: Adding or updating tests
chore: Maintenance tasks (dependencies, build config)
perf: Performance improvements
security: Security improvements or fixes

Task-Based Commits
For /commit-progress TASK-003:
feat(person-management): implement accessibility compliance for person card

- Added ARIA labels and keyboard navigation support
- Implemented screen reader compatibility
- Added high contrast mode support
- Achieved WCAG AA compliance

Related Task: TASK-003 (Person Card Component)
Related ADR: ADR-003 (Accessibility-First Design)
AI-Assisted: Claude Code
Validation: ✅ Unit tests passing, ✅ A11y tests passing

Co-authored-by: Claude Code <claude@anthropic.com>
ADR-Based Commits
For /commit-adr ADR-002:
docs(architecture): add ADR-002 for Angular Signals state management

Documents decision to use Angular Signals over RxJS for state management.
Includes performance benchmarks and migration strategy.

Related ADR: ADR-002 (Angular Signals State Management)
Related Task: TASK-004 (State Management Implementation)
AI-Assisted: Claude Code

Co-authored-by: Claude Code <claude@anthropic.com>
Decision-Based Commits
For /commit-decision DEC-005:
docs(security): add security policy for file upload validation

Documents multi-layer validation strategy for user file uploads.
Establishes MIME type whitelist and file header verification.

Related Decision: DEC-005 (File Upload Security)
Impact: Medium - affects upload workflow
AI-Assisted: Claude Code

Co-authored-by: Claude Code <claude@anthropic.com>
Automatic Staging
I will intelligently stage files based on the commit type:

TASK commits: Stage task-related source files + TASKS.md + CHANGELOG.md
ADR commits: Stage ADR file + related source files + DECISIONS.md if cross-referenced
Decision commits: Stage DECISIONS.md + related documentation
Completion commits: Stage all files related to completed task

Validation Before Commit

Verify all changes are related to stated task/ADR/decision
Check that tests are passing (if applicable)
Ensure documentation is updated
Validate commit message format
Confirm no sensitive information in commit
