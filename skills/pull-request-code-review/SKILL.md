---
name: "pull-request-code-review"
description: "Analyzes pull request diffs to detect security vulnerabilities, logic bugs, performance regressions, and style issues."
license: MIT
---

# Pull Request Code Review

## Overview
This skill executes in-depth, multi-dimensional code reviews on proposed pull request changes, surfacing critical security vulnerabilities, concurrency bugs, algorithmic inefficiencies, and style deviations.

## Key Capabilities
- **Multi-Criteria Inspection**: Evaluates changes for correctness, security vulnerabilities, test coverage, and performance.
- **Severity Scoring**: Prioritizes findings into high, medium, and low severity tiers.
- **Structured Feedback**: Generates clean markdown summaries with expandable file breakdown details.

## Operational Workflow
1. **Diff Retrieval**: Ingest PR git diff and surrounding source file context.
2. **Static Analysis & Filtering**: Filter out non-essential binary or generated lockfiles.
3. **Semantic Review Evaluation**: Evaluate code additions against language idioms and security standards.
4. **Report Generation**: Publish structured review comment to the host Git platform.
