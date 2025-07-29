# Session Start Command

## Purpose
Initialize new Claude Code session with complete project context and current state.

## Usage
```bash
/session-start
Implementation
I will:

Read current project state from TASKS.md
Load last session context and progress
Check for any new issues or blockers
Prepare working environment for immediate productivity
Summarize next steps based on current priorities
Load relevant files into context
Check environment and dependencies

Process
Read Project State
typescript// Read TASKS.md to understand current status
const tasksContent = await readFile('TASKS.md');
const currentTasks = parseTasksFromMarkdown(tasksContent);
const inProgressTasks = currentTasks.filter(t => t.status === 'IN_PROGRESS');
const pendingTasks = currentTasks.filter(t => t.status === 'PENDING');
Load Context Files

TASKS.md - Current project state
INITIAL.md - Project requirements
CHANGELOG.md - Recent changes
docs/decisions/ADR-*.md - Technical decisions
DECISIONS.md - Project decisions

Prepare Working Environment

Check git status for uncommitted changes
Verify development environment setup
Load active task files into context
Identify immediate next steps

Output Format
markdown# 🚀 Session Started - [PROJECT_NAME]

## 📊 Current Status
- **Progress**: X% complete
- **Active Tasks**: X in progress, X pending
- **Last Updated**: [timestamp]
- **Next Priority**: [task description]

## 🎯 Immediate Next Steps
1. [Specific next action]
2. [Follow-up action]
3. [Context needed]

## 📁 Context Loaded
- [List of relevant files loaded]
- [Current working directory]
- [Environment status]

## ⚠️ Attention Needed
- [Any blockers or issues]
- [Dependencies to resolve]
- [Decisions pending]
Ready to begin productive work immediately!
