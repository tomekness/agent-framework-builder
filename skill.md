---
name: agent-framework-builder

description: Plan and build a complete openCode Agent Framework for a new project. Creates AGENTS.md, opencode.json, subagents, skills, commands, and memory structure. Use this skill when you want to set up a new autonomous agent.

compatibility: opencode
---

# Agent Framework Builder

This skill guides you through building a complete openCode Agent Framework for a new project.

---

## Step 1 - Understand the Project

Ask the user these questions before creating anything:

1. **What should the agent do?** (Task in 1-2 sentences)
2. **Which external tools/APIs are needed?** (MCPs, REST APIs, databases)
3. **How autonomous should the agent work?** (fully autonomous / with confirmation / just plan)
4. **Which subagents are needed?** (e.g. specialized builder, writer, tester)
5. **Is there a budget limit?** (Premium requests, prefer free models?)
6. **Should memory persist between sessions?** (yes/no)

---

## Step 2 - Plan File Structure

Always create this base structure:

```

project-name/
+-- AGENTS.md <- Main rules, automatically loaded
+-- memory/
+-- memory.md <- Project status & insights
+-- variables.md <- All configuration values
+-- progress.md <- Progress checklist
+-- .opencode/
+-- agents/ <- Subagent Markdown definitions
+-- commands/ <- Custom /commands
+-- prompts/ <- System prompts as .txt files
+-- skills/ <- Project-specific skills
+-- opencode.json <- Agent & MCP configuration (in root OR .opencode/)

```

---

## Step 3 - Build opencode.json

### Required Fields per Agent

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "skill": { "*": "allow" }
  },
  "mcp": {
    "my-mcp": {
      "type": "local",
      "command": ["npx", "-y", "my-mcp-package"],
      "environment": {
        "API_KEY": "xxx"
      }
    }
  },
  "agent": {
    "main-agent": {
      "description": "...",
      "mode": "primary",
      "temperature": 0.1,
      "max_steps": 20,
      "prompt": "{file:./prompts/main-agent-system.txt}",
      "permissions": {
        "file": "allow",
        "bash": "allow",
        "web": "allow"
      }
    }
  }
}
```

### Important Rules for opencode.json

- Omit `model` -> automatically uses currently selected model
- Only set `model` if a specific model must be forced (e.g. free subagents)
- Reference `prompt` with `{file:./prompts/name.txt}` - file MUST exist or error
- `command` in MCP always as array, never as string with separate `args`
- Always specify `type: "local"` for MCP
- opencode.json can be in `.opencode/` OR root - both work

### Budget Strategy

When premium requests are limited:
- Main agent: don't set model (user chooses)
- Subagents: `"model": "github/gpt-4.1"` (free with Copilot subscription)
- Keep main agent `max_steps` low (15-25)
- Higher `max_steps` for free subagents (30-50)

---

## Step 4 - Build AGENTS.md

AGENTS.md is the most important file - automatically loaded every session.

### Required Sections

```markdown
# Projectname - openCode Agent Rules

## Project
[1-2 sentences what the agent does]

## Startup - ALWAYS execute first
1. Read memory/memory.md
2. Read memory/variables.md  
3. Read memory/progress.md
[Briefly inform user, then continue]

## Core Rules
- [Most important behavior rule]
- [Second important rule]
- Never guess - ask user for missing values
- Update memory/ after every step

## Agent Usage
- Main work: `main-agent` (Tab to switch)
- [Subagent]: Call `@subagent-name` for [task]

## Skills
[Table: Situation -> Skill to load]

## Constraints
[Table: What -> Rule]
```

### AGENTS.md Rules
- Keep under 150 lines - loaded into context every session
- No umlauts (ae/oe/ue instead of ä/ö/ü) - prevents encoding issues
- No em-dashes (—) - only normal hyphens (-)
- Most important info first - agent reads top to bottom

---

## Step 5 - Build System Prompts

Every primary agent gets a `.txt` file in `.opencode/prompts/`.

### Required Sections in System Prompt

```markdown
## Startup (every session)
1. Read memory/memory.md, memory/variables.md, memory/progress.md
2. Inform user in 2-3 sentences
3. Continue at last open step


## task
[Exact description what agent does]

## Phases
[Phase 1, Phase 2, etc. with concrete steps]

## Use Skills
[When to load which skill]

## Important Rules
[Project-specific rules]

