# Branch Protection Standards

Branch protection rules must be applied to the default branch (`main`) across all repositories in the AIBO Assistant ecosystem.

---

## Enforced Branch Protection Rules

1. **Require Pull Request Reviews**:
   - Minimum of 1 approving review from an authorized code owner before merge.
   - Dismiss stale pull request approvals when new commits are pushed.
   - Require review from Code Owners (defined in `.github/CODEOWNERS`).
2. **Require Status Checks to Pass**:
   - Status checks must pass before merging.
   - Require branches to be up to date with `main` before merging.
3. **Require Conversation Resolution**: All comments and review threads must be resolved before merging.
4. **Enforce Restrictions**:
   - Restrict force pushes (`git push --force` strictly prohibited).
   - Restrict branch deletions.
   - Linear commit history recommended (squash-and-merge or rebase-and-merge).

---

## Required Status Checks by Repository

| Repository | Required Status Check Names | Success Criteria |
| --- | --- | --- |
| **`AIBO-BACKEND`** | `lint-and-typecheck`, `unit-and-integration-tests`, `e2e-scenarios`, `dependency-audit` | Clean ESLint/TSC, 343 Jest tests pass, 92 E2E scenarios pass, no high/critical CVEs. |
| **`AIBO-FRONTEND`** | `lint-and-typecheck`, `vitest-components`, `production-build` | Clean ESLint/TSC, 87 Vitest component tests pass, Vite production build exits 0. |
| **`AIBO-ENGINE-V1.0`** | `mypy-typecheck`, `pytest-suite`, `wheel-build` | Strict Mypy typecheck passes, 606 Pytest cognitive engine tests pass, package builds. |
| **`.github`** | `markdown-lint`, `schema-validation`, `link-check` | Documentation syntax, template YAML, and link integrity verified. |

---

## Emergency Bypass Policy

Administrative bypass (`--no-verify` or admin merge overrides) is restricted to critical production outages (Sev 1). Every emergency bypass must be documented in a post-mortem incident report with an associated remediation pull request within 24 hours.
