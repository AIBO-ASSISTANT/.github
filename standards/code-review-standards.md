# Code Review Standards

## Reviewer Responsibilities

- Review for correctness, security, maintainability, and operational impact.
- Check that tests match the risk.
- Check that docs remain accurate.
- Ask for an ADR when a decision changes architecture.
- Prefer concrete suggestions over broad preference comments.

## Author Responsibilities

- Keep the PR scoped.
- Explain tradeoffs and validation.
- Respond to each review thread.
- Update docs and tests with code behavior.
- Do not dismiss security or data-integrity concerns without evidence.

## Review Checklist

- Does this preserve service boundaries?
- Does it validate external input?
- Does it avoid leaking secrets or user data?
- Does it handle dependency failure?
- Does it introduce a new runtime dependency?
- Does it need an ADR?
- Does it change the feature maturity matrix?

## Approval Expectations

At least one approval is required. Require additional owner review for:

- auth/session changes
- database migrations
- deployment or workflow changes
- API breaking changes
- security-sensitive changes
- cross-repository contracts
