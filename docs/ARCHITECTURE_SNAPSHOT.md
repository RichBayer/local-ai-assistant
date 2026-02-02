# Local AI Assistant

This project documents the design and implementation of a local-first,
GPU-accelerated personal AI assistant running on consumer hardware.

The system is intentionally designed to favor:
- explicit state
- manual control
- inspectable storage
- replaceable components

> NOTE:
> This document captures a point-in-time architectural snapshot created during early implementation.
> It is retained for historical context and design continuity.
> Public-facing documentation is provided in README.md and /docs.

The goal is not autonomy, but reliability, predictability, and long-term ownership.
=====================================================================

1. HOST ENVIRONMENT

Primary host:
- Machine: Lenovo Legion desktop
- Operating System: Windows 11 (edition to be confirmed: Home or Pro)
- CPU: AMD Ryzen (exact model not critical at this stage)
- RAM: 32 GB
- GPU: NVIDIA RTX 3060 with 12 GB VRAM
- GPU acceleration via CUDA

Access model:
- SSH over Tailscale
- Key-based authentication only
- User lands in PowerShell, then enters Git Bash (bash)

Secondary device:
- Lenovo Yoga laptop used as SSH client

=====================================================================

2. STORAGE STRATEGY

Authoritative AI root:
- Drive letter: G:
- Root path: G:\ai

Design rules:
- Windows OS drive must not store AI models or AI memory
- Homelab VM storage must remain isolated
- AI data must be simple to back up and migrate
- All AI-related assets live under a single root directory

=====================================================================

3. DIRECTORY LAYOUT (ASCII SAFE)

G:\ai\
  models\                 LLM model files only
  runtime\                AI runtime binaries and configuration
  memory\
    identity.yaml         Long-term personal facts
    preferences.yaml      Assistant behavior preferences
    facts.json            Verified system facts
    scratch\              Ephemeral working memory
  projects\
    local-ai-assistant\
      overview.md
      decisions.md
      constraints.md
      current_state.md
      open_questions.md
  backups\                Offline and archival backups

The filesystem is the authoritative source of truth.
Databases may be added later only as indexes or accelerators.

=====================================================================

4. MEMORY ARCHITECTURE

Memory is curated state, not raw conversation history.

Memory categories:

1. Identity Memory
   - Long-term personal facts
   - Examples: name, location, career direction
   - Stored in identity.yaml
   - Written only with explicit user approval

2. Preference Memory
   - Behavioral preferences for the assistant
   - Examples: CLI-first, avoid GUI, explanation style
   - Stored in preferences.yaml

3. Knowledge Ledger
   - Confirmed factual information about systems
   - Examples: OS version, GPU model, VM platform
   - Stored in facts.json

4. Project Memory
   - Scoped, contextual project knowledge
   - One directory per project
   - Stored as human-readable Markdown files

5. Ephemeral Context
   - Short-term working memory
   - Auto-expiring
   - Stored under memory\scratch

=====================================================================

5. MEMORY WRITE RULES

- The assistant may read all memory from day one
- The assistant may NOT write memory silently
- Memory writes require:
  - Explicit phrasing such as "remember this"
  - OR explicit confirmation from the user
- Proposed memory changes must be reviewable

Memory must always be:
- Inspectable
- Editable by the human operator
- Reversible

=====================================================================

6. RUNTIME STRATEGY (PHASE 1)

- Appliance-style local LLM runtime
- Native Windows execution (no WSL initially)
- GPU acceleration via CUDA
- Manual start only (no background services)
- Single primary model initially

Explicit exclusions in phase 1:
- No automation
- No system modification
- No VM control

=====================================================================

7. MODEL STRATEGY (INITIAL)

- Quantized models only (Q4 or Q5)
- Size range: 7B to 13B parameters
- Focus on reasoning, scripting, planning
- Avoid maintaining a large model collection ("model zoo")

Models are treated as tools, not personalities.

=====================================================================

8. FUTURE EXPANSION (EXPLICITLY DEFERRED)

The architecture is designed to support these later,
but they are intentionally excluded from phase 1.

Memory enhancements:
- SQLite for indexed memory
- Vector database for semantic recall
- Filesystem remains authoritative

Interaction enhancements:
- Local web UI
- Voice interface (optional)

Cloud augmentation (optional):
- AWS Polly for text-to-speech
- AWS Transcribe for speech-to-text
- Amazon Lex-style intent routing

Cloud services are treated as peripherals,
not as the source of intelligence or memory.

=====================================================================

9. NON-GOALS

- Autonomous system changes
- Silent file edits
- Implicit personality inference
- Storing raw conversation history
- Replacing cloud AI services

=====================================================================

10. DESIGN PHILOSOPHY

- Local-first
- Explicit memory
- Human-in-the-loop
- Inspectable state
- Replaceable components

This system should survive:
- Model swaps
- Runtime changes
- Hardware upgrades
- Loss of external context

End of architecture snapshot.

