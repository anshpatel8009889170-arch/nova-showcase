# 📝 Changelog & Engineering Milestones

All notable architectural milestones and version advancements of the **NOVA** project are documented here.

---

## [0.4.0] — September 2026 (Enterprise Hardening & Public Showcase)
### Added
* **Fail-Closed Docker Sandboxing**: Ephemeral `/workspace` mounts, air-gapped network isolation (`--network none`), read-only rootfs, and non-root execution.
* **Automated Test Suite Expansion**: Expanded automated Pytest coverage to **726 passed tests (2 skipped, 0 failed)** with strict CI evidence auditing.
* **7-Layer Canonical Architecture**: Formalized the full end-to-end pipeline from acoustic ingestion to immutable evidence commitment.
* **Public Architecture Showcase**: Published open architectural documentation, walkthrough demos, and verification proofs.

---

## [0.3.0] — August 2026 (Multimodal & Security Infrastructure)
### Added
* **Biometric Acoustic Gate**: Speaker verification based on RMS energy, Zero-Crossing Rate (ZCR), and spectral centroid distance metrics.
* **Relational Schema Migrations**: Integrated Alembic forward/backward database migrations for user profiles and audit ledgers.
* **AST Project Indexing & Patch Engine**: Implemented line-range targeted patching and syntax tree symbol inspection.

---

## [0.2.0] — July 2026 (Hardware Scaling & NOVA Rebranding)
### Changed
* **Rebranding to NOVA**: Officially transitioned project identity from experimental "JARVIS" to **NOVA (Next-generation Operations & Virtual Assistant)**.
* **Hardware Migration**: Transitioned development to **Lenovo LOQ** (Intel Core i7 14th Gen + NVIDIA GeForce RTX 5050 Laptop GPU).
* **Dual-Brain Router**: Began scaffolding local 14B QLoRA offloading targeted for local GPU tensor acceleration.

---

## [0.1.0] — May 2026 (The Cloud Vault Baseline)
### Added
* **Initial Cloud Vault**: First safe cloud commit comprising over **7,300 lines of functional Python code**.
* **Modular Codebase**: Restructured flat scripts into standardized `tools/`, `memory/`, and `core/` subsystems.

---

## [0.0.1] — March 2026 (The Inception: "JARVIS")
### Initial
* **First Working Prototype**: Early offline experiments with voice recognition, text-to-speech, and operating system controls on an HP laptop.
