---
name: "changelog-compliance-auditing"
description: "Updates changelog documentation and audits pull request compliance against ticketing systems and branch policies."
license: MIT
---

# Changelog and Compliance Auditing

## Overview
This skill audits pull requests against organizational policies, ticketing requirements (Jira, GitHub Issues), and formats user-facing changelog updates to maintain repository health.

## Key Capabilities
- **Changelog Extraction**: Formulates standardized Keep-a-Changelog entries for new PR features and fixes.
- **Ticket Compliance Auditing**: Verifies that PRs reference valid issue tickets and include testing plans.
- **Branch Policy Checking**: Ensures PR conventions (branch naming, commit formats) meet repository rules.

## Operational Workflow
1. **Compliance Check**: Inspect PR metadata against configured compliance rulesets.
2. **Changelog Formulation**: Synthesize human-readable changelog bullets from code changes.
3. **Audit Feedback**: Post verification results or warnings if compliance criteria are unmet.
4. **Documentation Commit**: Emit committable updates to the repository's `CHANGELOG.md`.
