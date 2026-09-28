# 📌 01. Overview & Core Philosophy

## What is NOVA?

**NOVA** is a local-first autonomous multimodal desktop agent designed around controlled orchestration, agentic task planning, sandboxed execution, strict verification, and self-healing workflows.

Unlike typical cloud-dependent AI wrappers that immediately execute arbitrary scripts or send private user context to third-party endpoints, NOVA enforces **enterprise software engineering discipline**:

```
                    USER
                     │
                     ▼
               NOVA BRAIN
                     │
              ┌──────┴──────┐
              ▼             ▼
          PLANNER        ROUTER
              │             │
              └──────┬──────┘
                     ▼
            CAPABILITY LAYER
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
      VOICE        VISION       TOOLS
        │            │            │
        └────────────┼────────────┘
                     ▼
                 SANDBOX
                     │
                     ▼
                VERIFICATION
                     │
                     ▼
                 EVIDENCE
```

---

## Why NOVA? (Design Philosophy)

Most AI assistant projects suffer from the **"Hallucinate & Break"** pattern: an LLM generates a complete script, runs it with host administrator privileges, breaks existing code, or leaks credentials.

NOVA replaces this with six fundamental architectural principles:

### 1. Planning Before Execution
NOVA does not blindly execute tasks. User intent is analyzed, decomposed into a Directed Acyclic Graph (DAG) of atomic operations, and validated against tool manifests before any action is taken.

### 2. Verification Before Acceptance
An action is not complete simply because an LLM returned text. Every task must be backed by concrete proof: passing tests, exit code 0, AST syntax verification, or structured evidence logs.

### 3. Minimal Patching Instead of Unnecessary Rewrites
When fixing a bug or editing code, NOVA avoids rewriting whole files. Rewriting entire files introduces regressions, erases comments, and consumes unnecessary tokens. Instead, NOVA identifies the exact AST target and applies surgical line-range patches.

### 4. Sandboxing Before Code Execution
All dynamic code executes inside isolated environments. If Docker is available, an ephemeral container with a read-only rootfs and no host mounts is instantiated. If the environment cannot guarantee isolation, execution fails closed.

### 5. Capability Routing Instead of One-Model-Does-Everything
A single model should not handle wake-word audio processing, biometric verification, AST parsing, and heavy multi-file reasoning simultaneously. NOVA routes lightweight tasks to local specialized engines and preserves heavy models for complex reasoning.

### 6. Evidence-Backed Completion
Every critical operation produces an immutable telemetry record containing execution timestamps, output sanitization proofs, and verification hashes.

---

[Next: 02. Architecture & Pipeline ➔](02-architecture.md)
