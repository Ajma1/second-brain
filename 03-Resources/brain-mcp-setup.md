---
type: reference
source: obsidian
title: "Connecting to the Brain MCP"
tags: [reference, mcp, setup, brain]
---

# Connecting to the Brain MCP

The brain runs as a local **stdio MCP server** (`mcp/server.ts`) in the
**`modernagencysales/maestro-score`** repo. Point Claude Code (or any MCP client) at it.

## 1. Get the repo + install

```bash
git clone git@github.com:modernagencysales/maestro-score.git
cd maestro-score
npm install
```

## 2. Add to your MCP config (`~/.claude/settings.json`)

```json
{
  "mcpServers": {
    "brain": {
      "command": "npx",
      "args": ["tsx", "/path/to/maestro-score/mcp/server.ts"],
      "env": {
        "BRAIN_CONVEX_URL": "https://<prod-deployment>.convex.cloud"
      }
    }
  }
}
```

- `BRAIN_CONVEX_URL` — the prod Convex deployment URL. It's already set in
  `mcp/brain-mcp.sh`; ask Ajmal if you need it and to confirm your access.
- `SEARXNG_URL` (optional) — enables the web tools via the company SearXNG.

## 3. Tools you get

- `search_brain` — semantic search over all company knowledge
- `grepVault` — exact-term lexical search across vault notes
- `readNote` / `listNotes` / `backlinks` — vault read ops
- `writeNote` / `appendNote` — vault write ops (canonical; stamped `source: agent`)
- `whose_day` — person activity lookup
- `web_search` / `web_fetch` — via the company SearXNG

## Notes

- **Repo is `modernagencysales/maestro-score`.** The old name `Ajma1/business-brain`
  is deprecated — do not clone it.
- The vault (canonical markdown store) is a separate repo, `Ajma1/second-brain`,
  which maestro-score indexes into Convex. You don't clone the vault to use the MCP.
- **Blocker if tools return nothing:** the vault must be indexed and you need a valid
  `BRAIN_CONVEX_URL` — ask Ajmal to share the Convex deployment URL + confirm access.
