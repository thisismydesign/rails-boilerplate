---
name: skills
description: Write agent skills and instructions. Use when creating or editing SKILL.md files, AGENTS.md, or any agent instruction document.
---

# Writing Skills & Agent Instructions

## Rules

- Content MUST be concise. Every character earns its place.
- Prescriptive statements MUST use RFC 2119 keywords (MUST, MUST NOT, SHOULD, SHOULD NOT, MAY).
- Content MUST describe intention and constraints, not implementation — name patterns, don't build them out.
- Code blocks MUST be **copy-paste templates** only.
- Content MUST NOT teach things the agent already knows (language syntax, framework basics, standard patterns).
- Content MUST NOT narrate what code does. Only explain _why_ if non-obvious.
- SHOULD prefer bullets over paragraphs, one sentence over two.

## Self-test

Before finalizing, verify:
- Can any section be deleted without loss of correctness?
- Can any code block be replaced with a sentence?
- Would the agent produce wrong output without this line?
