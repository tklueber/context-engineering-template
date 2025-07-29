### **3.4 .claude/commands/log-decision.md**
```bash
cat > .claude/commands/log-decision.md << 'EOF'
# Log Project Decision

## Purpose
Record project-level decisions in DECISIONS.md with proper categorization and impact analysis.

## Usage
```bash
/log-decision "DECISION_TITLE" --category="architecture|business|security|performance|ux|process" --impact="low|medium|high"
Examples:
bash/log-decision "Maximum 1000 participants limit" --category="business" --impact="medium"
/log-decision "WCAG AA compliance requirement" --category="ux" --impact="high"  
/log-decision "No analytics or tracking" --category="security" --impact="low"
Implementation
I will:

Generate decision ID (next available DEC-XXX)
Categorize decision using provided category
Assess impact level and affected components
Record stakeholders and decision makers
Document alternatives that were considered
Add to decision tracking table with review schedule
Update DECISIONS.md with proper formatting
Cross-reference with related ADRs or tasks

Decision Categories

🏛️ Architecture: Technical architecture decisions
📊 Business: Product and business logic decisions
🛡️ Security: Security-related decisions
⚡ Performance: Performance optimization decisions
🎨 UX/UI: User experience and interface decisions
🔧 Process: Development process and workflow decisions

Impact Assessment

Low: Affects single component, easy to change
Medium: Affects multiple components, moderate effort to change
High: Affects architecture/process, significant effort to change

Decision Entry Template
markdown### DEC-XXX: [Decision Title]
- **Date**: [Current date]
- **Category**: [Category icon] [Category name]
- **Decision**: [Clear statement of what was decided]
- **Rationale**: [Why this decision was made]
- **Alternatives Considered**: [What other options were evaluated]
- **Impact**: [Impact level] - [Description of affected areas]
- **Stakeholders**: [Who was involved in the decision]
- **Status**: ✅ Implemented / 🔄 In Progress / ⏳ Planned
- **Review Trigger**: [Conditions that would trigger review]
Automatic Updates

Add to decision tracking table
Update decision impact summary
Set review schedule based on impact level
Cross-reference with related technical ADRs

This ensures all project decisions are properly documented and tracked for future reference and review.
