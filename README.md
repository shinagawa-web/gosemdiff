# gosemdiff

**Go Semantic Diff: Understand the "Logic," not just the "Lines."**

## 🤔 Why gosemdiff?

Traditional `git diff` is "dumb"—it sees text, not code. 

When you refactor a Go project, you often encounter "Diff Noise":
- A 100-line function moved to another file is reported as 100 deletions and 100 insertions.
- Renaming a widely used struct field creates a massive, unreadable diff.
- Formatting changes or comment updates bury actual logic modifications.

In the era of AI-generated code and large-scale refactoring, we need a tool that speaks the language of the **AST (Abstract Syntax Tree)**. `gosemdiff` filters out the noise to reveal the *true* evolution of your logic, ensuring that every code review focuses on what actually matters.


## 🚀 The Vision

Traditional `git diff` is "dumb"—it sees text, not code. When you move a 100-line function to another file, `git` reports 100 deletions and 100 insertions. In large-scale refactoring, the real logic changes get lost in the noise.

`gosemdiff` aims to eliminate this noise, proving that your refactor is safe and highlighting the 1% of code that actually requires a human reviewer's brain.

## 🌟 Key Features (The "Grand Plan")

### 1. Semantic Change Classification
Automatically labels diffs so reviewers know where to focus:
- **`MOVE`**: Functions/Structs moved between files with zero logic changes.
- **`RENAME`**: Symbols renamed consistently across the package.
- **`REFACTOR`**: Equivalent code transformations (e.g., `a = a + 1` → `a++`).
- **`LOGIC`**: **[Priority]** Structural changes to control flow, math, or state.

### 2. Logic Integrity Proof
Mathematical certainty for refactors. By comparing normalized AST hashes, `gosemdiff` can certify that a code change has **zero impact** on execution logic. 

### 3. Breaking Change Guard
Integrated CI tool to detect modifications in exported API signatures. Instantly flag unexpected changes to your library's public contract.

### 4. Noise Cancellation
Zero-distraction mode. Ignore comments, white-spaces, import reordering, and formatting changes. If the compiled behavior doesn't change, the diff doesn't show it.

## 🛠 Technical Roadmap (Note to Future Self)

- **AST Fingerprinting**: Implement a hashing mechanism for `ast.FuncDecl` and `ast.TypeSpec` that is name-agnostic to detect renames.
- **Structural Comparison**: Use a custom tree-walking algorithm (via `go/ast`) to identify "Equivalent Structures."
- **High-Performance CLI**: Inspired by the speed of modern tools like `oxlint`, built with Go's efficiency and `charmbracelet/lipgloss` for a world-class terminal UI.

## 🌈 Why this matters
In an era where AI generates massive Pull Requests, humans need a "Truth Machine." `gosemdiff` is the last line of defense—ensuring that we understand *exactly* how our logic is evolving, one AST node at a time.

## 🗺 Roadmap to v1.0.0

`gosemdiff` evolves from a simple CLI tool to an intelligent CI gatekeeper.

### 🟢 Phase 1: Foundations (The "Identity" Phase) - v0.1.0
*Goal: Establish the basic ability to identify "equivalent" code blocks.*
- [ ] **Function Inventory**: Extract all top-level functions and methods from two files and map them by signature.
- [ ] **Structural Hashing**: Generate "normalized AST hashes" by ignoring comments, whitespaces, and position.
- [ ] **Simple Move Detection**: Identify functions that vanished from File A and appeared in File B with identical hashes.
- [ ] **Simple Rename Detection**: Detect functions where the body hash is identical but the identifier has changed.

### 🟡 Phase 2: Refinement (The "Abstract" Phase) - v0.3.0
*Goal: Deepen the analysis to see through superficial changes.*
- [ ] **Identifier Anonymization**: Abstract local variable names during hashing so that logic remains "identical" even if internal names are refactored.
- [ ] **Diff Visualization**: Build a beautiful terminal UI (powered by `lipgloss`) that collapses `MOVE`/`RENAME` labels and highlights `LOGIC` changes.
- [ ] **Package-level Scope**: Expand analysis from single files to entire Go packages and directories.

### 🟠 Phase 3: Quality & Safety (The "Guardian" Phase) - v0.6.0
*Goal: Ensure the safety of refactoring and API stability.*
- [ ] **Breaking Change Guard**: Implement strict checks for exported API signatures to prevent accidental breaking changes.
- [ ] **Test-Gap Analysis**: Detect if a `LOGIC CHANGE` has corresponding tests (e.g., `FuncA` -> `TestFuncA`). Warn if tests are missing for modified logic.
- [ ] **Constant & Type Tracking**: Detect structural shifts in `struct` fields or `const` values.

### 🔵 Phase 4: Integration (The "Automation" Phase) - v0.9.0
*Goal: Integrate with the development workflow.*
- [ ] **GitHub Actions Integration**: Automated PR summaries: "This PR is 90% refactoring. Logic changes detected in 2/10 files."
- [ ] **CI Enforcement**: Allow CI to fail if unauthorized breaking changes or untested logic changes are detected.
- [ ] **Performance Optimization**: Implement concurrent parsing and caching for large-scale repositories.

### 🔴 Phase 5: Production Ready - v1.0.0
*Goal: Stability and reliability.*
- [ ] **Comprehensive Test Suite**: Verify correctness against historical commits of the Go standard library (e.g., `net/http`).
- [ ] **Stable CLI Interface**: Finalize flags and configurations (e.g., `--ignore-comments`, `--strict-types`).

## 🎯 The Ultimate Goal: CI-First Code Review

The final milestone for `gosemdiff` is an automated **GitHub Action** "Pre-Reviewer."

### The Workflow:
1. **CI Execution**: On every PR, `gosemdiff` analyzes structural changes between `base` and `head`.
2. **Automated Summary**: It posts a structured report:
   > 🤖 **gosemdiff Analysis Summary:**
   > - ✅ **Refactor-Only**: 12 files (Moved functions, renamed variables, no logic changes).
   > - ⚠️ **Logic Changes**: 2 files (`internal/engine/core.go`, `api/handler.go`).
   > - 🔍 **Test Coverage**: No tests found for the logic change in `api/handler.go`!
   > - 🚫 **Breaking Changes**: None.
3. **Reviewer Confidence**: Reviewers focus only on the 2 files with logic changes, saving hours of manual labor.

---

### 📝 Note to Future Self: Where to Start?

When you return to this repo, follow these steps to get the momentum back:

1. Create `examples/old.go` and `examples/new.go`.
2. In `new.go`, shuffle the function order and rename one function, but keep the logic identical.
3. Start by implementing a recursive `ast.Inspect` loop to compare the `ast.BlockStmt` of two functions.
