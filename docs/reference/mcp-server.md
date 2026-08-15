---
sidebar_position: 2
---

# MCP Server

Connect an MCP-compatible client (Claude Code, Claude Desktop, etc.) directly to your Rikugan instance — list targets, browse findings, check scan status, and trigger scans from within a conversation.

Lives in [`mcp-server/`](https://github.com/geekshiv/rikugan-platform/tree/main/mcp-server) in the main repo, as a **standalone process** with its own Python environment — not embedded in the backend. The official `mcp` package requires a newer Starlette/Pydantic than this project's pinned FastAPI/SQLModel versions tolerate, so it runs separately rather than forcing a risky upgrade of the core web stack. It's a thin client over the [Public API](./api.md) — same auth, same tools' worth of surface, just callable from an MCP conversation instead of `curl`.

## Setup

```bash
cd mcp-server
python3.12 -m venv venv && source venv/bin/activate
pip install -r requirements.txt
```

Create a personal access token: **Settings → Workspace → API Tokens** (see [Public API Reference](./api.md)). Read-only is enough for every tool except `trigger_scan`.

## Available tools

| Tool | Scope | Description |
|---|---|---|
| `list_targets` | read | List targets in your accessible workspaces |
| `list_findings` | read | List findings, filterable by target/severity/state, paginated |
| `get_finding` | read | Full detail for one finding |
| `get_scan_status` | read | A scan's status/result |
| `trigger_scan` | read/write | Trigger a native scan against a target |

## Client config (Claude Desktop / Claude Code)

```json
{
  "mcpServers": {
    "rikugan": {
      "command": "/absolute/path/to/rikugan-platform/mcp-server/venv/bin/python",
      "args": ["/absolute/path/to/rikugan-platform/mcp-server/server.py"],
      "env": {
        "RIKUGAN_API_URL": "http://localhost:8000",
        "RIKUGAN_API_TOKEN": "rikugan_pat_..."
      }
    }
  }
}
```

Runs over stdio — the client launches it as a subprocess, it's not a long-running network service.
