# 🛡️ 04. Sandbox Security & Governance

Security in NOVA is not an add-on; it is an architectural foundation. When an autonomous AI generates and runs code, it must be contained within strict, fail-closed boundaries.

---

## 1. Threat Model & Sandboxing Principles

```
                         NOVA ENGINE
                              │
                              ▼
                        Sandbox Policy
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
Command Allowlist    Filesystem Isolation    Network Lockdown
        ▼                     ▼                     ▼
Environment Scrub       Timeout Limits        Process Cleanup
        └─────────────────────┬─────────────────────┘
                              │
                              ▼
                     Fail-Closed Gate
                              │
                              ▼
                    Isolated Worker Container
                              │
                              ▼
                    Compile & Run Tests
```

---

## 2. Comprehensive Security Layers

| Security Layer | Operational Responsibility | Failure Behavior |
| :--- | :--- | :--- |
| **RBAC Authority** | Governs *who* and *which role* can perform a system action. | Rejects with `403 Forbidden` and logs security incident. |
| **Sandbox Isolation** | Governs *where* and *under what constraints* code can execute. | Spawns isolated Docker container; fails closed if unavailable. |
| **Path Policy** | Enforces directory boundaries (`Path.resolve`) to stop path traversal (`../`). | Aborts with `PathPolicyViolationError`. |
| **Command Allowlist** | Restricts executable binaries to approved compilers and test runners. | Blocks unauthorized shell commands immediately. |
| **Secret Sanitizer** | Regex scanner scrubbing API keys, tokens, and passwords from outputs. | Redacts secrets with `[REDACTED_SECRET]` before output commit. |
| **SSRF Guard** | Restricts outbound HTTP requests to prevent local network probing. | Blocks private IP ranges (127.0.0.1, 10.0.0.0/8, 192.168.0.0/16). |
| **Fail-Closed Boundary** | Halts execution whenever an environment's security state is uncertain. | Throws `SandboxIsolationUnavailableError` rather than running unprotected. |
| **Audit Evidence** | Records immutable cryptographic hashes of all executions. | Commits SHA-256 audit entry to evidence ledger. |

---

## 3. Docker Container Hardening Specifications

When dynamic code is dispatched to the Docker sandbox worker, it executes under the following strict flags:

```bash
docker run --rm \
  --network none \
  --read-only \
  --user 10001:10001 \
  --cap-drop ALL \
  --security-opt no-new-privileges:true \
  --memory=512m \
  --cpus=1.0 \
  --pids-limit=64 \
  -v /tmp/nova_job_123:/workspace:rw \
  nova-sandbox-worker:latest
```

### Key Isolation Guarantees:
* **Air-Gapped Network (`--network none`)**: Outbound reverse shells and data exfiltration sockets are completely blocked at the kernel network stack.
* **Ephemeral Workspace Mount (`-v ...:/workspace:rw`)**: Only a temporary, job-specific directory is mounted. Host drives (`C:\`, `D:\`) are unmounted and invisible.
* **Immutable Rootfs (`--read-only`)**: System packages and container libraries cannot be modified or replaced by running code.
* **Least-Privilege User (`--user 10001:10001` & `--cap-drop ALL`)**: Strips all Linux root capabilities and prevents setuid escalation.

---

## 4. Fallback Architecture: Windows Job Objects

In environments where Docker is temporarily unavailable and execution is explicitly authorized:
* Child processes run jailed inside a **Windows Job Object**.
* Memory quotas and strict deadlines (e.g. 15s) are enforced at the OS kernel level.
* On timeout or abort, the entire process tree is terminated simultaneously, eliminating orphaned or zombie processes.

---

[Next: 05. Multimodal System ➔](05-multimodal-system.md)
