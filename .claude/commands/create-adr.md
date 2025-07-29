## **3.3 .claude/commands/create-adr.md**
```bash
cat > .claude/commands/create-adr.md << 'EOF'
# Create Architecture Decision Record

## Purpose
Generate comprehensive ADR for technical decisions with proper analysis and documentation.

## Usage
```bash

/create-adr "DECISION_TITLE" --alternatives="alt1,alt2,alt3" --category="architecture|performance|security"
Examples:
bash/create-adr "Use Dexie.js instead of raw IndexedDB" --alternatives="raw IndexedDB,localForage,IDB library"
/create-adr "Implement client-side image compression" --alternatives="server-side,no compression,user choice"
Implementation
I will:

Generate ADR number (next available ADR-XXX)
Create structured ADR file in docs/decisions/
Analyze decision context and problem statement
Compare alternatives with pros/cons analysis
Document implementation details with code examples
Include success metrics and validation criteria
Set review schedule and update triggers
Link to related decisions and tasks

ADR Template Structure
markdown# ADR-XXX: [Decision Title]

## Status
**ACCEPTED** - [Date]

## Decision Makers
- Claude Code (Primary Implementation)
- Human Developer (Technical Review)
- [Stakeholders]

## Context and Problem Statement
[Problem description and requirements]

## Decision
[Clear statement of what was decided]

## Rationale
### ✅ Pros of Chosen Solution
- [Benefits with evidence]

### ❌ Cons of Alternatives
- [Why other options were rejected]

### 📊 Comparison Analysis
[Table comparing all options]

## Implementation Details
[Code examples and technical specifics]

## Consequences
### Positive
- [Benefits and improvements]

### Negative  
- [Costs and limitations]

### Risk Mitigation
- [How risks are addressed]

## Validation and Metrics
- [Success criteria and benchmarks]

## Related Decisions
- [Links to other ADRs and decisions]

## References
- [Documentation and resources]

---
**Review Date**: [6 months from decision]
**Last Updated**: [Current timestamp]
**Decision ID**: ADR-XXX
File Naming Convention

docs/decisions/ADR-001-use-dexie-instead-of-indexeddb.md
docs/decisions/ADR-002-implement-offline-first-architecture.md
etc.

This creates comprehensive technical decision documentation that provides context for future development and maintenance.
