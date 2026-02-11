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

---

### 📝 Development Memo (Kick-off Plan)
* **Start Point**: Build a prototype that compares two `ast.File` objects and identifies added/removed functions.
* **Identity Logic**: If function bodies are structurally identical despite name changes, mark as `RENAME`.
* **Experience**: Leverage the deep AST knowledge gained from `gomarklint` to handle complex Go syntax edge cases.
