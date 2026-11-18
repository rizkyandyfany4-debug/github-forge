# github-forge

[![make.fun · github-core-clone-01](https://make.fun/badge/github-core-clone-01.svg)](https://make.fun/bounty/github-core-clone-01?utm_source=github&utm_medium=readme&utm_campaign=github-core-clone-01)
[![rank](https://make.fun/badge/github-core-clone-01/rizkyandyfany4-debug/github-forge/rank.svg)](https://make.fun/bounty/github-core-clone-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=github-core-clone-01)
[![reward](https://make.fun/badge/github-core-clone-01/rizkyandyfany4-debug/github-forge/reward.svg)](https://make.fun/bounty/github-core-clone-01?utm_source=github&utm_medium=readme&utm_campaign=github-core-clone-01)

A standalone GitHub-compatible git forge in a single Node 22 file (`main.ts`,
standard library only: `node:http`, `node:crypto`, `node:zlib`). One process
serves, from one deterministic in-memory store:

- **Git smart HTTP** — `git-upload-pack` (protocol v2 with `ls-refs`/`fetch`,
  and v0 fallback) and `git-receive-pack` (v0) with full packfile
  read/write, shallow/deepen, fast-forward enforcement, annotated-tag peeling.
- **REST v3** (`/api/v3`) — repos, branches, commits, git refs, contents
  (create/update/delete with SHA checks), labels, issues (+ comments),
  pull requests (+ files, squash merge with conflict detection), pagination
  with `Link` headers, `X-GitHub-Request-Id` on every reply.
- **GraphQL** (`/api/graphql`) — viewer, repository, issue(s), pullRequest(s)
  with cursor pagination, `node(id:)` with opaque global ids, `__type`
  introspection, and mutations: createIssue, closeIssue, createPullRequest,
  mergePullRequest, addComment. Batch requests supported.
- **MCP** (`/mcp`, JSON-RPC 2.0 Streamable HTTP) — modern `server/discover`
  (2026-07-28) with legacy `initialize` fallback; tools: `create_repo`,
  `get_repo`, `create_issue`, `list_issues`, `create_pull_request`,
  `merge_pull_request`.

## Run

Suite lifecycle (default): binds an ephemeral port, POSTs `{"port": N}` to
`$MAKEFUN_ENV_URL/register`, answers `GET /healthz`, then polls
`$MAKEFUN_ENV_URL/done` until the trial ends.

Standalone (no `MAKEFUN_ENV_URL`): listens on `$PORT` (or ephemeral) and
prints the address. Authentication uses `MAKEFUN_SHOP_TOKEN` as the bearer
token when set.

```sh
MAKEFUN_SHOP_TOKEN=test-token node main.ts
```

No dependencies, no network beyond the trial environment, no model calls.
Deterministic: same operations replay to the same state.
