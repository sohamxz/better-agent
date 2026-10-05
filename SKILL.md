---
name: better-agent
description: Enforces ground-truth verification against installed packages, test-driven defect reproduction, surgical edits, and hierarchical codebase inspection. Use when writing code, fixing bugs, refactoring, or reviewing changes.
---

# Better Agent

A set of rules for AI coding agents to verify code against installed packages, reproduce bugs before fixing them, make minimal changes, and avoid modifying tests to force passes.

Works with Antigravity, Claude Code, Cursor, Gemini CLI, Codex, and OpenHands.

## 1. Verify against installed packages and documentation

- Do not write code, options, or configurations from memory. APIs change across versions.
- Check installed packages and local files before writing code:
  1. Dependencies: Check package.json, lockfiles, and environment files. Do not import, require, or dynamically resolve packages that are missing from the project manifest. Do not rely on monorepo root hoisting or transitive packages.
  2. Types: Check exported types in node_modules or local definition files. Import public module entry points. Do not import private subpaths like /dist/internal/ or /src/.
  3. Documentation: Check local reference files or official docs for third-party libraries.
- Do not install new packages, add devDependencies, upgrade versions, or run dependency modification commands (such as npm install, npm update, npm audit fix, pip install, or cargo add) without explicit user permission.
- Do not manually edit package manifests (such as package.json or Cargo.toml) to add dependencies or change version ranges without user approval.
- Do not invoke external runtimes or download unmanaged scripts or binaries via curl, wget, npx, bunx, or dlx to bypass dependency restrictions.
- Do not write placeholder code, stub functions, fake data, or empty wrappers in production paths. Do not leave TODO comments, ellipses, or truncated blocks in place of complete implementation.
- Do not add test-specific conditional branches, test flags, environment toggles (such as checking process.env.NODE_ENV or process.env.CI), or mock branches inside production code to bypass actual logic.
- Do not inject placeholder credentials, synthetic tokens, dummy secrets, or unapproved environment files into the workspace. Validate required variables against existing .env.example files.
- Do not mutate global prototypes or attach synthetic properties to runtime globals (such as globalThis, window, or process) to patch missing functionality.

## 2. Inspect codebases hierarchically

- Read directory structure and exported types before reading implementation files. When inspecting large files, read specific line ranges.
- Use Language Server or symbol navigation tools when available to locate definitions and references directly. Do not rely solely on text pattern searches.
- Always exclude node_modules, .git, dist, build, and coverage directories when searching text or listing files.
- Keep documentation notes in a references/ directory for external dependencies, third-party APIs, and non-trivial upstream behaviors:
  - references/docs/<library>.md for library usage and version notes.
  - references/apis/<service>.md for endpoint payloads and headers.
  - references/github/<repo>.md for upstream patterns and workarounds.
- Reference Freshness: Record the documented package version in each reference file header. When package.json or lockfile versions change, update the corresponding reference file before generating code.
- Check local references/ files before searching the web.
- Do not read minified assets, lockfiles, generated source maps, or build outputs into context. Inspect source manifests and code files directly.

## 3. Do not weaken tests

- Do not edit tests to make failing checks pass. Fix the application code to satisfy the test contract.
- Only update tests when the user explicitly changes the feature requirements.
- Do not bypass failures: do not skip tests using test.skip, it.skip, xit, or comments.
- Do not delete or comment out assertion statements inside test blocks. Leaving empty test blocks without assertions is forbidden.
- Do not insert early return statements or conditional guards inside test functions to bypass failing assertions.
- Do not relax test assertions: do not replace exact matchers with partial matchers (such as toMatchObject or objectContaining), widen numeric tolerances, or broaden schema checks to force a pass.
- Do not update snapshot files (such as Jest or Vitest snapshots via -u) to match failing output without explicit user instruction.
- Do not modify test fixtures, setup blocks (such as beforeEach), teardown blocks, or test input data to make tests pass. The entire test harness and input dataset are part of the test contract.
- Do not increase timeouts, disable concurrency, or alter test runner configuration to mask timing defects.
- Do not rerun failing tests repeatedly to exploit intermittent timing flakiness. A test that fails once indicates a defect that must be resolved deterministically.
- Do not introduce artificial mocks into existing integration or end-to-end tests to force a pass.
- Do not suppress errors with as any, @ts-ignore, @ts-expect-error, @ts-nocheck, /* eslint-disable */, # type: ignore, # noqa, or empty catch blocks. Fix the data flow.

## 4. Use the simplest working solution

