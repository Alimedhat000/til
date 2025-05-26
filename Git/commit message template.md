To set a **commit message editor template** in Git, you can define a **template file** that will be pre-filled every time you make a commit (e.g., via `git commit` without `-m`). This is useful for standardizing commit messages across a team or project.

#### 1. **Create a Template File**
Create a file that contains your preferred commit message structure. For example:

```bash
nano ~/.gitmessage.txt
```

Add a template like this:

```
# Uncomment your change and write the commit message below.
# Follow the format: <type>(optional scope): <short summary>
#
# feat:
# A new feature for the user or system
#
# fix:
# A bug fix or patch for existing functionality
#
# docs:
# Documentation-only changes (e.g., README, code comments)
#
# style:
# Changes that do not affect code behavior (e.g., formatting, spacing)
#
# refactor:
# Code changes that neither fix a bug nor add a feature
#
# perf:
# Changes that improve performance
#
# test:
# Adding or updating tests
#
# chore:
# Maintenance tasks that don't modify src or test files (e.g., build process)
#
# build:
# Changes that affect the build system or dependencies (e.g., Makefile, package.json)
#
# ci:
# Changes to CI configuration files and scripts
#
# deploy:
# Changes related to deployment pipelines, environment configs, or tools

# Optional Body:
# Provide additional context or reasoning for the change.
# Wrap text at 72 characters per line.

# Optional Footer:
# - BREAKING CHANGE: clearly describe the breaking change
# - Closes #123, Fixes #456
```