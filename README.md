# FlipLetters MCP

**The Model Context Protocol server for [FlipLetters](https://flipletters.com).**
FlipLetters makes split-flap and flip-dot board videos (the look of an old
train-station departure board) with synthesized mechanical sound. Boards are
written in [flipcode](https://flipletters.com/docs), a small open text format.
This MCP lets AI agents draft and validate boards, build Studio share links,
render MP4s, and send boards to a team library or a Postiz social queue.

- 📚 **Docs:** [flipletters.com/mcp](https://flipletters.com/mcp) · [flipcode reference](https://flipletters.com/docs)
- 🌐 **Endpoint:** `https://app.flipletters.com/mcp/v1` (Streamable HTTP; alias `https://app.flipletters.com/api/mcp`)
- 🔑 **API key:** [FlipLetters Studio](https://app.flipletters.com) → **Account → API keys & agents** (keys start with `flk_`; free to create)
- 💻 **Local, no key:** `npx -y flipletters-mcp-server@0.3.1` (draft tools over stdio)
- 🗂️ **Registry name:** `com.flipletters/mcp`

> This is a public listing repo (README, `server.json`, LICENSE). The hosted
> MCP runs at the endpoint above. The flipcode parser is open source (MIT) at
> [tera-sarah/flipcode](https://github.com/tera-sarah/flipcode).

---

## Connect

### Hosted (every `tools/call` needs a `flk_` key)

**Cursor** (`~/.cursor/mcp.json` or `.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "flipletters": {
      "url": "https://app.flipletters.com/mcp/v1",
      "headers": { "Authorization": "Bearer YOUR_FLK_KEY" }
    }
  }
}
```

**Claude Code:**

```bash
claude mcp add --transport http flipletters https://app.flipletters.com/mcp/v1 \
  --header "Authorization: Bearer YOUR_FLK_KEY"
```

**Claude Desktop** (`claude_desktop_config.json`, via the `mcp-remote` bridge):

```json
{
  "mcpServers": {
    "flipletters": {
      "command": "npx",
      "args": [
        "-y", "mcp-remote", "https://app.flipletters.com/mcp/v1",
        "--header", "Authorization:${AUTH_HEADER}"
      ],
      "env": { "AUTH_HEADER": "Bearer YOUR_FLK_KEY" }
    }
  }
}
```

### Local stdio (draft tools work with no key)

```json
{
  "mcpServers": {
    "flipletters": {
      "command": "npx",
      "args": ["-y", "flipletters-mcp-server@0.3.1"],
      "env": { "FLIPLETTERS_API_KEY": "optional: flk_ key for render and team tools" }
    }
  }
}
```

Then ask: *"Make a trainboard that flips through 'NOW BOARDING', 'GATE 12', 'ON TIME'."*

---

## Tools

Listing tools (`GET https://app.flipletters.com/mcp/v1` or `tools/list`) is
open. On the hosted server, every `tools/call` needs `Authorization: Bearer flk_…`.

### Draft

| Tool | What it does |
|---|---|
| `flipletters_flipcode_reference` | The flipcode cheat sheet: directives, board types, size limits, transitions. |
| `flipletters_example_board` | A complete, valid flipcode document for a board type. |
| `flipletters_validate_board` | Parse a draft: normalized code, warnings, and a share URL. |
| `flipletters_template_url` | A readable Studio URL where each query parameter mirrors one flipcode line. |

### Render and share

| Tool | What it does |
|---|---|
| `flipletters_render_board` | Start a cloud MP4 render under the key owner's plan. |
| `flipletters_render_status` | Poll a render job. |
| `flipletters_list_teams` | Teams the key owner belongs to. |
| `flipletters_share_to_team` | Share a board into a Team Library. |

### Workspaces and Postiz

| Tool | What it does |
|---|---|
| `flipletters_list_workspaces` | Agency projects (workspaces) for the key owner. |
| `flipletters_list_postiz_destinations` | Postiz destinations attached to a project. |
| `flipletters_list_postiz_integrations` | Connected social channels for a project's Postiz account. |
| `flipletters_share_to_postiz` | Upload media and schedule, draft, or post it through Postiz. |

Creating a key is free. Render usage follows the key owner's plan (free export
quota, Premium, or Studio), the same as in the Studio UI.

## Discovery

| URL | Returns |
|---|---|
| `GET https://app.flipletters.com/mcp/v1` | Server info and tool list (no key). |
| `GET https://app.flipletters.com/.well-known/mcp/server.json` | MCP server manifest. |
| `GET https://flipletters.com/llms.txt` | Agent-readable site summary. |

## Links

- [flipletters.com](https://flipletters.com) · [Studio](https://app.flipletters.com) · [Privacy](https://flipletters.com/privacy) · [Terms](https://flipletters.com/terms)
- npm: [`flipletters-mcp-server`](https://www.npmjs.com/package/flipletters-mcp-server) · [`@flipletters/flipcode`](https://www.npmjs.com/package/@flipletters/flipcode)
- Contact: dev@flipletters.com
