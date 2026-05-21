# CLAUDE.md

This file provides guidance for Claude Code when working in this repository.

## Repository Overview

This repository implements an automated PR review and AI safety guardrail system using Claude Code and GitHub Actions.

## AI Safety Guardrails

### Risk Classification

All PRs are automatically classified by `ai-risk-classify.yml`:

| Level | Label | Auto-merge | Criteria |
|-------|-------|------------|----------|
| 🟢 Green | `risk: green` | ✅ Yes | No protected paths, low-risk changes (docs, tests, minor UI) |
| 🟡 Yellow | `risk: yellow` | ❌ No | Config changes, non-critical logic tweaks |
| 🔴 Red | `risk: red` | ❌ No | Business logic, API changes, data model changes |
| ⚫ Black | `risk: black` | ❌ No | Security, auth, infra, trading logic, dependency changes |

### Protected Paths

PRs touching any of the following paths **cannot be auto-merged**, regardless of risk level:

- `.github/**` — GitHub Actions and CI/CD configuration
- `.claude/**` — Claude AI configuration
- `CLAUDE.md` — This file
- `infra/**`, `terraform/**`, `deploy/**` — Infrastructure
- `trading/**`, `broker/**`, `strategy/**` — Core trading logic
- `backtest/**`, `portfolio/**`, `rebalance/**` — Financial computation
- Dependency manifests: `package.json`, `requirements*.txt`, `pyproject.toml`, `go.mod`, `Cargo.toml`, etc.

### Auto-merge Conditions (Green PRs only)

A PR is eligible for automatic merge only when **all** of the following are true:

1. Labeled `risk: green`
2. No protected paths are modified
3. All CI checks pass
4. No blocking reviews from code owners

## Workflows

| Workflow | Trigger | Purpose |
|----------|---------|---------|
| `ci.yml` | Push / PR to master | Lint and test |
| `ai-risk-classify.yml` | PR open / update | Classify risk, apply label |
| `claude-pr-review.yml` | PR open / update | Automated code review |
| `auto-merge-green.yml` | PR labeled `risk: green` | Auto-merge eligible PRs |
| `claude-audit-sprint.yml` | Weekly (Mon 00:00 UTC) | Sprint audit report |

## Required Secrets

| Secret | Purpose |
|--------|---------|
| `ANTHROPIC_API_KEY` | Claude AI API access for review and classification |

## Code Review Guidelines for Claude

When reviewing PRs, evaluate:

1. **Correctness** — Does the code do what it claims?
2. **Security** — Any injection risks, auth bypasses, or secret exposure?
3. **Risk** — Would this cause financial or data loss if it fails?
4. **Test coverage** — Are edge cases handled?
5. **Consistency** — Does it follow existing patterns in the codebase?

## CODEOWNERS

Critical paths require review from `@Hyodokamo`:

- `.github/**`, `.claude/**`, `CLAUDE.md`
- `infra/**`, `terraform/**`, `deploy/**`
- `trading/**`, `broker/**`, `strategy/**`
- `backtest/**`, `portfolio/**`, `rebalance/**`
- `src/auth/**`, `src/security/**`
- All dependency manifest files
