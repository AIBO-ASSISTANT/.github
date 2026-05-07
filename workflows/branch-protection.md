# Branch Protection Recommendations

Apply to `main` in every repository.

## Recommended Settings

- require pull request before merge
- require at least one approval
- dismiss stale approvals after new commits
- require conversation resolution
- require status checks
- require branches to be up to date before merge when practical
- restrict force pushes
- restrict deletions
- require signed commits if the organization adopts signing consistently

## Required Status Checks by Repository

| Repository | Checks |
| --- | --- |
| `AIBO-BACKEND` | lint, test, dependency audit |
| `AIBO-FRONTEND` | lint, test, build, dependency audit |
| `AIBO-ENGINE` | unit tests |
| `.github` | governance structure check |

## Admin Bypass

Admin bypass should be rare and documented in the pull request or incident record.
