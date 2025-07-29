# Session End Command

## Purpose
Properly end a development session with comprehensive commit and state preservation.

## Usage
```bash
/session-end [--summary="session summary"]
Implementation
I will:

Review current session work and identify all changes
Validate all in-progress work is properly saved
Update TASKS.md with final session state
Generate session summary with accomplishments and next steps
Commit all session changes with comprehensive commit message
Prepare next session context in TASKS.md
Clean up temporary files and Object URLs
Verify git status is clean

Session Commit Message
chore(session): end development session - [date/time]

Session Summary:
- Completed: TASK-003 (Person Card Component)  
- Progressed: TASK-004 (Add Person Form) to 60%
- Created: ADR-003 (Accessibility-First Design)
- Decided: DEC-006 (No Analytics Policy)

Next Session Priorities:
1. Complete TASK-004 (Add Person Form)
2. Start TASK-005 (Slot Machine Component)  
3. Review ADR-003 implementation

Session Stats:
- Files Modified: 12
- Tests Added: 8
- Documentation Updated: 4 files
- Quality Gates: ✅ All passed

AI-Assisted: Claude Code
Session Duration: [calculated duration]

Co-authored-by: Claude Code <claude@anthropic.com>
State Preservation
Update TASKS.md with:

Current progress on all active tasks
Context for next session startup
Any blockers or issues encountered
Files that need attention next session
Environment status and setup notes

Clean Exit Checklist

✅ All meaningful changes committed
✅ TASKS.md reflects current state
✅ No uncommitted work-in-progress
✅ Next session priorities documented
✅ All temporary resources cleaned up
