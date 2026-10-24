# General Tool Building Rules

> **Tri-Platform Agent Skill** for Claude Desktop / Code, Grok CLI, and Google Antigravity / Gemini.  
> Mandatory workflow and guardrails for working on any software codebase with disciplined documentation.

---

## Overview

`general-tool-building-rules` provides standardized capabilities across conversational AI platforms:
- Formal documentation-first software engineering and code modification workflow.\n- Step 0 mandatory constraint gathering protocol at the start of every session.\n- Implementation Plan, App Changes Tracker, and README documentation lifecycle management.\n- Semantic-version git tagging (vX.Y.Z) and mandatory full-repo GitHub backups.\n- Tri-platform NTFS junctions connecting Claude, Grok, and Antigravity.

---

## Tri-Platform Architecture

Zero duplicate code across platforms. Canonical source resides in `.agents`:
- **Canonical Source**: `C:\Users\<you>\.agents\skills\general-tool-building-rules`
- **Claude**: `C:\Users\<you>\.claude\skills\general-tool-building-rules` (NTFS Junction)
- **Grok**: `C:\Users\<you>\.grok\skills\general-tool-building-rules` (NTFS Junction)
- **Antigravity**: `C:\Users\<you>\.gemini\config\skills\general-tool-building-rules` (NTFS Junction)

---

## Documentation Suite

- [`HOW_TO_USE.txt`](HOW_TO_USE.txt): Standardized user guide and prompt templates.
- [`IMPLEMENTATION_PLAN.md`](IMPLEMENTATION_PLAN.md): Feature checklist and roadmap.
- [`App Changes Tracker.html`](App%20Changes%20Tracker.html): Interactive visual version log.
- [`SKILL.md`](SKILL.md): Agent system instructions and triggers.
