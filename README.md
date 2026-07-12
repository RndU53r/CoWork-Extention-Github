# Git & GitHub Cowork Skill for Claude/Antigravity

This documentation explains how to install, configure, and use the global `git-cowork` skill with Claude / Antigravity.

---

## 1. Installation

The `git-cowork` skill is a global customization. It is stored in your global configuration directory and is automatically discovered and loaded by the agent.

### Directory Structure

Global skills must be placed under your global customizations directory. Ensure your skill files are structured as follows:

```
~/.gemini/config/
└── skills/
    └── git-cowork/
        └── SKILL.md
```

### Setup Instructions

1. **Verify or Create the Directory**:
   Ensure the `git-cowork` directory exists in your global configuration directory:
   ```bash
   mkdir -p ~/.gemini/config/skills/git-cowork
   ```

2. **Deploy the SKILL.md**:
   Save the [SKILL.md](file:///Users/plashchowdhary/.gemini/config/skills/git-cowork/SKILL.md) file containing the frontmatter and Git guidelines to the path above.

3. **Automatic Discovery**:
   Once placed in the global `skills/` directory, the agent automatically loads this skill at startup. No manual registration is required in `skills.json` since it resides in a standard customization root.

---

## 2. Requirements & Prerequisites

The skill relies on the **GitHub CLI (`gh`)** and standard **Git** being installed on your host system.

### Install GitHub CLI (`gh`)
If not already installed, install it using your system package manager:
- **macOS**: `brew install gh`
- **Linux**: `sudo apt install gh` or similar
- **Windows**: `winget install --id GitHub.cli`

### Authenticate
Log in to your GitHub account to authorize the CLI:
```bash
gh auth login
```
Follow the interactive prompts to log in via your web browser or supply a Personal Access Token (PAT) with `repo` scopes.

---

## 3. How to Use the Skill

When chatting with the agent, you can ask it to perform Git and GitHub operations. Because the skill's description triggers on Git/GitHub-related terms, the agent will automatically reference the skill and follow its guidelines.

### Example Prompts to Trigger the Skill

Here are some ways you can instruct the agent to run the Git workflows:

| Workflow | Example User Prompt |
|----------|---------------------|
| **Create Repository** | *"Initialize a new Git repository for this project and push it to a new private repository on GitHub."* |
| **Feature Work / Check-in** | *"Create a branch for user authentication, commit my local changes using Conventional Commits, and push the branch."* |
| **Pull Request Creation** | *"Open a draft pull request on GitHub for my current branch and write a description based on the diff."* |
| **PR Review / Checkout** | *"Check out PR #42 locally, run the test suite, and approve the PR if it passes."* |

---

## 4. Key Workflows the Agent Performs

When the skill is active, the agent follows strict protocols:

1. **Authentication Check**: First checks if it is authenticated by running `gh auth status` or searching for `$GITHUB_TOKEN`.
2. **Local Validation**: Prior to staging or pushing, the agent locates standard project files (like `package.json`, `pubspec.yaml`, or `requirements.txt`) and runs verification commands (`npm test`, `pytest`, `cargo test`, etc.).
3. **Structured Commits**: Uses **Conventional Commits** (e.g. `feat(ui): add navbar`) and creates descriptive branches (e.g. `feat/navbar`).
4. **Local PR Review**: When asked to approve code, the agent checks out the PR branch locally, runs builds/tests, and only executes `gh pr review --approve` if all checks pass.
