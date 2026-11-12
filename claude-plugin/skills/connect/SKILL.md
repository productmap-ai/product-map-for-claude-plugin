---
description: Connect this folder to its Product Map project. Resolves it from the repository's marker file, git remotes, or an earlier connection; otherwise lets the user pick or create one. Remembers the answer on Product Map and writes .productmap/productmap.json so every clone resolves offline.
argument-hint: "[project-id]"
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, LS
---

# Connect this folder to a Product Map project

Project id given by the user: `$1` (may be empty).

**Facts.** Gather them once per session and reuse them: `cwd` = `git rev-parse --show-toplevel` (the
current folder when not in a git repository); `remotes` = the distinct URLs in `git remote -v`; `marker` = the
JSON in `.productmap/productmap.json` under `cwd`, read with the Read tool, or none when the file is missing;
`client_session_id` = the one a Product Map result returned earlier in this session, else omit it and keep the
one the next result returns. Where you cannot run commands, call `connect_workspace` with `client` and the
facts you have, and follow its `choose` flow.

- Call `connect_workspace(remote_urls=<remotes>, marker=<marker, or omit when none>, cwd=<cwd>,
  client="claude-code", client_session_id=<client_session_id, if known>, project_id="$1")` with the facts
  above. If the tool says the account is not connected, tell the user to run
  `/mcp` and sign in to Product Map, then stop.
- `status: "connected"`: the project is `project`. If `write_marker` is true, write the result's `marker` as
  JSON to `.productmap/productmap.json` under `cwd` (create the folder) so every clone of this repository
  resolves to it; tell the user the file was written and can be committed. Otherwise Product Map remembers
  the folder by its remotes, and the next session resolves it with one `connect_workspace` call.
- `status: "choose"`: show the user `projects` (name, type, storage_mode) and ask which one this folder is -
  or whether to create a new project whose context is stored on Product Map
  (`create_project(name, description, project_type)`; propose the folder name as the project name). Never pick
  or create without asking. Then call `connect_workspace` again with the chosen `project_id`.
- If `storage_mode` is `git` and the project's `knowledge_root_path` folder (default `.productmap`) does not
  exist under `cwd`, say that the knowledge base is expected there and that agents work best once it is
  checked out.
- Finish with one line: project name, type, storage_mode, and that `/pm:<slug> <request>` runs an
  agent here (`/pm:agents` lists them).
