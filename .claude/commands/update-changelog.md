### **3.5 .claude/commands/update-changelog.md**
```bash
cat > .claude/commands/update-changelog.md << 'EOF'
# Update Changelog Command

## Purpose
Automatically update CHANGELOG.md based on completed tasks, commits, and decisions.

## Usage
```bash
/update-changelog TASK-ID "DESCRIPTION"
/generate-release-changelog 0.2.0
/changelog-from-commits v0.1.0..HEAD
Examples:
bash/update-changelog TASK-003 "Added person card component with accessibility compliance"
/update-changelog TASK-005 "Implemented slot machine animation with 60fps performance"
Implementation
I will:

Read completed task details from TASKS.md
Categorize the change (Added, Changed, Fixed, Security, Performance)
Generate appropriate changelog entry with proper formatting
Add to Unreleased section with [AUTO-GENERATED] tag
Include relevant metrics and validation results
Cross-reference with ADRs and decisions if applicable
Update last modified timestamp

Auto-categorization Rules

Added: New features, components, services, functionality
Changed: Modifications to existing features or behavior
Fixed: Bug fixes, issue resolutions, error corrections
Security: Security improvements, vulnerability fixes, validation enhancements
Performance: Performance optimizations, bundle size improvements, speed enhancements
Breaking: Changes that break existing functionality (rare, requires explicit flag)

Changelog Entry Format
markdown### Added
- [AUTO-GENERATED] [Feature description] ([TASK-ID])
  - [Specific implementation details]
  - [Performance metrics if applicable]
  - [Related ADR: ADR-XXX] if technical decision involved

### Security
- [AUTO-GENERATED] [Security improvement] ([TASK-ID])
  - [Security validation details]
  - [Related decision: DEC-XXX] if policy decision involved
Release Generation
When generating release changelog:

Collect all Unreleased entries
Group by category (Added, Changed, etc.)
Add release metadata (date, version, metrics)
Include performance benchmarks from completed tasks
List known issues and limitations
Add migration notes if applicable
Move to versioned section and clear Unreleased

Cross-referencing

Link to completed tasks in TASKS.md
Reference related ADRs for technical changes
Reference related decisions for business changes
Include validation metrics and success criteria

This ensures comprehensive, accurate, and automatically maintained project history.
