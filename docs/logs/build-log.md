Build Log – Local AI Assistant (Phase 1)

This document records the implementation of Phase 1 of the Local AI Assistant project.
It focuses on what was done, what issues were encountered, and how decisions were made.

This is an execution log, not a design document.


Goal

Establish a local, GPU-backed LLM runtime on Windows with explicit lifecycle control.
The system should be predictable, manually operated, and free of background services.


Initial Environment Verification

Host system:
- Windows 11 desktop
- AMD Ryzen CPU
- 32 GB RAM
- NVIDIA RTX 3060 (12 GB VRAM)

Before installing any AI runtime, GPU availability was verified using nvidia-smi.
The GPU was detected correctly, drivers were installed, and the device was idle.

This confirmed the system was suitable for local inference.


Runtime Selection

Ollama was selected as the initial runtime because:
- it supports Windows-native execution
- it provides CUDA acceleration
- it allows manual operation via CLI
- it keeps model management simple

No WSL-based runtime was used.


Installation Notes (Windows-Specific)

Initial attempts to install Ollama over SSH failed silently.
This was traced to Windows UAC behavior: GUI installers requiring elevation cannot be launched remotely over SSH.

The installer was instead executed locally on the desktop, which completed successfully.

Ollama was installed per-user at:
C:\Users\richb\AppData\Local\Programs\Ollama\ollama.exe

This location was accepted, as the binary itself is not considered persistent state.


Model Storage Redirection

The environment variable OLLAMA_MODELS was set to redirect model storage off the OS drive.

Target directory:
G:\ai\models

After restarting shells, the variable was confirmed to be active.


Model Download and Placement

An initial model was selected:
- llama3.1 8B instruct
- quantization: Q4_K_M

The model was downloaded using the Ollama CLI.

At first, model files appeared under the default user directory.
They were manually moved to G:\ai\models to enforce the storage design.

After restarting the runtime, Ollama correctly recognized the model in the new location.


Runtime Lifecycle Behavior

The Ollama server was started manually using:
ollama serve

During early testing, the server appeared to respawn after being terminated.
Investigation showed that a GUI helper shortcut was configured to auto-start Ollama on login.

This startup entry was identified and disabled to preserve manual lifecycle control.

After this change, the runtime behaved predictably:
- start only when explicitly launched
- stop cleanly with Ctrl+C
- no background listeners remained active


GPU-Backed Inference Verification

With the server running, the model was launched using:
ollama run llama3.1:8b-instruct-q4_K_M

GPU usage was monitored with:
nvidia-smi

VRAM usage increased to approximately 6–7 GB, and the Ollama process was visible as a compute workload.
This confirmed that inference was running on the GPU rather than CPU fallback.


Clean Shutdown

The runtime was terminated manually using Ctrl+C on the ollama serve process.

After shutdown:
- no process was listening on port 11434
- GPU memory returned to idle levels
- no services or startup processes remained active

This confirmed that the system met the Phase 1 lifecycle goals.


Phase 1 Outcome

Phase 1 successfully established:
- a local LLM runtime on Windows
- GPU-backed inference via CUDA
- filesystem-controlled model storage
- explicit manual start and stop behavior

No persistent memory was introduced in this phase.

