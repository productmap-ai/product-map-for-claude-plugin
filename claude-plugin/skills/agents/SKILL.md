---
description: List the Product Map agents available for this folder's project, with what each one is for and a starter request, so the user can pick a /pm:<slug> command.
argument-hint: ""
allowed-tools: Read, Bash(git remote -v), Bash(git rev-parse *), mcp__plugin_pm_productmap__*
---

# Product Map agents for this project

**Facts.** Gather them once per session and reuse them: `cwd` = `git rev-parse --show-toplevel` (the
current folder when not in a git repository); `remotes` = the distinct URLs in `git remote -v`; `marker` = the
JSON in `.productmap/productmap.json` under `cwd`, read with the Read tool, or none when the file is missing;
`client_session_id` = the one a Product Map result returned earlier in this session, else omit it and keep the
one the next result returns. Where you cannot run commands, call `connect_workspace` with `client` and the
facts you have, and follow its `choose` flow.

- Find the project id: `marker` above; otherwise call `connect_workspace` with the facts above (and follow
  its `choose` flow if it asks).
- Call `list_agents(project_id=<project_id>)`.
- Show a table: the row's `command` (`/pm:<slug>`), name, summary, and the first starter's title.
- End with one line: `/pm:<slug> <request>` runs one of them in this session; the agents take
  turns in the same Product Map chat.
- Do not run an agent here.
