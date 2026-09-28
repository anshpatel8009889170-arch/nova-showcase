# 🎙️ Walkthrough Demo: Voice Command & Intent Flow

This walkthrough documents how a natural speech prompt is ingested, verified, routed, and executed through NOVA's architecture without leaking audio or executing unauthorized system actions.

---

## 1. Scenario: Desktop Application Launch

```
User Prompt: "Nova, launch Visual Studio Code in my workspace."
```

---

## 2. Step-by-Step Architectural Trace

### Step 1: Acoustic Ingestion & Wake-Word Detection
* **Component**: `audio/listening_engine.py`
* **Signal**: 16,000 Hz 16-bit mono PCM.
* **Observation**: RMS energy exceeds sliding noise threshold ($E > 0.042$). Wake-word `"Nova"` matched with acoustic confidence $0.94$.

### Step 2: Speaker Biometric Verification
* **Component**: `core/speaker_verifier.py`
* **Analysis**: Extracted spectral centroid, ZCR, and MFCC feature vector compared against enrolled user acoustic profile.
* **Result**: Euclidean distance $d = 0.11$ (Well within authorized threshold $d \le 0.18$). Speaker verified.

### Step 3: Capability Routing
* **Component**: `core/capability_router.py`
* **Classification**: Matched against Dimension 1: `System Automation & Workspace Controls`.
* **Execution Brain Selected**: **Local Fast-Path Brain** (Zero cloud latency, 100% private).

### Step 4: RBAC Authority Check
* **Component**: `core/auth_manager.py`
* **Verification**: Checks role permissions for `tools.system_power_tool.launch_app`.
* **Decision**: **PERMITTED** (Role: `admin_owner`, Session valid).

### Step 5: Execution & Feedback
* **Action**: Dispatches application launcher with path sanitization.
* **Response**: Synthetic speech acknowledgment via local TTS:
  > *"Visual Studio Code launched in your workspace."*
* **Telemetry**: Event committed to local JSONL audit matrix with timestamp and exit code 0.
