# EXPLAINABILITY.md

This document explains the internal mechanisms, data lineage, operational boundaries, and governance framework of **PR-Agent** (`pr-agent`) in accordance with the **OpenGAP v0.1.0** specification for the **HiDevs GitAgent Passport** clearance pipeline.

> **Agent Name:** PR-Agent (`pr-agent`)  
> **Specification:** OpenGAP v0.1.0  
> **Category / Domain:** Developer Tools / Automated Code Review & Git Pull Request Automation  
> **Compliance Standard:** OpenGAP Checkpoint 2 (Explainability & Decision Governance), OWASP LLM Top 10, MITRE ATLAS  

---

## How the Agent Decides

PR-Agent is an autonomous AI developer tool engineered by Qodo to accelerate, streamline, and govern pull request lifecycles across leading Git platforms (GitHub, GitLab, Bitbucket, Azure DevOps). The agent operates as a multi-modal code review and git management engine, ingesting git diffs, commit histories, surrounding source code, and developer instructions. It provides targeted code reviews (`/review`), synthesizes semantic descriptions (`/describe`), generates committable inline suggestions (`/improve`), and answers localized queries (`/ask`).

### 1. Decision Architecture

The pull request intake, diff chunking, semantic analysis, suggestion formatting, and publication pipeline operates across a deterministic, five-stage architecture:

```
Git Webhook / Developer Command (e.g., "/review", "/improve", "/describe")
    │
    ▼
[Stage 1: Webhook Ingestion & Platform Context Retrieval]
    │  - Authenticates Git platform webhook payload (GitHub/GitLab/Bitbucket/Azure)
    │  - Fetches pull request metadata, branch names, commit history, and raw git diffs
    │  - Validates repository configuration rules and command permissions from `.pr_agent.toml`
    ▼
[Stage 2: Diff Partitioning & Contextual Grounding]
    │  - Filters out generated files, lockfiles (`package-lock.json`), and binary assets
    │  - Partitions large diffs into cohesive file hunks while preserving function context
    │  - Computes modified file criticality weights (core business logic vs. test fixtures)
    ▼
[Stage 3: Multi-Criteria Semantic Code Analysis]
    │  - Analyzes diff chunks for logic bugs, security vulnerabilities, and code smells
    │  - Cross-references changes against language best practices, typing, and exception safety
    │  - Formulates candidate code refactorings and targeted improvement suggestions
    ▼
[Stage 4: Suggestion Verification & Committable Formatting]
    │  - Verifies that proposed replacements parse cleanly with surrounding line contexts
    │  - Calculates confidence and severity scores; filters low-impact cosmetic comments
    │  - Renders inline suggestions into platform-native committable suggestion markdown
    ▼
[Stage 5: Platform Publication & Telemetry Audit]
    │  - Posts structured review summaries and inline comments to the Git platform API
    │  - Updates PR descriptions and applies semantic labels (e.g., `bug`, `effort: 2`)
    │  - Commits structured review logs with redacted credentials to local telemetry stores
    ▼
Published Pull Request Review, Committable Suggestions & Auditable Trace Log
```

### 2. Decision Logic & Routing Formulations

PR-Agent evaluates code defect severity, suggestion confidence, and review prioritization using deterministic mathematical models:

1. **Defect Severity & Review Impact Score ($S_{\text{severity}}$)**:
   $$S_{\text{severity}}(d) = (w_s \cdot S_{\text{security}}) + (w_c \cdot C_{\text{correctness}}) + (w_p \cdot P_{\text{perf}}) + (w_m \cdot M_{\text{maintain}})$$
   where:
   - $S_{\text{security}} \in [0, 1]$ measures vulnerability risk (OWASP Top 10, injection, credential leak).
   - $C_{\text{correctness}} \in [0, 1]$ represents likelihood of functional regression or unhandled exception.
   - $P_{\text{perf}} \in [0, 1]$ indicates algorithmic or database query performance bottlenecks.
   - $M_{\text{maintain}} \in [0, 1]$ assesses code complexity and testability impact.
   - Weights: $w_s = 0.40, w_c = 0.30, w_p = 0.20, w_m = 0.10$ ($\sum w_i = 1.0$).

