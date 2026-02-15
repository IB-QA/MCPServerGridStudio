# MCP Grid Studio

> A dynamic MCP server bridge that manages multiple MCP servers on demand without overloading AI agents.

**By [IB-QA](https://github.com/IB-QA)** -- Intelligent Build & Quality Automation

[![CI](https://github.com/IB-QA/mcp-marketplace-grid-studio/actions/workflows/ci.yml/badge.svg)](https://github.com/IB-QA/mcp-marketplace-grid-studio/actions/workflows/ci.yml)
[![Release](https://img.shields.io/github/v/release/IB-QA/mcp-marketplace-grid-studio?include_prereleases)](https://github.com/IB-QA/mcp-marketplace-grid-studio/releases)
[![PyPI](https://img.shields.io/pypi/v/mcp-grid)](https://pypi.org/project/mcp-grid/)
[![Python](https://img.shields.io/pypi/pyversions/mcp-grid)](https://pypi.org/project/mcp-grid/)
[![VS Code](https://img.shields.io/badge/VS%20Code-Extension-blue?logo=visualstudiocode)](https://marketplace.visualstudio.com/items?itemName=IB-QA.mcp-grid)
[![License](https://img.shields.io/github/license/IB-QA/mcp-marketplace-grid-studio)](LICENSE)
[![Marketplace](https://img.shields.io/badge/Marketplace-grid.mcpstudio.qabots.diy-blueviolet)](https://grid.mcpstudio.qabots.diy)

---

<!-- TODO: Replace with actual demo GIF/video -->
<!-- ![MCP Grid Demo](docs/assets/demo.gif) -->

## What is MCP Grid?

**MCP Grid** is a meta-MCP server that sits between your AI agent and multiple MCP servers. Instead of loading every tool from every server into your agent's context, MCP Grid exposes **21 management tools** that let the agent dynamically discover, start, use, and stop servers on demand.

```
AI Agent  -->  [21 Tools]  -->  MCP Grid  -->  [Dynamic Server Instances]
                                    |              [OpenAPI -> MCP Tools]
                                    |              [Enterprise Workflows]
                              Management API  -->  VS Code Extension UI
```

## Why MCP Grid?

| Problem | MCP Grid Solution |
|---------|-------------------|
| Too many tools bloat the context window | Only 21 meta-tools exposed; actual tools loaded on demand |
| Servers waste resources running idle | Auto-start on demand, auto-shutdown when idle |
| No visibility into tool performance | Built-in metrics dashboard with latency, success rates |
| Can't use REST APIs as MCP tools | OpenAPI/Swagger spec -> instant MCP tools |
| Enterprise workflows stuck in web UI | Execute workflow chains directly from your IDE |
| Managing MCP configs is painful | Visual marketplace + one-click install |

## Key Features

### Server Management
- **Dynamic Lifecycle** -- Servers start on demand and auto-shutdown when idle
- **Context Efficiency** -- Only meta-tools in context; actual tools appear when needed
- **Parallel Execution** -- Multiple tool calls across instances execute simultaneously
- **Multi-Agent Isolation** -- Each agent session gets its own server instances
- **Resource Guards** -- System CPU/memory checks, per-server and per-agent limits
- **Health Monitoring** -- Auto-restart on crash, periodic health checks

### OpenAPI-to-MCP Adapter
- **Any REST API** -- Import OpenAPI 3.x or Swagger 2.0 specs (URL, file, or paste)
- **Auto-Discovery** -- Endpoints become callable MCP tools instantly
- **Auth Support** -- API Key, Bearer Token, Basic Auth, OAuth2
- **Customizable** -- Rename tools, edit descriptions, enable/disable endpoints

### Enterprise Platform Integration
- **Workflow Chains** -- Connect to enterprise AI platforms via JWT + WebSocket
- **Real-time Execution** -- Stream workflow results with heartbeat monitoring
- **Zero Persistence** -- All tokens stay in-memory, nothing written to disk

### VS Code Extension
- **Server Catalog** -- Browse 11+ pre-built servers across 5 categories
- **Marketplace** -- Discover, configure, and install servers visually
- **Active Instances** -- Monitor running servers with tool listings
- **Metrics Dashboard** -- Real-time stats: calls, latency, success rates
- **One-Click Install** -- Add servers from marketplace or OpenAPI specs

## Quick Start

### Install the Python Package

```bash
pip install mcp-grid
```

### Add to Your AI Agent

Add to `~/.cursor/mcp.json` (or Claude Desktop config):

```json
{
  "mcpServers": {
    "mcp-grid": {
      "command": "mcp-grid",
      "args": []
    }
  }
}
```

### Install the VS Code Extension

Download the latest `.vsix` from [Releases](https://github.com/IB-QA/mcp-marketplace-grid-studio/releases) and install:

```bash
code --install-extension mcp-grid.vsix
```

## 21 Agent Tools

### Local MCP Server Management (9 tools)

| Tool | Purpose |
|------|---------|
| `discover_servers` | Browse servers by category, search, or tag |
| `configure_server` | Create an instance with specific options |
| `activate_server` | Start the server and get available tools |
| `call_tool` | Execute a tool on an active instance |
| `deactivate_server` | Stop an instance and free resources |
| `get_server_status` | Check instance status and statistics |
| `list_active_tools` | See all tools on active instances |
| `parallel_call` | Execute multiple calls across instances at once |
| `import_mcp_config` | Import servers from `mcp.json` format |

### OpenAPI-to-MCP Adapter (6 tools)

| Tool | Purpose |
|------|---------|
| `import_openapi_spec` | Import any OpenAPI/Swagger spec |
| `list_api_tools` | Browse discovered API endpoints as tools |
| `configure_api_auth` | Set API credentials (API key, Bearer, Basic, OAuth2) |
| `customize_api_tool` | Rename, re-describe, or disable tools |
| `call_api_tool` | Execute an API endpoint as an MCP tool |
| `get_api_tool_schema` | Get full JSON Schema for tool parameters |

### Enterprise Platform Integration (6 tools)

| Tool | Purpose |
|------|---------|
| `connect_platform` | Connect to an enterprise AI platform with JWT |
| `list_workflows` | Browse available workflow chains |
| `get_workflow_details` | Inspect chain components and input variables |
| `execute_workflow` | Run a workflow chain via WebSocket |
| `get_platform_status` | Check connection and token validity |
| `disconnect_platform` | Disconnect and clear all tokens |

## Pre-Built Server Catalog

| Category | Servers | Config Options |
|----------|---------|----------------|
| Browser & Automation | Playwright MCP, Puppeteer MCP | headless, browser, viewport |
| Data & Analytics | SQLite MCP, PostgreSQL MCP | db_path, connection_string |
| Development | GitHub MCP, Git MCP, Filesystem MCP | allowed_directories |
| Search & Web | Brave Search MCP, Fetch MCP | -- |
| Utilities | Time MCP, Memory MCP | -- |

Browse the full catalog at **[grid.mcpstudio.qabots.diy](https://grid.mcpstudio.qabots.diy)**

## Architecture

```
                     +-- Marketplace (gh-pages) ----+
                     |  Catalog JSON + Updates       |
                     +-------------------------------+
                                  |
                                  v (hourly sync / push updates)
+----------+     +-----------+     +------------------+
| AI Agent | --> | MCP Grid  | --> | Server Instances |
|          |     | (21 tools)|     | (on-demand)      |
+----------+     +-----------+     +------------------+
                      |
                      v
               +-------------+     +------------------+
               | Management  | --> | VS Code Extension|
               | REST API    |     | (Sidebar + Dash) |
               +-------------+     +------------------+
```

## Security

- **Zero Persistence** -- Credentials and tokens are held in-memory only
- **Local First** -- All processing happens on your machine
- **No Telemetry** -- No data sent to external services
- **Encrypted Config** -- Extension config stored encrypted locally
- **Force Updates** -- Critical security patches deploy automatically
- **Resource Isolation** -- Each agent session has isolated instances

## Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `MCP_BRIDGE_DATA_DIR` | `~/.mcp-bridge` | Data directory |
| `MCP_BRIDGE_LOG_LEVEL` | `INFO` | Logging level |
| `MCP_BRIDGE_INSTANCE_MAX_TOTAL_INSTANCES` | `20` | Max concurrent instances |
| `MCP_BRIDGE_INSTANCE_IDLE_TIMEOUT_SECONDS` | `300` | Auto-shutdown after idle |
| `MCP_BRIDGE_SYSTEM_MAX_MEMORY_PERCENT` | `80` | Max memory before refusing |
| `MCP_BRIDGE_METRICS_ENABLED` | `true` | Enable metrics collection |

## Contributing

See the [Wiki](https://github.com/IB-QA/mcp-marketplace-grid-studio/wiki) for development guides, architecture docs, and contribution guidelines.

## Acknowledgements

- [Accenture/mcp-bench](https://github.com/Accenture/mcp-bench) -- Connector and server manager patterns
- [mcp-use/mcp-use](https://github.com/mcp-use/mcp-use) -- Meta-tools pattern
- [universal-mcp/universal-mcp](https://github.com/universal-mcp/universal-mcp) -- Credential store design
- [jlowin/fastmcp](https://github.com/jlowin/fastmcp) -- The framework MCP Grid is built on
- [CognitionAI/metabase-mcp-server](https://github.com/CognitionAI/metabase-mcp-server) -- Tool organization approach

## License

MIT -- see [LICENSE](LICENSE) for details.

---

Built with care by **[IB-QA](https://github.com/IB-QA)** -- Intelligent Build & Quality Automation