## Use Subagents Sparingly
- Only call subagents when really needed
- Update memory only at end of session

```

---

## Step 6 - Create Memory Files

### memory.md Base Structure
```markdown
# memory.md - Project Status
_Last updated: not started yet_

## Status
Phase: 0 - Not started
Next step: Start Phase 1

## Insights
(to be filled)

## Error Log
| Date | Error | Solution |
```

### variables.md Base Structure
```markdown
# variables.md - Configuration
_MISSING = still needs to be entered_

## [Service 1]
VAR_1=    # MISSING
VAR_2=    # MISSING

## Status
| Variable | Status |
|----------|--------|
| VAR_1    | MISSING |
```

### progress.md Base Structure
```markdown
# progress.md - Progress

## Phase 1 - [Name]
- [ ] Step 1
- [ ] Step 2

## Current Focus
Next action: Start Phase 1
```

---

## Step 7 - Create Commands

Minimum commands for every agent:

### /start (Required)
```markdown
***
description: Start agent - reads memory, shows status, continues
agent: main-agent
***
Read memory/memory.md, memory/variables.md, memory/progress.md.
Summarize status in 2-3 sentences and continue at next open step.
```

### /status (Recommended)
```
***
description: Show project status without doing anything
agent: main-agent
***
Read memory files and show compact overview.
Then do NOTHING - wait for instruction.
```

---

## Step 8 - MCP Configuration

### Common MCP Problems and Solutions

| Problem | Cause | Solution |
|---|---|---|
| MCP red / doesn't start | Missing `type: "local"` | Add it |
| MCP red | `command` as string instead of array | Use array |
| MCP red | Double space in token | Check token |
| Only read-only tools | Wrong MCP server | Check correct MCP |
| stdio MCP doesn't start | Missing env vars | `MCP_MODE=stdio`, `LOG_LEVEL=error`, `DISABLE_CONSOLE_OUTPUT=true` |
| Local URLs blocked | Security mode | `WEBHOOK_SECURITY_MODE=moderate` |

### MCP Type Reference

**Local (stdio):**
```json
{
  "type": "local",
  "command": ["npx", "-y", "package-name"],
  "environment": { "KEY": "value" }
}
```

**Remote (HTTP/SSE via supergateway):**
```json
{
  "type": "local",
  "command": [
    "npx", "-y", "supergateway",
    "--streamableHttp", "http://host:port/mcp/http",
    "--header", "authorization:Bearer TOKEN"
  ]
}
```

---

## Step 9 - Pre-Completion Checklist

- [ ] All `{file:...}` references in opencode.json - do files really exist?
- [ ] No umlauts in AGENTS.md and system prompts
- [ ] memory/, variables.md, progress.md created
- [ ] /start command created
- [ ] MCP `type: "local"` set
- [ ] MCP `command` as array
- [ ] No `prompt` reference to non-existent files
- [ ] For free subagents: `model` explicitly set

---

## Typical Agent Archetypes

| Archetype | Primary Agent | Subagents | Special Feature |
|---|---|---|---|
| Workflow Builder | main-agent | builder, memory-writer | MCP for external tool |
| Research Agent | researcher | summarizer, memory-writer | Needs web access |
| Code Generator | coder | reviewer, tester | bash access important |
| Data Pipeline | pipeline-agent | fetcher, transformer | API credentials in variables.md |
| Content Creator | writer | editor, memory-writer | No bash needed |

---

## Autonomy Level (always include in variables.md)

Every agent framework should have `AUTONOMY_LEVEL` in `variables.md`:

```markdown
AUTONOMY_LEVEL=3
1 = Confirmation at every step
2 = Autonomous within phases, confirmation between phases
3 = Fully autonomous, only ask for real problems
```

In main agent's system prompt:

```markdown
## Autonomy Level (read from variables.md)

AUTONOMY_LEVEL=1: Announce every step, wait for confirmation
AUTONOMY_LEVEL=2: Autonomous within phases, show status between phases
AUTONOMY_LEVEL=3: Work completely autonomously, only ask for:
- Missing values not in variables.md
- Errors not automatically fixable
- Decisions with irreversible consequences
```


## Variables Check Rule (Required in every system prompt)

```markdown
## Variables - MOST IMPORTANT RULE
ALWAYS read memory/variables.md completely FIRST.
ONLY ask for variables marked "MISSING".
Variables with values -> NEVER ask again.
Before every question check: "Is it already in variables.md?"
```

