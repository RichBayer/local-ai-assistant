Local AI Assistant



This project documents the design and early implementation of a local, GPU-accelerated AI assistant running entirely on personal hardware.



The goal is not to build an autonomous system or a replacement for cloud AI services. The goal is to understand how a local AI system actually works under the hood, how its state is managed, and how to keep control over what it does and does not do.



This repository is part learning exercise, part long-term personal project, and part technical portfolio.





Why this project exists



Most AI tools today are cloud-hosted and opaque. They work well, but they hide important details such as:



- where data is stored

- what state is retained

- when models are running

- how resources are used



This project explores a different approach:



- run the model locally

- keep state on the filesystem

- start and stop the AI manually

- avoid background services and silent behavior



The intent is reliability and understanding, not convenience.





High-level design goals



The system is designed around a few simple principles:



- local-first execution

- explicit, human-approved memory

- filesystem as the source of truth

- manual lifecycle control

- replaceable components



Nothing should happen automatically without the operator knowing about it.





Current environment (Phase 1)



The current implementation runs on a single Windows desktop system:



- Windows 11

- AMD Ryzen CPU

- 32 GB RAM

- NVIDIA RTX 3060 (12 GB VRAM)

- GPU acceleration via CUDA



Access is performed locally or over SSH from a secondary laptop.





Storage approach



All AI-related data lives under a single directory on a non-OS drive:



G:\ai



This includes:

- model files

- runtime configuration

- future memory files

- project notes and backups



The Windows OS drive does not store AI models or AI memory. This makes the system easier to back up, migrate, or rebuild later.





Runtime approach



The AI runtime is intentionally operated like an appliance:



- Windows-native execution

- GPU acceleration enabled

- manual start and stop

- no required background services



When the runtime is not needed, it is not running.



This keeps resource usage predictable and avoids hidden state.





Memory (intentionally limited for now)



Memory is treated as curated state, not raw chat history.



There is currently no persistent memory in use. The design for memory is documented, but implementation is deferred until later phases.



Any future memory will be:

- human-inspectable

- human-editable

- written only with explicit approval





What this project is not



This project is not:



- an autonomous agent

- a system that modifies the OS on its own

- a background service that runs all the time

- a cloud-dependent AI setup

- a large model collection experiment



Scope is intentionally limited to keep behavior understandable.





Project status



Phase 1 is complete.



Completed work includes:

- local LLM runtime setup

- GPU-backed inference verification

- filesystem-based model storage

- manual lifecycle control



Future phases will focus on:

- explicit memory design

- interaction discipline

- documentation and refinement





Repository structure



- README.md – project overview

- ARCHITECTURE_SNAPSHOT.md – point-in-time design snapshot

- docs/ – expanded documentation

- logs/ – build and execution notes



