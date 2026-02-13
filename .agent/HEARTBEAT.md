---
description: Periodic Status Check Prompt
---

# HEARTBEAT: Periodic Status Check Prompt

This document provides prompt templates for Scripter agent to periodically check and report its status.

---

##1. HEARTBEAT Overview

HEARTBEAT is a mechanism for the agent to periodically check its own status and report. It is executed in the following situations:

- When major milestones are achieved
- Before session ends
- When API usage reaches 95%
- Upon user's explicit request

---

##2. HEARTBEAT Checklist

The agent checks the following items when executing HEARTBEAT:

### 2.1. Work Status
- [ ] Is current work in progress?
- [ ] Is current work completed?
- [ ] Is next work defined?

### 2.2. Code Status
- [ ] Is written code tested?
- [ ] Do tests pass?
- [ ] Is code documented?

### 2.3. Resource Status
- [ ] Is API usage below 95%?
- [ ] Is session continuity maintained?

### 2.4. Reporting Status
- [ ] Is progress reported?
- [ ] Are issues recorded?
- [ ] Is next plan defined?

---

##3. HEARTBEAT Report Templates

### 3.1. Milestone Report

```markdown
Milestone Report: [Milestone Name]

## Progress Summary
- Milestone Status: [Completed/In Progress]
- Task Queue Alignment: [e.g., Task T-XXX finished]

## Roadmap Update
- Next Phase Triggered: [Yes/No]
- Dependencies Resolved: [List]

## Verification & Refinement
- Test Coverage Status: [XX]%
- Refinement Pass Results: [Summary of improvements]

## Immediate Next Task
1. [Next Task ID and Description]
```

### 3.2. Session/Cycle End Report (Context Handoff)

```markdown
Session State: [Date/Time]

## Completed Tasks
- [List of completed Task IDs]

## In-Progress Context
- Current Task ID: [T-XXX]
- **Pause Location**: [Specific code file/function or document section]
- **Resume Point**: [Exactly what to do first in the next session]

## Unresolved Issues
- [List from MEMORY.md Issue Log]

## Queue for Next Iteration
1. [Priority 1 Task]
2. [Priority 2 Task]
```

### 3.3. Usage Report

```markdown
Quota Alert: [Date/Time]

## Current Usage
- Quota Usage: XX%
- Transition: Freezing Session

## Context Preservation
- [ ] Task status updated in MEMORY.md
- [ ] Pause location recorded
- [ ] Next session goals defined
```

---

##4. HEARTBEAT Execution Procedure

### 4.1. When Milestone Achieved
1. Verify results against the roadmap in `MEMORY.md`.
2. Execute 'Refinement Pass' (Section 3.2 of `AGENTS.md`).
3. Run final tests.
4. Record the Milestone Report in `MEMORY.md`.
5. Update Task Queue status.

### 4.2. When Cycle/Session Ends
1. Define the precise **Pause Location**.
2. Update `MEMORY.md` Roadmap & Queue.
3. Record Session End Report.
4. If usage is < 95%, the agent may choose to proceed to the next task immediately.

### 4.3. When API Usage Reaches 95%
1. Immediately perform 'Context Handoff'.
2. Record state in `MEMORY.md`.
3. Terminate for safety.

### 4.4. When User Requests
1. Check current status
2. Write report in requested format
3. Report to user

---

##5. HEARTBEAT Frequency

| Situation | Frequency | Report Format |
|-----------|------------|---------------|
| Milestone Achieved | Immediately | Milestone Report |
| Session End | Immediately | Session End Report |
| API Usage 95% | Immediately | Usage Report |
| User Request | Immediately | Requested Format |
| Periodic Check | Every 1 hour | Simple Status Report |

---

##6. HEARTBEAT & MEMORY.md Integration

HEARTBEAT reports are permanently stored in [`MEMORY.md`](MEMORY.md):

```markdown
## HEARTBEAT History

### [Date/Time] - Milestone Report
- Milestone: [Milestone Name]
- Progress: XX%
- Next Plan: [Plan]

### [Date/Time] - Session End Report
- Completed Work: [List]
- In Progress Work: [List]
- Next Steps: [Steps]
```

---

##7. HEARTBEAT Automation

HEARTBEAT can be automated as follows:

### 7.1. Periodic Check
```python
# Periodic status check example
import time

def heartbeat_check():
    while True:
        # Check API usage
        usage = check_api_usage()
        if usage >= 95:
            trigger_freeze_state()

        # Wait 1 hour
        time.sleep(3600)
```

### 7.2. Milestone Trigger
```python
# Auto report when milestone achieved
def on_milestone_reached(milestone_name):
    report = generate_milestone_report(milestone_name)
    save_to_memory(report)
    notify_user(report)
```

---

## 8. External Notifications (Webhooks)

To ensure security and prevent credential leakage (e.g., in GitHub), the Webhook URL **MUST** be read from the `.env` file using the variable name `AGENT_HEARTBEAT_WEBHOOK`.

### 8.1. Notification Priority
1. **Webhook**: If `AGENT_HEARTBEAT_WEBHOOK` exists in `.env`, the agent **must** POST the report.
2. **Local Log**: Record the report in `MEMORY.md` (for session history).
3. **Terminal**: Output for immediate visibility.

### 8.2. Discord Webhook Example (Using curl)
```bash
curl -H "Content-Type: application/json" \
     -X POST \
     -d "{\"content\": \"**Milestone Achievement**\n[Report Content...]\"}" \
     $AGENT_HEARTBEAT_WEBHOOK
```

---

## 9. HEARTBEAT Monitoring

The user can monitor HEARTBEAT through the following methods:

- Check [`MEMORY.md`](MEMORY.md)
- Check log files
- Receive Webhook notifications
- Directly ask agent for status

---

##10. The Core of HEARTBEAT

> **"Periodic Status Check, Transparent Reporting, Continuous Context Preservation"**

HEARTBEAT is a core mechanism for the agent to transparently report its status and maintain work continuity.