- If a requirement is speculative, do not build it.
- Reuse existing functions and patterns in the repository. Search existing utility files before writing new helpers.
- Use standard library methods before adding custom helpers.
- Use native platform features (HTML elements, CSS, database constraints) before external libraries.
- Use already-installed dependencies. Do not install new packages for simple tasks.
- Avoid unnecessary abstractions like single-implementation interfaces, premature factory functions, or speculative plugin hooks.
- Avoid file sprawl: do not create new files for logic that belongs in an existing cohesive module.
- Conform to the existing architecture and coding paradigms of the file. Do not rewrite functional code into object-oriented patterns or object-oriented code into functional patterns.
- When modifying a function, inspect every call site across the codebase using symbol references or search. Fix the defect at the source, and verify all callers remain compatible. Do not add loose optional parameters to mask breaking signature changes.

## 5. Keep diffs small

- Edit only the lines needed for the task.
- Keep existing comments, type definitions, formatting, indentation, and line endings in untouched code.
- Do not reformat or clean up unrelated code. Do not run repository-wide formatters that alter files outside the task scope.
- Do not refactor adjacent functions or fix unrelated issues discovered during execution. Report them separately if noteworthy, but do not include them in the diff.
- Do not delete existing files or rewrite entire files from scratch when updating existing modules. Preserve existing architecture and commit history.
- Do not remove or rename exported symbols that external callers depend on. Preserve public module interfaces.
- Do not modify configuration files, build scripts, or CI workflows unless the task specifically requests configuration changes.

## 6. Reproduce bugs before fixing them

- Write a test or run a command that reproduces the failure before changing code.
- Confirm that the reproduction fails with the reported symptom before applying the fix. The failure must match the specific defect and error signature reported by the user, not a setup or syntax error. A test that passes before the fix is invalid because it does not trigger the defect.
- The reproduction must exercise the actual defect path. Mocking the bugged component in the reproduction test is invalid.
- Apply the fix and verify that the reproduction test passes.
- Retain the reproduction test in the test suite as a permanent regression test.
- Run the full test suite to check for regressions in other files.

## 7. Use modular skills

- Read domain skill instructions only when working in that domain.
- Update skill files when library versions change or project patterns change.
- Write new skill files when a useful workflow repeats across tasks.
- Invariant precedence: when domain skills conflict with core safety rules (such as test weakening or unapproved dependencies), core invariants take absolute precedence.

## 8. Run verification commands

- Run the project compiler (such as tsc or cargo check), linter, and test suite to verify changes before marking work complete.
- Verify the command exit code is zero. Output text indicating completion is not proof of success if the exit code failed.
- Confirm the test runner executed test cases. A zero exit code from a run where zero tests were matched or all tests were skipped is invalid.
- Do not extrapolate success from partial or truncated command output. If output is truncated, re-run with explicit filters to prove zero failures.
- Never state that code compiles or passes tests without executing the command and observing the output. Evidence must precede every claim of success.
- Do not skip verification gates. Passing tests alone does not satisfy verification if compiler or linter checks were skipped.
- Treat all compiler and linter warnings in modified files as errors. Do not leave new warnings in modified files.
- Do not use selective test filters to bypass failing suites during final verification. Run the full, standard test suite command configured in package.json, Makefile, or pyproject.toml.
- Do not run test suites with flags that mask failures, such as --passWithNoTests.
- Do not modify compiler configs (such as tsconfig.json), linter rules, or build flags to disable strict checks or hide warnings.
- Do not bypass git hooks with --no-verify.
- If your change causes a compiler or test error in any other file in the repository, you must resolve that error. A failure anywhere caused by your changes is a blocking defect.
- For UI changes, check rendered output across themes and screen sizes.
- Circuit breaker: if a fix fails verification twice, stop trying small variations. Revert changes completely with git checkout or git restore, and re-read the error and documentation. Do not retain unverified intermediate edits.

## 9. Track multi-step work

- Work on one task at a time.
- For multi-step tasks, keep a written checklist. Mark items complete only after their verification commands succeed with exit code zero. Never check off items based on unverified code edits alone.
- Keep tool outputs concise.

## 10. Workspace isolation

- For speculative refactors or major structural changes, use an isolated git worktree (git worktree add) or temporary branch to keep the primary working tree clean.
- Verify changes completely in the isolated workspace before merging. Remove the worktree when finished.
- Clean up all scratch files, temporary reproduction scripts, and worktrees before declaring work complete. The working tree must be clean.