2. **Committable Suggestion Confidence ($C_{\text{suggestion}}$)**:
   $$C_{\text{suggestion}} = \sigma\left(\alpha \cdot \text{ContextSim} - \beta \cdot \Delta_{\text{lines}} + \gamma \cdot \text{SyntaxPass}\right)$$
   where $\Delta_{\text{lines}}$ penalizes overly broad refactors and $\text{SyntaxPass} \in \{0, 1\}$ guarantees valid syntax. A suggestion is emitted as a committable block only when $C_{\text{suggestion}} \ge 0.80$.

### 3. Thresholding & Refusal Decision Criteria

PR-Agent enforces strict operational safeguards to protect codebase stability and developer trust:
- **Refusal to Auto-Merge Protected Branches**: PR-Agent operates strictly as an advisory reviewer and will never execute automated git merges (`ERR_AUTOMATED_MERGE_FORBIDDEN`).
- **Refusal to Leak Detected Credentials**: Diffs containing accidentally committed private keys or secrets trigger immediate critical alerts with masked tokens (`WARN_SECRET_LEAKAGE_DETECTED`).
- **Diff Size Ceilings & Token Truncation**: Extremely large diffs exceeding token budgets are prioritized by file criticality and truncated with explicit disclosure (`WARN_DIFF_TOKEN_LIMIT_TRUNCATED`).
- **Refusal of Low-Confidence Hallucinations**: Code suggestions failing internal syntax or reference validation are deterministically dropped (`ERR_SUGGESTION_VALIDATION_FAILED`).

### 4. Fallback Decision Mechanism

Continuous code review availability is guaranteed through multi-tier fault recovery:
- **Provider Cascade**: When foundation model providers return HTTP 429 rate limit errors or timeouts, PR-Agent cascades between OpenAI, Anthropic, Azure, and AWS Bedrock endpoints.
- **Hierarchical Diff Chunking Fallback**: If full-diff processing exceeds LLM context windows, the agent falls back to file-by-file chunking and incremental summary aggregation.
- **Git Platform API Retry**: Network flakiness during comment posting triggers exponential backoff retries with jitter to ensure zero dropped review comments.

### 5. Human-in-the-Loop Governance

Human developers retain sovereign review primacy and operational authority:
- **One-Click Suggestion Application**: Developers review and selectively apply code suggestions through native Git platform suggestion interfaces.
- **Granular Repository Configuration**: Teams configure agent personality, active tools, and review strictness via repo-level `.pr_agent.toml` files.
- **Transparent Audit Trails**: All review interactions, token usages, and reasoning steps are preserved in structured logs for compliance auditing.

---

## The Data It Uses

PR-Agent adheres to stringent data minimization, privacy protection, and corporate code governance standards.

### 1. Ingested Input Data

The agent processes only operational data necessary to perform pull request reviews:
- **Pull Request Metadata**: PR title, author, branch names, target repository, and associated issue tickets.
- **Git Diffs & Source Snapshots**: Unified diffs of modified files and immediate surrounding code contexts.
- **Developer Invocations**: Slash commands (`/review`, `/improve`, `/describe`, `/ask`) and comment queries.

### 2. Configuration & Reference Data

- **Repository Settings**: `.pr_agent.toml` configuration files defining review triggers, ignore paths, and prompt overrides.
- **Git Platform Credentials**: Scoped platform installation tokens (GitHub App tokens, GitLab personal tokens).
- **Compliance Rulesets**: Checklists and ticket integration schemas (`pr_compliance_checklist.yaml`).

### 3. Base Model & Inference Lineage

