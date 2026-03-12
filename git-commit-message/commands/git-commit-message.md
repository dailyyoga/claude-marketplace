---
name: git-commit-message
description: Generate clear and standardized commit messages based on code changes.
---

# Generating Commit Messages

## Instructions

1. Run `git status` to view the modified files
2. Run `git diff` to view the specific changes
3. Generate clear commit messages according to *Specifications && Best Practices*
4. After commit messages are generated， options are provided for committing the message, committing, and pushing to the remote repository
5. Execute the git command to complete the operation based on the user's previous selection; otherwise, do nothing.

## Specifications && Best Practices

**IMPORTANT**: All commit messages MUST be in Chinese.

**IMPORTANT**: Only generate commit messages. Do NOT add any AI-generated signatures, company branding, or attribution markers (e.g., "Generated with Claude", "Co-Authored-By: Claude", emojis like 🤖, etc.). Keep commit messages clean and professional.

Follow Conventional Commits style for all commit messages，using a simple format like `<type>[optional scope]: <description>` to add meaning, marking commit history clearer for humans and enabling automation(like semantic versioning). Key types according *Types*，with `BREAKING CHANGE:` in the footer or body for major changes, ensuring explict, machine-readable context.

### Structure
```
<type>[optional scope]: <description>

- [optional body]

[optional footer(s)]
```

- `<type>`: The category of change (according to *Types* list)
- `[optional scope]`: A noum describing the section of code affected (e.g. `api`, `ui`)
- `<description>`: A concise summary (50-char guideline)
- `[optional body]`: Detailed explanation, one blank line after description
- `[optional footer(s)]`: For meta-info like `BREAKING CHANGE:` or issue references

### Types
- `feat` - New feature or functionality
- `fix` - Patches a bug
- `refactor` - Code refactoring without changing funtionality
- `docs` - Documentation changes
- `chore` - Maintenance tasks (dependencies, config, cleanup)
- `test` - Adding or updating tests
- `perf` - Performance improvements
- `style` - Code style/formatting changes
- `build` - Changes affecting build system or dependencies
- `ci` - Changes to CI config

### Examples
```
feat(auth): 添加企业微信账号登录

- 更新身份验证流程
- 添加新的用户界面元素

BREAKING CHANGE: 之前基于 JWT 的登录方式已被弃用
```
