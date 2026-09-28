# 📋 NOVA Technical Subsystem Specifications

This document catalogs the engineering specifications, protocols, and performance metrics across **NOVA** subsystems.

---

## 1. Multimodal Perception Subsystems

### Continuous Voice Ingestion (`audio/`)
* **Audio Sampling Rate**: 16,000 Hz, 16-bit mono PCM.
* **Energy Normalization**: Real-time sliding window RMS calculation for dynamic ambient noise floor suppression.
* **Acoustic Speaker Verification**:
  - Distance Metric: Cosine and Euclidean spectral distance ($\text{threshold} \le 0.18$).
  - Features: Root-Mean-Square (RMS), Zero-Crossing Rate (ZCR), MFCC vectors.
  - Fail-Safe: Automated fallback to biometric PIN / Iris verification when acoustic confidence drops below 85%.

### Computer Vision & Screen Ingestion (`intelligence/`)
* **Multi-Monitor Screen Capture**: Zero-copy DirectX / GDI surface grabbing at up to 30 FPS.
* **Region-of-Interest (ROI) Cropping**: Dynamic window bounding box extraction for focused LLM visual reasoning.
* **Visual Privacy Masking**: Automated blurred redaction of password fields and credential boxes before visual processing.

---

## 2. Agentic Tooling & System Interfaces (`tools/`)

| Tool Module | Interface Functions | Operational Contract |
| :--- | :--- | :--- |
| **`CodeRunnerTool`** | `run_python_code()`, `run_cpp_code()`, `run_pytest()` | Executes multi-language code inside the Docker sandbox with stream capture. |
| **`PatchTool`** | `patch_file()`, `generate_diff()`, `search_in_files()` | Line-range atomic file replacement with automated unified diff rollback. |
| **`ProjectIndexer`** | `build_ast_map()`, `search_symbol()` | AST-based syntax tree parsing extracting classes, functions, and cross-file dependencies. |
| **`SecretSanitizer`** | `sanitize_text()`, `scrub_environment()` | Regex entropy scanner neutralizing API keys, JWTs, and private tokens. |

---

## 3. Storage, Database & Schema Migrations (`memory/`, `migrations/`)

* **Relational Store**: PostgreSQL (Production) / SQLite (Local Fast-Path).
* **Schema Management**: Managed via **Alembic** migration chains (`migrations/versions/`).
* **Session Lifecycle**: 5-minute inactivity auto-lock with encrypted key invalidation.
* **Audit Telemetry**: Immutable JSONL execution records with SHA-256 verification hashes.

---

## 4. Hardware Verification Benchmarks

* **Hardware Rig**: Lenovo LOQ Laptop
* **Processor**: Intel Core i7 14th Gen 14700HX (20 cores, 28 threads)
* **GPU**: NVIDIA GeForce RTX 5050 Laptop GPU
* **Thermal Performance**: GPU baseline idle at $41^\circ\text{C}$; zero thermal throttling under heavy Qwen inference loops.
* **Test Suite Performance**: 725+ tests execute in $< 20\text{ seconds}$ on local hardware.
