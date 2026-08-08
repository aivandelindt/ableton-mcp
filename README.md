<div align="center">

# AbletonMCP

### Connect Claude AI to Ableton Live via the Model Context Protocol

[![PyPI Downloads](https://img.shields.io/pypi/dm/ableton-mcp?style=flat-square&logo=pypi&label=PyPI%20Downloads&color=3775A9)](https://pypi.org/project/ableton-mcp/)
[![GitHub Stars](https://img.shields.io/github/stars/MCPBlender/ableton-mcp?style=flat-square&logo=github&color=yellow)](https://github.com/MCPBlender/ableton-mcp/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/MCPBlender/ableton-mcp?style=flat-square&logo=github&color=blue)](https://github.com/MCPBlender/ableton-mcp/forks)
[![License](https://img.shields.io/github/license/MCPBlender/ableton-mcp?style=flat-square&color=green)](https://github.com/MCPBlender/ableton-mcp/blob/main/LICENSE)
[![Discord](https://img.shields.io/discord/1234567890?style=flat-square&logo=discord&label=Discord&color=5865F2)](https://discord.gg/SNqPn4TcKQ)

[Discord](https://discord.gg/SNqPn4TcKQ) · [Tutorial](https://youtu.be/iJWJqyVuPS8) · [Releases](https://github.com/MCPBlender/ableton-mcp/releases)

</div>

---

AbletonMCP connects **Ableton Live** to **Claude AI** through the [Model Context Protocol (MCP)](https://modelcontextprotocol.io/), giving Claude direct, two-way control over your Live session. Create full arrangements, manipulate tracks, load instruments and effects, and compose music — all through natural language prompts.

---

## Table of Contents

- [Features](#features)
- [How It Works](#how-it-works)
- [Installation](#installation)
  - [Prerequisites](#prerequisites)
  - [1. Install the MCP Server](#1-install-the-mcp-server)
  - [2. Install the Ableton Remote Script](#2-install-the-ableton-remote-script)
  - [3. Configure Your AI Client](#3-configure-your-ai-client)
- [Usage](#usage)
- [Example Prompts](#example-prompts)
- [Troubleshooting](#troubleshooting)
- [Technical Details](#technical-details)
- [Telemetry](#telemetry)
- [Community](#community)
- [Contributing](#contributing)

---

## Features

| Feature | Description |
|---|---|
| 🎛️ **Two-way Communication** | Real-time socket bridge between Claude and Ableton Live |
| 🎹 **Track Manipulation** | Create, modify, and delete MIDI and audio tracks |
| 🎸 **Instrument & Effect Loading** | Access and load instruments, effects, and sounds from Ableton's browser |
| 📋 **Clip Creation** | Create and edit MIDI clips with full note control |
| 🎼 **Arrangement Composition** | Build complete songs in Arrangement View — intro, buildup, drop, breakdown, outro |
| ▶️ **Session Control** | Start/stop playback, fire clips, control transport in Session and Arrangement View |
| 🎚️ **Parameter Control** | Set tempo, adjust device parameters, modify mixer settings |
| 🔌 **Smithery Support** | One-command install via [Smithery](https://smithery.ai/server/@ahujasid/ableton-mcp) |

---

## How It Works

AbletonMCP has two components that work together:

1. **Ableton Remote Script** (`AbletonMCP_Remote_Script/__init__.py`) — A MIDI Remote Script installed inside Ableton Live. It opens a local TCP socket server and listens for JSON commands.
2. **MCP Server** (`MCP_Server/`) — A Python server implementing the Model Context Protocol. It connects to the Remote Script socket and exposes Ableton's capabilities as MCP tools to Claude.

```
Claude Desktop / Cursor
        │
        │  MCP (stdio/SSE)
        ▼
  MCP Server (Python)
        │
        │  TCP socket (localhost:9000)
        ▼
Ableton Remote Script
        │
        ▼
   Ableton Live
```

---

## Installation

### Prerequisites

- Ableton Live 10 or newer
- Python 3.8 or newer
- [uv](https://astral.sh/uv) package manager

Install `uv` for your platform:

```bash
# macOS (Homebrew)
brew install uv

# Windows / Linux
# See: https://docs.astral.sh/uv/getting-started/installation/
```

> ⚠️ **Do not proceed without installing `uv` first.**

---

### 1. Install the MCP Server

#### Option A — Smithery (recommended, one command)

```bash
npx -y @smithery/cli install @ahujasid/ableton-mcp --client claude
```

#### Option B — Manual (`uvx`)

No installation needed — `uvx` downloads and runs `ableton-mcp` on demand. See [client configuration](#3-configure-your-ai-client) below.

---

### 2. Install the Ableton Remote Script

> 📹 [Follow the video walkthrough](https://youtu.be/iJWJqyVuPS8)

1. Download `AbletonMCP_Remote_Script/__init__.py` from this repository.
2. Create a folder named **`AbletonMCP`** inside Ableton's MIDI Remote Scripts directory and copy the file there.

**Remote Scripts directory locations:**

<details>
<summary><strong>macOS</strong></summary>

- **Method 1 (App bundle):** Right-click Ableton Live in Applications → Show Package Contents →
  `Contents/App-Resources/MIDI Remote Scripts/`
- **Method 2 (User folder):**
  `/Users/<username>/Library/Preferences/Ableton/Live <version>/User Remote Scripts/`

</details>

<details>
<summary><strong>Windows</strong></summary>

- **Method 1:** `C:\Users\<username>\AppData\Roaming\Ableton\Live <version>\Preferences\User Remote Scripts`
- **Method 2:** `C:\ProgramData\Ableton\Live <version>\Resources\MIDI Remote Scripts\`
- **Method 3:** `C:\Program Files\Ableton\Live <version>\Resources\MIDI Remote Scripts\`

Replace `<version>` with your Ableton version number (e.g. `11`, `12`).

</details>

<details>
<summary><strong>Linux</strong></summary>

- `~/.config/ableton/Live <version>/User Remote Scripts/`

</details>

3. Launch Ableton Live.
4. Open **Settings / Preferences → Link, Tempo & MIDI**.
5. In **Control Surface**, select **AbletonMCP**.
6. Set **Input** and **Output** to **None**.

---

### 3. Configure Your AI Client

> ⚠️ Run only **one** instance of the MCP server at a time (Claude Desktop **or** Cursor, not both).

<details>
<summary><strong>Claude Desktop</strong></summary>

Open **Claude → Settings → Developer → Edit Config** (`claude_desktop_config.json`) and add:

```json
{
  "mcpServers": {
    "AbletonMCP": {
      "command": "uvx",
      "args": ["ableton-mcp"]
    }
  }
}
```

</details>

<details>
<summary><strong>Cursor</strong></summary>

Go to **Cursor Settings → MCP** and enter the following as the command:

```
uvx ableton-mcp
```

</details>

<details>
<summary><strong>VSCode (Copilot MCP)</strong></summary>

Add to your `.vscode/mcp.json`:

```json
{
  "servers": {
    "AbletonMCP": {
      "type": "stdio",
      "command": "uvx",
      "args": ["ableton-mcp"]
    }
  }
}
```

</details>

---

## Usage

1. Make sure the **AbletonMCP Remote Script** is active in Ableton Live (the Control Surface indicator should appear).
2. Open **Claude Desktop** or **Cursor** — the MCP server starts automatically.
3. Look for the 🔨 hammer icon in the chat interface — this confirms MCP tools are available.
4. Start prompting Claude to control Ableton.

---

## Example Prompts

```
"Create an 80s synthwave track"
"Build a Metro Boomin style hip-hop beat"
"Create a full song with intro, buildup, drop, breakdown, and outro"
"Add a jazz chord progression to track 1"
"Load an 808 drum rack and create a 4-bar pattern"
"Add reverb and delay to the lead synth"
"Set the tempo to 128 BPM and create 8 bars of house music"
"Get information about the current session"
"Fire the clip in track 2, scene 3"
```

> 📹 [Demo: 80s Synthwave track](https://youtu.be/VH9g66e42XA)

---

## Troubleshooting

<details>
<summary><strong>Connection issues — Claude can't reach Ableton</strong></summary>

- Confirm the **AbletonMCP** Control Surface is selected in Ableton's MIDI preferences.
- Make sure only one MCP server instance is running.
- Restart both Ableton Live and your AI client, then try again.

</details>

<details>
<summary><strong>Timeout errors on complex requests</strong></summary>

Break large tasks into smaller steps. For example, instead of "create a full track", try:
1. "Create a drum track with a basic pattern"
2. "Add a bass track with a simple riff"
3. "Add a synth lead melody"

</details>

<details>
<summary><strong>Remote Script not appearing in Control Surface list</strong></summary>

- Verify the `AbletonMCP` folder (containing `__init__.py`) is in the correct Remote Scripts directory for your OS and Ableton version.
- Restart Ableton Live after copying the files.
- Check Ableton's Log.txt for any script errors.

</details>

<details>
<summary><strong>General: still not working?</strong></summary>

1. Quit both Ableton Live and Claude Desktop / Cursor completely.
2. Restart Ableton Live and confirm the AbletonMCP Control Surface loads.
3. Restart your AI client.
4. If the issue persists, open a thread in [Discord](https://discord.gg/SNqPn4TcKQ) with your OS, Ableton version, and any error messages.

</details>

---

## Technical Details

### Communication Protocol

Commands flow as JSON objects over a local TCP socket (default port **9000**):

```json
// Request
{ "type": "create_midi_track", "params": { "index": -1 } }

// Response
{ "status": "success", "result": { "track_id": "track_0", "name": "1-MIDI" } }
```

### Limitations

- Complex arrangements may need to be broken into multiple steps.
- Designed for Ableton's built-in devices and browser content; third-party plug-in automation support varies.
- Always **save your project** before running extensive AI-driven changes.

---

## Telemetry

AbletonMCP collects **anonymous** usage data to help improve the tool:

- Which MCP tools are invoked (no content or project data)
- Session activity for active-user counts
- Aggregate error rates

No personal information, file names, audio content, or project details are ever collected.

### Opt Out

Set any of these environment variables before starting the server:

```bash
export ABLETON_MCP_DISABLE_TELEMETRY=true
```

Or in your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "AbletonMCP": {
      "command": "uvx",
      "args": ["ableton-mcp"],
      "env": {
        "ABLETON_MCP_DISABLE_TELEMETRY": "true"
      }
    }
  }
}
```

---

## Community

| | |
|---|---|
| 💬 **Discord** | [Join the server](https://discord.gg/SNqPn4TcKQ) — share projects, get help, and give feedback |
| 🐛 **Bug Reports** | [Open an issue](https://github.com/MCPBlender/ableton-mcp/issues) |
| 💡 **Feature Requests** | [Start a discussion](https://github.com/MCPBlender/ableton-mcp/discussions) |
| ⭐ **Stay Updated** | [Star the repo](https://github.com/MCPBlender/ableton-mcp) to follow releases |

---

## Contributing

Contributions are welcome! To get started:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m "feat: add my feature"`
4. Push and open a Pull Request

Please open an issue first for substantial changes so we can discuss the approach.

---

<div align="center">

[MCPBlender](https://github.com/MCPBlender) · Not affiliated with Ableton AG

</div>
