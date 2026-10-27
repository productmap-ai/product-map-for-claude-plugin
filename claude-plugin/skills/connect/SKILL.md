---
description: Connect this folder to its Product Map project. Resolves it from the repository's marker file, git remotes, or an earlier connection; otherwise lets the user pick or create one. Remembers the answer on Product Map and writes .productmap/productmap.json so every clone resolves offline.
argument-hint: "[project-id]"
disable-model-invocation: true
allowed-tools: Bash(python3 ${CLAUDE_PLUGIN_ROOT}/scripts/pm-bind *), mcp__plugin_pm_productmap__*
---

# Connect this folder to a Product Map project

Project id given by the user: `$1` (may be empty).

!`python3 ${CLAUDE_PLUGIN_ROOT}/scripts/pm-bind context --session "${CLAUDE_SESSION_ID}"`

- Call `connect_workspace(remote_urls=<remotes>, marker=<marker, or omit when none>, cwd=<cwd>,
  folder_fingerprint=<folder_fingerprint>, client="claude-code", client_session_id=<client_session_id>,
  project_id="$1")` with the facts above. If the tool says the account is not connected, tell the user to run
  `/mcp` and sign in to Product Map, then stop.
- `status: "connected"`: the project is `project`. If `write_marker` is true, run
  `python3 ${CLAUDE_PLUGIN_ROOT}/scripts/pm-bind write-marker "<cwd>" --json '<marker as JSON>'` so every
  clone of this repository resolves to it; tell the user the file was written and can be committed.
  Otherwise run `pin-project "<cwd>" --project <project_id> --name "<name>" --knowledge-root
  "<knowledge_root_path>" --storage-mode <storage_mode>` so the next session's hook knows it.
- `status: "choose"`: show the user `projects` (name, type, storage_mode) and ask which one this folder is -
  or whether to create a new project whose context is stored on Product Map
  (`create_project(name, description, project_type)`; propose the folder name as the project name). Never pick
  or create without asking. Then call `connect_workspace` again with the chosen `project_id`.
- If `knowledge_folder` above is missing and `storage_mode` is `git`, say that the project's knowledge base is
  expected at that folder and that agents work best once it is checked out.
- Finish with one line: project name, type, storage_mode, and that `/pm:<slug> <request>` runs an
  agent here (`/pm:agents` lists them).
