## Contribution Guidelines

To maintain code quality, consistency, and smooth collaboration across the team, all contributors are expected to follow the guidelines outlined below.

### 1. Branching Strategy

* Direct commits or pushes to the `main` branch are strictly prohibited.
* All work must be done on a separate branch created from the latest `main`.

**Branch Naming Convention:**
Use the following format:

```
feature/<your-name>/<task-name>
```

**Examples:**

```
feature/ashwin/login-ui
feature/rahul/api-integration
fix/ashwin/navbar-bug 
```

* Use `feature/` for new features
* Use `fix/` for bug fixes
* Use `chore/` for maintenance or non-functional changes

Branch names should be short, descriptive, and written in lowercase using hyphens.

---

### 2. Pull Request (PR) Process

* Every change must be submitted through a Pull Request (PR).
* PRs should target the `main` branch unless specified otherwise.
* Do not merge your own PR without approval.

**PR Requirements:**

* At least **one approval** from another team member is mandatory before merging.
* Ensure your branch is up to date with `main` before requesting a review.
* Resolve all merge conflicts before merging.

---

### 3. Commit Guidelines

* Write clear, concise, and meaningful commit messages.
* Avoid vague messages like `fix`, `update`, or `changes`.

**Recommended Format:**

```
<type>: <short description>
```

**Examples:**

```
feat: add login page UI
fix: resolve navbar alignment issue
chore: update dependencies
```

* Keep commits focused on a single logical change.
* Do not bundle unrelated changes in one commit.

---

### 4. PR Best Practices

* Keep PRs **small, focused, and easy to review**.

* Clearly describe:

  * What changes were made
  * Why the changes were necessary
  * Any assumptions or trade-offs

* Link related issues or tasks if applicable.

* Add screenshots or recordings for UI-related changes.

---

### 5. Code Quality Expectations

* Follow consistent coding standards and project structure.
* Write readable, maintainable, and well-structured code.
* Avoid unnecessary complexity—prioritize clarity over cleverness.
* Ensure your changes do not break existing functionality.

---

### 6. General Rules

* Test your changes before submitting a PR.
* Do not leave commented-out or unused code.
* Respect deadlines and communicate blockers early.
* Be open to feedback and willing to revise your work.

---

Following these guidelines ensures a clean codebase, faster reviews, and efficient teamwork. Non-compliance may result in PR rejection or delays in merging.
