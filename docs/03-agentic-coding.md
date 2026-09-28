# 🛠️ 03. Agentic Coding & Autonomous Repair Loop

> **The Hero Feature of NOVA**: Moving from fragile code generation to resilient, test-verified self-repair.

---

## 1. The Problem with Standard AI Coding

Most current AI coding assistants follow a naive, error-prone workflow:

$$\text{Prompt} \longrightarrow \text{LLM Generates Full File} \longrightarrow \text{Overwrite File} \longrightarrow \text{Hope It Works}$$

### Why this fails in real-world software engineering:
1. **Regression Cascades**: Rewriting an entire 500-line file to fix one function often introduces subtle syntax errors, breaks unrelated functions, or drops existing comments.
2. **Context Blowup**: Transmitting and receiving massive files repeatedly exhausts token limits and increases latency.
3. **No Verification**: If the generated code fails to compile or breaks unit tests, the assistant often loops blindly or gives up.

---

## 2. NOVA's Agentic Self-Repair Pipeline

NOVA treats code modification as a disciplined, closed-loop engineering cycle:

```
                    User Task / Coding Prompt
                               │
                               ▼
                    Inspect Project via AST
                               │
                               ▼
                   Plan File Targets & Scopes
                               │
                               ▼
                    Generate Targeted Patch
                               │
                               ▼
                   Apply Minimal Surgical Patch
                               │
                               ▼
                    Sandbox Container Execution
                               │
                               ▼
                     Run Pytest / Compilers
                               │
                               ▼
                        Error Detected?
                         ↙           ↘
                      YES             NO
                       │               │
                       ▼               ▼
               Capture Traceback   Verified Complete!
                       │
                       ▼
             Autonomous Repair Engine
                       │
                       ▼
                 Retest in Sandbox
```

---

## 3. The Minimal Patching Philosophy

A core rule enforced by NOVA's `PatchTool` is:

> **"If only 4 lines of a function are defective, NOVA touches only those 4 lines."**

### How it works:
* **AST Code Indexing (`ProjectIndexer`)**: Before modifying code, NOVA builds an Abstract Syntax Tree (AST) map of the repository, identifying class declarations, function signatures, and line boundaries.
* **Line-Range Targeting**: Instead of rewriting the file, the agent generates a precise replacement chunk with strict `StartLine` and `EndLine` constraints.
* **Unified Diff Verification**: Generates standard unified diffs (`git diff` format) to verify that unrelated code and comments are completely preserved.

---

## 4. The Autonomous Self-Repair Engine

When generated code or a patch causes a failure during sandbox execution:

1. **Structured Traceback Extraction**: The error stream (`stderr`) is parsed to extract the exact failing filename, line number, exception type, and stack trace.
2. **Localized Context Window**: Only the failing function and immediate surrounding AST context are fed back to the planning engine.
3. **Targeted Correction**: A corrective patch is generated to address the specific root cause (e.g., missing import, type mismatch, edge-case index error).
4. **Retest Verification Loop**: The patch is applied and re-tested inside the isolated sandbox. The cycle terminates only when the test suite passes (Exit Code 0) or the safety retry budget is exhausted.

---

[Next: 04. Sandbox Security & Governance ➔](04-sandbox-security.md)
