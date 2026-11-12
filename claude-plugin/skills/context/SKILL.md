---
description: Review the knowledge changes Product Map agents have proposed for this folder's project and accept or discard each one - into the .productmap/ files and a commit for a git-synced project, or on Product Map for a cloud-stored one.
argument-hint: ""
allowed-tools: Read, Edit(.productmap/**), Write(.productmap/productmap.json), Bash(git remote -v), Bash(git rev-parse *), Bash(git diff *), Bash(git add *), Bash(git commit *), Bash(shasum -a 256 *), Bash(sha256sum *), Bash(mkdir -p *), mcp__plugin_pm_productmap__*
---

# Review proposed Product Map context changes

**Facts.** Gather them once per session and reuse them: `cwd` = `git rev-parse --show-toplevel` (the
current folder when not in a git repository); `remotes` = the distinct URLs in `git remote -v`; `marker` = the
JSON in `.productmap/productmap.json` under `cwd`, read with the Read tool, or none when the file is missing;
`client_session_id` = the one a Product Map result returned earlier in this session, else omit it and keep the
one the next result returns. Where you cannot run commands, call `connect_workspace` with `client` and the
facts you have, and follow its `choose` flow.

- Find the project id: `marker` above; otherwise `connect_workspace` with the facts above.
- `list_context_proposals(project_id)`. If `proposals_enabled` is false, explain the `reason` and stop. If the
  list is empty, say so and stop. Its `apply_mode` decides how each accepted change is applied.
- For each proposal, in order: `read_context_proposal(project_id, proposal_id, include_content=true)`; show
  `title` and the `diff` verbatim; ask accept / edit then accept / discard.
  - `apply_mode: "local_files"`: do not change local files before the user accepts the proposal.
    Accept: resolve `target_full_path` inside the repository root; compute the sha256 of the local file.
    If it exists and differs from `base_content_sha256`, warn that the local file moved on and ask before
    overwriting. Write `proposed_content` (or the user's accepted edited content) to the file, creating
    folders for a new one; show `git diff -- <path>`.
    `git add <path>`; `git commit -m "Apply Product Map context proposals: <title>"`;
    `reconcile_context_proposals(project_id, items=[{proposal_id, accepted: true, commit_sha: <HEAD>,
    local_content_sha256: <sha256 of the committed file>}])`. Push only if the user asks.
    Discard: only call `reject_context_proposals(project_id, [proposal_id])`; leave local files and the
    Git index untouched. Never restore or delete files when discarding. Do not use `accept_context_proposals` here.
  - `apply_mode: "server"`: accept with `accept_context_proposals(project_id, [proposal_id])` (applied on
    Product Map); discard with `reject_context_proposals`.
- One line per decision; no commentary on the content of the changes.
