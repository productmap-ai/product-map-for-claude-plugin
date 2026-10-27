---
description: List the Product Map agents available for this folder's project, with what each one is for and a starter request, so the user can pick a /pm:<slug> command.
argument-hint: ""
allowed-tools: Bash(python3 *), mcp__plugin_pm_productmap__*
---

# Product Map agents for this project

!`python3 "${CLAUDE_SKILL_DIR}/../../bin/pm-bind" context --session "${CLAUDE_SESSION_ID}"`

- Find the project id: `cached_project` or `marker` above; otherwise call `connect_workspace` with the facts
  above (and follow its `choose` flow if it asks).
- Call `list_agents(project_id=<project_id>)`.
- Show a table: the row's `command` (`/pm:<slug>`), name, summary, and the first starter's title.
- End with one line: `/pm:<slug> <request>` runs one of them in this session; the agents take
  turns in the same Product Map chat.
- Do not run an agent here.
