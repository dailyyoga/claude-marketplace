---
name: code-review
description: >
  Universal code review tool. Auto-detects project tech stack, reviews against CLAUDE.md
  project conventions and language/framework best practices. Supports any language and framework
  (React, Vue, Go, Rust, Flutter, etc.). Usage: /code-review [file paths...]
---

# Code Review Skill

Universal code review tool for any tech stack. Auto-detects project language and framework, combines project conventions (CLAUDE.md) with language/framework best practices, and outputs structured review results.

## Workflow

### Step 1: Detect Project Tech Stack

Auto-detect the tech stack by reading config files in the project root (read if exists, skip if not):

| Config File | Detection |
|-------------|-----------|
| `package.json` | Node.js ecosystem, frontend framework (React/Vue/Angular), build tools, dependencies |
| `go.mod` | Go project, module path, dependencies |
| `Cargo.toml` | Rust project |
| `tsconfig.json` | TypeScript config, path aliases |
| `next.config.*` / `nuxt.config.*` / `vite.config.*` / `angular.json` | Specific framework confirmation |
| `.eslintrc*` / `biome.json` / `.prettierrc*` / `.golangci.yml` | Code style rules |
| `Dockerfile` / `docker-compose.yml` | Deployment architecture |
| `pubspec.yaml` | Flutter/Dart project |

**Output**: A tech stack summary (language, framework, build tools, linters) to guide which best practices to apply.

### Step 2: Load Project Conventions

Read project-level convention files in priority order (read if found, used as highest-priority review criteria):

1. **`CLAUDE.md`** (primary) — Extract all coding conventions, architecture patterns, naming rules, prohibited practices, etc.
2. **`.cursorrules`** (supplementary)
3. **`.editorconfig`** (basic formatting: indentation, encoding)

If no `CLAUDE.md` exists, rely entirely on auto-detected tech stack + universal best practices.

### Step 3: Determine Review Scope

Based on user input, determine which files to review:

**Case A — File paths specified**:
```
/code-review src/views/UserList.vue pkg/handler/auth.go
```
Read and review each specified file.

**Case B — No files specified**:
- Run `git diff --name-only` and `git diff --cached --name-only` to get changed files
- Run `git diff` and `git diff --cached` to view specific changes
- If no changes found, prompt user to specify files or make code changes first

### Step 4: Analyze Related Files

For each target file, auto-detect and read related files for full context. Use language-appropriate parsing strategies:

#### Import Dependencies (project-internal modules only, skip third-party)

| Language | Parsing Strategy |
|----------|-----------------|
| TypeScript/JavaScript | `import ... from` / `require(...)`, recognize path aliases (`@/`, `~/`, `#/`) |
| Vue | `import` in `<script>` + component references |
| Go | Non-stdlib and non-third-party paths in `import (...)` blocks |
| Rust | `mod` / `use crate::` statements |
| Flutter/Dart | `import 'package:...'` / `import '...'` for project-internal files |

#### Reverse Dependencies
- Grep the project to find which files import/reference the current file (by filename or module path)

#### Sibling / Same-Module Files
- Type definition files (`types.ts`, `interfaces.go`, `types.rs`, etc.)
- Test files (`*_test.go`, `*.test.ts`, `*.spec.ts`, `*_test.rs`, etc.)
- Framework convention files (determined by Step 1, e.g., Next.js `layout.tsx`, Nuxt `*.vue` conventions, etc.)
- Tightly coupled files in the same package/module

**Constraints**:
- Related files are for context only — review comments target the subject file only
- Read at most **8** related files per target file to avoid context overload
- Prioritize direct dependencies and dependents

### Step 5: Execute Review

Two categories: **Project Convention Checks** (from CLAUDE.md) and **Universal Quality Checks** (all projects).

#### A. Project Convention Compliance (only when CLAUDE.md exists)

Check all coding conventions defined in CLAUDE.md item by item, including but not limited to:
- Naming conventions (variables, functions, files, constants)
- Import/reference conventions (path aliases, import order, import style)
- Code organization (file structure, module layout, layered architecture)
- Framework usage rules (component types, state management, data fetching patterns, etc.)
- Styling/UI conventions (CSS approach, component library usage)
- Database/ORM usage rules
- Error handling patterns
- Internationalization conventions
- Any other project-specific rules

**Key**: CLAUDE.md rules have the highest priority. If project conventions conflict with general best practices, project conventions win.

#### B. Universal Quality Checks (all projects)

##### 1. Code Quality
- **Readability**: Are names meaningful? Is the logic clear?
- **Complexity**: Deep nesting, overly long functions, high cyclomatic complexity?
- **Duplication**: Obvious duplicate code that can be reasonably extracted?
- **Error Handling**: Proper handling at system boundaries (user input, external APIs, file IO)?
- **Type Safety**: (statically typed languages) Are types accurate? Unsafe type casts?