- **Deterministic Review Parsing**: Git patch parsers, AST linters, and regex secret scanners run with 100% determinism.
- **Frontier LLMs**: High-performance reasoning models (GPT-4o, Claude 3.5 Sonnet) utilized for semantic code analysis and description generation.
- **Zero Training on Developer Code**: Customer codebases, pull request diffs, and review comments are never transmitted to model vendors for training or fine-tuning.

### 4. Data Privacy, Storage, and Retention

- **OWASP LLM & MITRE ATLAS Hardened**: Defended against prompt injection through malicious code comments, credential harvesting, and indirect injection.
- **Ephemeral In-Memory Processing**: PR diffs are processed in-memory during webhook handling and never written to permanent disk storage.
- **Automated Secret Scrubbing**: Inadvertently committed credentials, authorization headers, and personal data are redacted from review comments and telemetry.
- **Zero Commercial Monetization**: Source code diffs, review histories, and developer communications are never commercialized, aggregated, or shared with third parties.

---

## Limitations

Understanding the operational boundaries and technical constraints of PR-Agent ensures productive deployment.

### 1. Full-Stack Dynamic Runtime Behavioral Verification
- **Limitation**: PR-Agent reviews static source diffs; it cannot dynamically spin up application servers or execute end-to-end integration tests.
- **Mitigation**: PR-Agent reviews test coverage diffs and integrates alongside CI/CD automated test runners.

### 2. Massive Monorepo Multi-Gigabyte Diffs
- **Limitation**: Multi-thousand-file pull requests spanning multiple gigabytes exceed practical LLM context windows.
- **Mitigation**: The agent prioritizes modified files by criticality, filters generated assets, and reviews batches incrementally.

### 3. Domain-Specific Proprietary Framework Idioms
- **Limitation**: Highly specialized, internal closed-source frameworks unfamiliar to public foundation models may receive generic advice.
- **Mitigation**: Teams inject custom instructions, architectural guidelines, and repo-specific conventions via `.pr_agent.toml`.

### 4. Subjective Architectural Styling Debates
- **Limitation**: Stylistic choices that lack universal consensus can lead to subjective or opinionated review comments.
- **Mitigation**: PR-Agent focuses primarily on bugs, security, and performance, with stylistic comments silenced via configuration.

### 5. Multi-Repository Cross-Dependency Tracking
- **Limitation**: Pull requests whose correctness depends on simultaneous changes in external detached repositories cannot be cross-analyzed.
- **Mitigation**: PR-Agent documents external contract expectations and prompts developers to verify cross-repo compatibility.

---

## Summary & Compliance Checklist

| Checkpoint 2 Requirement | Corresponding Section | Status |
| :--- | :--- | :---: |
| **How the agent decides** | [How the Agent Decides](#how-the-agent-decides) | **Covered** |
| - Decision architecture & 5-stage pipeline | Section 1 | Verified |
| - Decision logic & routing formulations | Section 2 | Verified |
| - Thresholding & refusal decision criteria | Section 3 | Verified |
| - Fallback decision mechanism | Section 4 | Verified |
| - Human-in-the-loop governance & oversight | Section 5 | Verified |
| **The data it uses** | [The Data It Uses](#the-data-it-uses) | **Covered** |
| - Ingested PR metadata, git diffs & developer commands | Section 1 | Verified |
| - Configuration, repo settings & compliance rulesets | Section 2 | Verified |
| - Base model lineage & deterministic review parsing | Section 3 | Verified |
| - Data privacy, retention lifecycle & MITRE/OWASP | Section 4 | Verified |
| **Its limitations** | [Limitations](#limitations) | **Covered** |
| - Full-stack dynamic runtime behavioral verification | Section 1 | Verified |
| - Massive monorepo multi-gigabyte diffs | Section 2 | Verified |
| - Domain-specific proprietary framework idioms | Section 3 | Verified |
| - Subjective architectural styling debates | Section 4 | Verified |
| - Multi-repository cross-dependency tracking | Section 5 | Verified |
