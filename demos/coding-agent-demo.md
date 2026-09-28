# 🛠️ Walkthrough Demo: Agentic Coding & Self-Repair

This walkthrough illustrates NOVA's **Agentic Self-Repair Loop** in action: analyzing a bug, applying a surgical minimal patch, running tests in an isolated sandbox, extracting a failure, and self-healing.

---

## 1. Scenario: Resolving a Division-by-Zero Edge Case

```
Task: "Fix edge-case division by zero in calculate_ratio() without breaking existing tests."
```

---

## 2. Step-by-Step Execution Trace

### Step 1: AST Codebase Inspection
* **Component**: `tools/project_indexer.py`
* **Action**: Analyzes repository AST syntax trees. Locates function `calculate_ratio` in `utils/math_helper.py` (Lines 42–56).
* **Target Scoping**: Identifies that only lines 46–49 require modification; remaining 120 lines in the file are marked **Immutable**.

### Step 2: Minimal Patch Generation
* **Component**: `tools/patch_tool.py`
* **Patch Strategy**: Generates a targeted replacement chunk:
  ```diff
  - return numerator / denominator
  + if denominator == 0:
  +     return 0.0
  + return numerator / denominator
  ```
* **Unified Diff Validation**: Verified that zero comments, imports, or adjacent functions are displaced.

### Step 3: Isolated Sandbox Test Run #1 (Simulated Regression)
* **Component**: `core/sandbox.py` (Docker Worker)
* **Execution**: Runs `pytest tests/test_math.py` inside ephemeral container.
* **Result**: **FAILED (Exit Code 1)**
* **Extracted Traceback**:
  ```text
  TypeError: unsupported operand type(s) for /: 'NoneType' and 'int'
  ```

### Step 4: Autonomous Error Extraction & Repair
* **Component**: `core/repair_engine.py`
* **Analysis**: Parser detects that `numerator` can also be `None`, which was not guarded.
* **Corrective Patch**: Automatically generates refined patch guarding both `None` and `0`:
  ```python
  if not denominator or numerator is None:
      return 0.0
  return float(numerator) / float(denominator)
  ```

### Step 5: Sandbox Retest Run #2
* **Execution**: Retests inside isolated Docker container.
* **Result**: **PASSED (14/14 tests green, Exit Code 0)**.
* **Evidence Commitment**: Diff, test stdout, and verification hash recorded in evidence audit ledger.
