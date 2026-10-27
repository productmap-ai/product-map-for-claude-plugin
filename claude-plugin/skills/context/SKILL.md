---
description: Review the knowledge changes Product Map agents have proposed for this folder's project and accept or discard each one - into the .productmap/ files and a commit for a git-synced project, or on Product Map for a cloud-stored one.
argument-hint: ""
allowed-tools: Read, Edit(.productmap/**), Bash(python3 "${CLAUDE_SKILL_DIR}/../../bin/pm-bind" *), Bash(git diff *), Bash(git add *), Bash(git commit *), Bash(git rev-parse *), Bash(git checkout -- *), Bash(shasum -a 256 *), Bash(sha256sum *), Bash(mkdir -p *), mcp__plugin_pm_productmap__*
---

# Review proposed Product Map context changes

!`python3 "${CLAUDE_SKILL_DIR}/../../bin/pm-bind" context --session "${CLAUDE_SESSION_ID}"`

- Find the project id: `cached_project` or `marker` above; otherwise `connect_workspace` with the facts above.
- `list_context_proposals(project_id)`. If `proposals_enabled` is false, explain the `reason` and stop. If the
  list is empty, say so and stop. Its `apply_mode` decides how each accepted change is applied.
- For each proposal, in order: `read_context_proposal(project_id, proposal_id, include_content=true)`; show
  `title` and the `diff` verbatim; ask accept / edit then accept / discard.
  - `apply_mode: "local_files"`: compute the sha256 of the local file at `target_full_path`; if it exists and
    differs from `base_content_sha256`, warn that the local file moved on and ask before overwriting. Write
    `proposed_content` to the file (create folders for a new one), show `git diff -- <path>`.
    Accept: `git add <path>`; `git commit -m "Apply Product Map context proposals: <title>"`;
    `reconcile_context_proposals(project_id, items=[{proposal_id, accepted: true, commit_sha: <HEAD>,
    local_content_sha256: <sha256 of the committed file>}])`. Push only if the user asks.
    Discard: restore the file (`git checkout -- <path>`, or delete a new file), then
    `reject_context_proposals(project_id, [proposal_id])`. Do not use `accept_context_proposals` here.
  - `apply_mode: "server"`: accept with `accept_context_proposals(project_id, [proposal_id])` (applied on
    Product Map); discard with `reject_context_proposals`.
- One line per decision; no commentary on the content of the changes.
