# Product Map for Claude Code

Product Map's product-management agents as slash commands in Claude Code. `/pm:product-strategy-roadmap`,
`/pm:prd-specification`, `/pm:ideal-customer-profile-icp` and the others answer with your project's knowledge base as
context, inside your normal coding session, and propose knowledge changes you review like code.

Your session stays a coding session. Each command runs one agent for one request; Claude writes a short brief
about the work in progress so the agent knows where you are. Every agent you invoke in one session takes its
turn in the same Product Map chat, which the website shows too.

## Install

```
/plugin marketplace add productmap-ai/product-map-for-claude-plugin
/plugin install pm@productmap
/mcp
```

`/mcp` shows the `productmap` server; pick it and sign in with your Product Map account in the browser. That is
the OAuth consent page of the Product Map connector, and the token stays in Claude Code.

For a local checkout: `/plugin marketplace add /path/to/product-map-for-claude-plugin` (the marketplace file is
at the repository root), or `claude --plugin-dir /path/to/product-map-for-claude-plugin/claude-plugin`.

## Use

| Command | What it does |
|---|---|
| `/pm:<slug> <request>` | Runs that agent for one request - `/pm:product-strategy-roadmap`, `/pm:prd-specification`, `/pm:user-research-planner`, ... The first call in a folder connects it to its project. |
| `/pm:agents` | Lists the agents for this folder's project with a starter request each. |
| `/pm:connect [project-id]` | Finds the project for this folder - from `.productmap/productmap.json`, the git remotes, or an earlier connection - or lets you pick or create one, and remembers it. |
| `/pm:context` | Reviews the open knowledge-change proposals for the project's context and applies or discards each. |

### How a turn works

1. You type `/pm:product-strategy-roadmap should we drop the free tier?`
2. Claude connects the folder to its project if it has not yet (once per folder; it asks you only when nothing
   matches, and never creates a project on its own), writes a brief about what you are working on, and runs
   the agent in this session's Product Map chat.
3. The agent answers with your project context in view; Claude prints the reply as written.
4. If the agent proposes a change to your product knowledge: for a project synced from this repository, Claude
   writes it into `.productmap/`, shows `git diff`, and asks you to accept (commit), edit, or discard. For a
   project whose context is stored on Product Map, accepting applies it there. Nothing is pushed unless you ask.
5. You carry on coding. The next `/pm:<slug>` command lands in the same chat.

### Connecting a folder

A folder is connected once. Product Map remembers it by the repository's git remotes and a fingerprint of the
folder, and Claude writes `.productmap/productmap.json` naming the project, so every clone and worktree
resolves without a question. The platform writes the same file when it publishes a project's context to git.

### From a coding task

The `productmap-advisor` subagent asks an agent one question in the background and reports the answer, for
when a coding task needs a product decision without leaving the main thread. Ask Claude to "ask the Product Map
strategy agent whether ..." and it will use it.

## What the plugin contains

- `.mcp.json`: the Product Map connector, a remote MCP server at `https://app.productmap.io/platform-api/mcp`.
  Sign-in is OAuth in the browser; the plugin holds no keys.
- `skills/<agent>/`: one command per agent, generated from Product Map's agent catalogue. `skills/connect`,
  `agents` and `context` are hand-written. The agent text is the same protocol the connector serves as its
  `agent` MCP prompt, so the connector alone gives a near-identical experience, and an agent the plugin has no
  command for yet can be run through that prompt.
- `hooks/hooks.json`: a `SessionStart` hook that tells a session the facts the commands need - its session id,
  the folder, its remotes and fingerprint, the marker, the cached project - in under a kilobyte.
- `bin/pm-bind`: gathers those facts, writes the marker, and keeps a small cache in
  `~/.claude/productmap/bindings.json`. The server holds the real bindings; the cache only saves a call.
- `agents/productmap-advisor.md`: the background subagent.

## Requirements

- Claude Code with plugin support, `git`, `python3`.
- A Product Map account. A project is picked or created on first use; a git repository is optional.

Support: hey@productmap.io. Privacy policy and terms: productmap.io.
