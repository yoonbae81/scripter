---
description: Permanent Memory Storage Template
---

# MEMORY: Permanent Memory Storage

This document is the permanent memory storage of the agent. Use this file to preserve context between sessions and track the autonomous roadmap.

---

## 🛡️ HOW TO USE THIS FILE (Instruction for the Agent)
1. **Context Load**: Start every session by reading Section 3.3 (Pause Location) to understand where the last agent stopped.
2. **Roadmap Sync**: Check Section 3.1 to see the big picture.
3. **Queue Execution**: Pick the next `Pending` task from Section 3.2.
4. **Update Before Exit**: Before ending a session or reaching a quota, you **MUST** update the Task Queue and Pause Location.

---

## 1. Roadmap & Task Queue (Active Planning)

### 1.1. Project Roadmap
*Define the major phases of the project here.*
| Phase | Milestone | Status | Priority | Notes |
|-------|-----------|--------|----------|-------|
| 1 | [e.g. Foundation Setup] | Pending | High | |
| 2 | [e.g. Core Feature Implementation] | Pending | Medium | |

### 1.2. Current Task Queue (Iteration Loop)
*Break down the roadmap into actionable tasks.*
| Task ID | Description | Status | Dependency | Next Step |
|---------|-------------|--------|------------|-----------|
| T-001 | [e.g. Initialize Repo] | Pending | None | [Action Name] |

### 1.3. Pause Location (Context Handoff)
*REQUIRED: Fill this out before every session end.*
- **Current Task**: [Task ID]
- **Specific Point**: [e.g., Finished function X, about to write tests for Y]
- **Immediate Next Step**: [Exactly what the next agent/iteration should do first]

---

## 2. Issue Resolution History
| Issue | Status | Resolution / Current Block |
|--------|---------|----------------------------|
| | | |

---

## 3. Learned Patterns & User Preferences
- **Language**: Korean / English (as requested)
- **Style**: Concise / Detailed
- **Technical Patterns**: [Store successful architectural patterns here]

---

## 4. Tech Stack Information
| Item | Value |
|-------|-------|
| Operating System | [e.g. macOS] |
| Work Directory | [Full Path] |
| Required Tools | [List from AGENTS.md] |

> ⚠️ **Security Note**: Professional/Sensitive credentials like Webhook URLs or API Keys **must not** be stored in this file. Use the `.env` file instead (configured via `.env.example`).
