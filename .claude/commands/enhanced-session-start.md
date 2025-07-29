# Enhanced Session Start (with Git Integration)

## Purpose
Start development session with git status analysis and context loading.

## Usage
```bash
/session-start
Implementation
I will:

Check git repository status for uncommitted changes
Analyze recent commits since last session
Load current project state from TASKS.md
Identify any conflicts between git state and documented state
Prepare working environment with full context
Show session briefing with priorities and context

Git Status Analysis
bash# Check for uncommitted changes
git status --porcelain

# Review recent commits
git log --oneline -10

# Check for any stashed changes
git stash list

# Verify branch state
git branch -vv
Session Start Report
markdown# 🚀 Session Started - [PROJECT_NAME]

## 📊 Git Repository Status
- **Branch**: main (up to date)
- **Uncommitted Changes**: None / [list files if any]
- **Last Commit**: [hash] [message] ([time] ago)
- **Stashed Changes**: None / [count if any]

## 📋 Project Status (from TASKS.md)
- **Progress**: 45% complete
- **Active Tasks**: 2 in progress, 3 pending
- **Last Session**: [timestamp from last commit]
- **Next Priority**: Complete TASK-004 (Add Person Form)

## 🎯 Session Priorities (Next 2-4 hours)
1. **Complete TASK-004**: Add Person Form - accessibility testing remaining
2. **Start TASK-005**: Slot Machine Component - design review completed
3. **Review ADR-003**: Accessibility patterns - implementation validation

## 📁 Context Loaded
- TASKS.md - Current project state
- INITIAL.md - Project requirements
- ADR-003 - Accessibility-First Design decision
- /src/app/features/person-management/ - Active working directory

## ⚠️ Attention Needed
- [Any blockers from TASKS.md]
- [Any uncommitted changes to resolve]
- [Any dependency updates needed]

## 🔄 Recommended First Action
[Specific next step based on current state]

Ready to begin productive work immediately!
Conflict Resolution
If git state and TASKS.md state don't match:

Identify discrepancy (e.g., commits exist but TASKS.md not updated)
Analyze recent commits to understand what was done
Update TASKS.md to reflect actual current state
Commit the state correction with explanation
Proceed with corrected context


## 🔄 **Git Workflow Integration**

### **Conventional Commit Integration**
```bash
# In .gitmessage Template (erstelle diese Datei):
cat > .gitmessage << 'EOF'
# <type>(<scope>): <description>
# 
# <body>
#
# Related Task: TASK-XXX
# Related ADR: ADR-XXX (if applicable)  
# Related Decision: DEC-XXX (if applicable)
# AI-Assisted: Claude Code
# Validation: [tests passing/quality gates met]
#
# Co-authored-by: Claude Code <claude@anthropic.com>
#
# Types: feat, fix, docs, style, refactor, test, chore, perf, security
# Scope: component/feature being modified
# Description: imperative mood, no period, max 50 chars
# Body: explain what and why, not how (wrap at 72 chars)
EOF

# Git config für Template:
git config commit.template .gitmessage