##### 2. Security
- **Injection Risks**: SQL injection, XSS, command injection, template injection (language/context dependent)
- **Auth/Authorization**: Do endpoints/routes have proper permission checks?
- **Sensitive Data**: Hardcoded secrets, passwords, tokens, connection strings?
- **Input Validation**: Is user input validated and sanitized at system boundaries?
- **Dependency Security**: Known vulnerable dependency versions?

##### 3. Performance
- **Obvious Issues**: N+1 queries, infinite loop risk, memory leak patterns, unnecessary synchronous blocking
- **Resource Management**: Are DB connections, file handles, goroutines/threads properly released?
- **Caching & Batching**: Obvious repeated computations or requests that could be optimized?

##### 4. Maintainability
- **Separation of Concerns**: Is business logic, data access, and presentation properly separated?
- **Over-Engineering**: Over-abstraction for hypothetical requirements? Unnecessary design patterns?
- **Consistency**: Is the code style consistent with the rest of the project?

##### 5. Language/Framework-Specific Checks

Auto-activate checks based on the tech stack detected in Step 1:

**Go**:
- Errors properly handled (no `_ = err`)
- Goroutine leak risks
- Context usage for timeouts and cancellation
- Interface design (small interface principle)
- Concurrency safety (mutex for shared state)

**React/Next.js**:
- Hooks rules (deps array, conditional calls)
- Unnecessary re-renders
- Server/Client Component boundaries
- Unnecessary useEffect usage

**Vue**:
- Correct reactivity usage (ref/reactive/computed)
- Component communication patterns (props/emit/provide-inject)
- Composition API patterns
- Lifecycle hook usage

**Rust**:
- Ownership and borrowing
- Safe unwrap/expect usage
- Result/Option error handling patterns
- Necessity and safety of unsafe blocks

**Flutter/Dart**:
- Correct widget lifecycle (dispose, initState)
- State management patterns (Provider/Riverpod/Bloc)
- BuildContext usage across async gaps
- Const constructors for performance

*These are examples — actual checks are flexibly selected based on the detected tech stack.*

### Step 6: Output Review Results

**IMPORTANT: All review output MUST be written in Chinese (zh-cn).** This includes issue titles, descriptions, suggestions, summaries, and all explanatory text. Only code snippets, file paths, and technical identifiers remain in their original form.

```markdown
# 代码审查报告

## 项目信息
- **技术栈**: [自动检测的语言、框架、工具]
- **项目规范**: [CLAUDE.md ✅ 已加载 / ❌ 未找到]

## 概要
- **审查范围**: [文件列表或变更描述]
- **严重程度统计**: 🔴 严重 x | 🟡 建议 x | 🟢 良好 x

---

## 文件: `<file path>`

### 关联文件
> 读取了以下关联文件作为上下文: [file list]

### 🔴 严重问题（必须修复）
> 功能缺陷、安全漏洞、数据风险、严重规范违规

- **[问题标题]** (line xx-xx)
  - 问题: [具体描述，引用代码片段]
  - 原因: [为什么这是一个问题]
  - 建议: [可操作的修复方案及代码示例]

### 🟡 改进建议（推荐修复）
> 代码质量、性能优化、轻微规范偏差、可读性改进

- **[问题标题]** (line xx-xx)
  - 问题: [具体描述]
  - 建议: [改进方案]

### 🟢 良好实践
> 值得称赞的代码亮点

- [描述]

---

## 总结与建议
[整体评估，按优先级排列的修复建议]
```

## Severity Definitions

| Level      | Meaning                                      | Examples                                                                         |
|------------|----------------------------------------------|----------------------------------------------------------------------------------|
| 🔴 Critical | Must fix, impacts functionality/security/data | Security vulnerabilities, logic errors, data loss risk, core convention violations |
| 🟡 Suggestion | Recommended fix, impacts quality/maintenance | Style inconsistency, performance optimization, minor convention deviations        |
| 🟢 Good    | Noteworthy practices                         | Excellent error handling, clean abstractions, good test coverage                  |

## Review Principles

- **Specific**: Point to line numbers and code snippets, provide actionable fixes with code examples
- **Pragmatic**: No vague comments (e.g., "needs optimization") — state exactly where, why, and how to fix
- **Focused**: For diff reviews, focus on changed code; for file reviews, assess overall quality
- **Respect Project Conventions**: CLAUDE.md rules have highest priority, even if they differ from personal preference
- **Stay On-Topic**: Don't raise unrelated improvements (unless security risks are involved)
- **Positive Feedback**: Highlight good code too, don't just find faults
- **Language**: All review output text MUST be in Chinese (zh-cn). Only code snippets, file paths, and technical identifiers remain in their original form.
