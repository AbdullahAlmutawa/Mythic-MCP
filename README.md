# Mythic MCP Server (Fork)

> Fork of [nbaertsch/Mythic-MCP](https://github.com/nbaertsch/Mythic-MCP) with a fix for wrapper payload creation.

An MCP server that gives AI assistants full operational control of the [Mythic C2 Framework](https://github.com/its-a-feature/Mythic). Connect it to Claude Code, Claude Desktop, or any MCP client and operate Mythic through natural language.

## What's Changed

**`wrapped_payload` fix** — The upstream [mythic-sdk-go](https://github.com/nbaertsch/mythic-sdk-go) `CreatePayload` function builds a GraphQL mutation map but never includes the `wrapped_payload` field. This means calling `mythic_create_payload` with a `wrapper_payload` parameter (e.g. ScareCrow wrapping an Apollo payload) silently fails with `"Missing wrapped_payload UUID"`.

The fix patches `payloads.go` (~line 236) in the SDK dependency to include `wrapped_payload` in the mutation map when provided.

**File:** `$GOPATH/pkg/mod/github.com/nbaertsch/mythic-sdk-go@.../pkg/mythic/payloads.go`

## Setup

```bash
# Build
go build -o mythic-mcp ./cmd/mythic-mcp

# Run (stdio mode — Claude Code / Claude Desktop)
MYTHIC_URL=https://mythic.lab:7443 \
MYTHIC_API_TOKEN=your-token \
  ./mythic-mcp
```

### Claude Code

```bash
claude mcp add mythic \
  -e MYTHIC_URL=https://127.0.0.1:7443 \
  -e MYTHIC_API_TOKEN=your-token \
  -e MYTHIC_SKIP_TLS_VERIFY=true \
  -s user \
  -- /path/to/mythic-mcp
```

### Claude Desktop

Add to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "mythic": {
      "command": "/path/to/mythic-mcp",
      "env": {
        "MYTHIC_URL": "https://mythic.lab:7443",
        "MYTHIC_API_TOKEN": "your-token"
      }
    }
  }
}
```

## Configuration

| Variable | Required | Description |
|----------|:--------:|-------------|
| `MYTHIC_URL` | Yes | Mythic server URL |
| `MYTHIC_API_TOKEN` | Yes* | API token or JWT |
| `MYTHIC_USERNAME` / `MYTHIC_PASSWORD` | Yes* | Alternative: credential-based auth |
| `MYTHIC_SKIP_TLS_VERIFY` | | Skip TLS verification (default `false`) |
| `MCP_TRANSPORT` | | `stdio` (default) or `http` |
| `MCP_HTTP_PORT` | | HTTP/SSE listen port (default `3333`) |

*One of API token or username/password is required.

## Tools

147 tools across 18 categories:

- **Auth** — login, API tokens, session management
- **Payloads** — build, download, wrap (ScareCrow), inspect agents
- **Callbacks** — list, task, pivot, manage active sessions
- **C2 Profiles** — start/stop listeners, IOCs, configuration
- **Tasks** — issue commands, read output, wait for completion
- **Files** — upload, download, preview, bulk export
- **MITRE ATT&CK** — technique lookup, coverage mapping
- **Operations** — campaign management, event logs
- **Credentials & Artifacts** — credential store, artifact tracking
- **Hosts, Processes, Screenshots, Keylogs, Tags, Operators, Commands, Documentation**

## Upstream

- [nbaertsch/Mythic-MCP](https://github.com/nbaertsch/Mythic-MCP) — original repo
- [nbaertsch/mythic-sdk-go](https://github.com/nbaertsch/mythic-sdk-go) — Go SDK (where the fix lives)
- [Mythic C2 Framework](https://github.com/its-a-feature/Mythic)
- [Model Context Protocol](https://modelcontextprotocol.io)

## License

[MIT](LICENSE)
