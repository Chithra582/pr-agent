---
name: "automated-pr-description-generation"
description: "Formulates comprehensive PR titles, summaries, walkthroughs, and effort estimations based on git commits and diffs."
license: MIT
---

# Automated PR Description Generation

## Overview
This skill generates complete, high-quality pull request descriptions, replacing placeholder or empty PR bodies with structured summaries, architectural rationale, and file-by-file walkthroughs.

## Key Capabilities
- **Summary Synthesis**: Summarizes complex multi-commit PRs into concise, high-level overviews.
- **File Walkthrough Tables**: Maps each modified file to a bulleted description of its exact change.
- **Metadata Tagging**: Categorizes PRs by type (Feature, Bug fix, Refactor) and estimates review effort (1–5 scale).

## Operational Workflow
1. **Commit & Diff Parsing**: Extract PR commit messages and analyze the aggregated git diff.
2. **Context Synthesis**: Deduce the overarching intent and key functional additions.
3. **Markdown Authoring**: Format standardized PR description blocks with collapsible walkthrough tables.
4. **PR Body Update**: Update the pull request description directly via the Git platform API.
