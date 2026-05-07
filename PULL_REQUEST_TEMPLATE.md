## Pull Request (PR) Template Guidelines

To ensure clarity, consistency, and efficient code reviews, every Pull Request (PR) must follow a well-structured format. Use the template below and provide sufficient detail in each section.

---

### 1. Description

Clearly explain the purpose of the PR.

* What problem does this PR solve?
* What feature or improvement is being introduced?
* Why is this change necessary?

**Good Example:**

> This PR implements the login UI and integrates it with the authentication API. It addresses the missing entry point for user authentication in the application.

Avoid vague descriptions such as:

> "Updated code" or "Made changes"

---

### 2. Changes Made

Provide a concise, itemized list of all significant changes.

* Highlight key implementations or modifications
* Mention any refactoring, optimizations, or removals
* Keep it structured and easy to scan

**Example:**

* Implemented login UI with form validation
* Integrated frontend with authentication API
* Added error handling for invalid credentials
* Refactored authentication service for better readability

---

### 3. Screenshots / Recordings (For UI Changes)

If your PR includes UI/UX changes, you must provide visual proof.

* Add before/after screenshots when applicable
* Include short screen recordings for interactive features
* Ensure images are clear and relevant

This helps reviewers quickly understand the impact without running the code.

---

### 4. Checklist

Before submitting your PR, ensure all the following conditions are met:

* [ ] The code works as expected and fulfills the intended functionality
* [ ] There are no runtime errors, warnings, or broken components
* [ ] The changes follow the project’s coding standards and structure
* [ ] The branch is up to date with the latest `main` branch
* [ ] No unnecessary files, logs, or commented-out code are included
* [ ] All edge cases relevant to the task have been handled

---

### 5. Additional Notes (Optional but Recommended)

Include any extra context that reviewers should be aware of:

* Known limitations or pending improvements
* Dependencies or setup steps required to test the PR
* Assumptions made during implementation

---

### Key Expectations

* Keep PRs focused and limited in scope
* Avoid mixing unrelated changes in a single PR
* Ensure your PR is easy to review and understand
* Be responsive to feedback and make required changes promptly

---

A well-documented PR reduces review time, improves collaboration, and ensures higher code quality across the project.
