---
name: git-cowork
description: >
  Guide the agent to interact with Git and GitHub CLI (gh) to perform operations like check-in, checkout, creating pull requests, pushing code, generating repositories, and reviewing/approving PRs. Use this skill when the user asks you to check out branches or PRs, commit/push code, create pull requests, initialize or publish repositories, or review/approve code on GitHub.
---

# Git & GitHub Cowork Capability

This skill provides step-by-step instructions and command sequences for interacting with Git and the GitHub CLI (`gh`). Follow these steps to ensure safe, structured, and correct contributions.

---

## 1. Authentication and Verification

Before performing any GitHub operations, check the authentication status.

1. Run the following command to check if you are logged in to GitHub CLI:
   ```bash
   gh auth status
   ```
2. If the user is authenticated, proceed with the task.
3. If not authenticated, check if the `GITHUB_TOKEN` environment variable is available.
4. If authentication fails or credentials are missing:
   - Request the user to run `gh auth login` to log in interactively, or
   - Use the `credentials` skill to prompt the user to configure their GitHub token safely.

---

## 2. Check-Out and Branching

### Working with Existing Branches
- To switch to an existing branch:
  ```bash
  git checkout <branch-name>
  # or
  git switch <branch-name>
  ```

### Checking out a Pull Request Locally
- To checkout a PR branch to inspect/test it:
  ```bash
  gh pr checkout <pr-number>
  ```

### Creating a New Feature or Bugfix Branch
- Always work on a separate branch. Check the current status first:
  ```bash
  git status
  ```
- Use a clear branch naming convention:
  - Features: `feat/short-description`
  - Bugfixes: `fix/short-description`
  - Documentation: `docs/short-description`
  ```bash
  git checkout -b <type>/<description>
  ```

---

## 3. Local Verification (Pre-Commit / Pre-Push)

Never push code that breaks compilation, linting, or tests.

1. Inspect the workspace for standard project configurations (e.g., `package.json`, `pubspec.yaml`, `requirements.txt`, `Makefile`, `pyproject.toml`).
2. Identify and run the appropriate commands to build, lint, and test:
   - **Node/JavaScript/TypeScript**: `npm run lint` / `npm test` / `npm run build`
   - **Flutter/Dart**: `dart analyze` / `flutter test`
   - **Python**: `pytest` / `flake8` / `black --check`
   - **Rust**: `cargo clippy` / `cargo test`
3. Resolve all errors locally before moving to the commit phase.

---

## 4. Check-In (Commit and Push)

### Staging Changes
- Review your changes first:
  ```bash
  git diff
  ```
- Stage only the relevant files (avoid staging temporary artifacts, logs, or node_modules):
  ```bash
  git add <file1> <file2>
  ```

### Formatting Commit Messages
Use the **Conventional Commits** format:
```
<type>(<scope>): <short description>

[Optional body explaining rationale or context]
```
*Types:* `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`.
*Example:* `feat(auth): add GitHub CLI dynamic authentication check`

- Create the commit:
  ```bash
  git commit -m "<message>"
  ```

### Pushing Code
- Push to the remote origin:
  ```bash
  git push -u origin <branch-name>
  ```
- If force-pushing is strictly necessary (e.g., after rebasing or clean-up), always use `--force-with-lease` and ensure the user approves:
  ```bash
  git push --force-with-lease
  ```

---

## 5. Pull Requests (PR)

### Generating a Pull Request
- Create a new PR on GitHub:
  ```bash
  gh pr create --title "<type>: <title>" --body "<body>"
  ```
- Recommend creating it as a draft first to allow final inspection:
  ```bash
  gh pr create --title "<type>: <title>" --body "<body>" --draft
  ```

### Automatically Generating PR Descriptions
- Read the git diff of your branch against the base branch (e.g., `main`) to write a descriptive and accurate PR body:
  ```bash
  git diff main...<branch-name>
  ```
- Structure the PR body with:
  - **Summary**: What this change does and why.
  - **Related Issues**: Any linked issues.
  - **Verification**: How the changes were tested.

---

## 6. Repository Generation

- To create a new repository from scratch:
  1. Initialize Git locally:
     ```bash
     git init
     ```
  2. Create the remote repository on GitHub and link/push to it:
     ```bash
     gh repo create <repo-name> --public --source=. --remote=origin --push
     # or for a private repository:
     gh repo create <repo-name> --private --source=. --remote=origin --push
     ```

---

## 7. PR Review and Approval

When asked to review or approve code:

1. Check out the PR branch locally:
   ```bash
   gh pr checkout <pr-number>
   ```
2. View the list of changed files and run a diff against the base branch:
   ```bash
   gh pr diff
   ```
3. Run local builds, lints, and test suites to verify correctness.
4. Perform the review:
   - **Approve**: If everything passes and the code looks correct:
     ```bash
     gh pr review --approve --body "LGTM! Tested locally and all checks passed."
     ```
   - **Request Changes / Comment**: If issues are found, leave constructive feedback:
     ```bash
     gh pr review --comment --body "<feedback>"
     # or
     gh pr review --request-changes --body "<feedback>"
     ```
