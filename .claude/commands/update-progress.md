### **3.2 .claude/commands/update-progress.md**
```bash
cat > .claude/commands/update-progress.md << 'EOF'
# Update Progress Command

## Purpose
Update task progress in TASKS.md with validation and automatic documentation updates.

## Usage
```bash
/update-progress TASK-ID STATUS "NOTES"
Examples:
bash/update-progress TASK-003 "IN_PROGRESS" "Basic functionality complete, working on accessibility"
/update-progress TASK-003 "COMPLETED" "All acceptance criteria met, tests passing"
/update-progress TASK-005 "BLOCKED" "Waiting for design feedback on user flow"
Parameters

TASK-ID: The task identifier (e.g., TASK-003)
STATUS: One of: PENDING, IN_PROGRESS, COMPLETED, BLOCKED, TESTING
NOTES: Description of current state or completion details

Implementation
I will:

Read TASKS.md and locate the specified task
Validate the status change is logical and allowed
Update task status with timestamp and notes
Check dependencies and unlock dependent tasks if applicable
Update progress percentages across all task categories
Generate changelog entry if task is completed
Create ADR if technical decision was made
Log decision if business decision was made
Validate completion criteria if status is COMPLETED

Status Validation Rules

PENDING → IN_PROGRESS ✅
IN_PROGRESS → COMPLETED ✅
IN_PROGRESS → BLOCKED ✅
IN_PROGRESS → TESTING ✅
TESTING → COMPLETED ✅
TESTING → IN_PROGRESS ✅ (if issues found)
BLOCKED → IN_PROGRESS ✅ (if unblocked)

Completion Validation
When marking COMPLETED, I will verify:

All acceptance criteria are met
Tests are written and passing
Documentation is updated
Code follows project standards
Security requirements satisfied
Performance targets met
Accessibility compliance achieved

Automatic Updates

TASKS.md: Task status, timestamps, progress percentages
CHANGELOG.md: New entry for completed features
DECISIONS.md: Business decisions made during task
docs/decisions/: ADR files for technical decisions

This ensures comprehensive tracking and documentation of all progress.
