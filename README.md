<p align="center">
  <img src="assets/photo.jpg" width="100%" alt="">
</p>

<p align="center">
  <img src="assets/icon.png" width="112" alt="MCP server icon">
</p>

<h1 align="center">MCP Server for AI Agents</h1>

<p align="center">
  <b>Let your AI assistant work with your repositories on <a href="https://git.skysparkle.cc">git.skysparkle.cc</a>.</b><br>
  Browse code, edit files, manage issues, pull requests and releases — straight from Claude, Cursor, VS Code and other MCP clients.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/endpoint-mcp.skysparkle.cc-0f2a33?style=for-the-badge&labelColor=1b1b1b" alt="Endpoint">
  <img src="https://img.shields.io/badge/transport-streamable_HTTP-2a7f8f?style=for-the-badge&labelColor=1b1b1b" alt="Transport">
  <img src="https://img.shields.io/badge/auth-personal_access_token-5fb3c2?style=for-the-badge&labelColor=1b1b1b" alt="Auth">
</p>

> [!TIP]
> If you find this useful, please consider giving it a ⭐ — it helps others discover the project.

## ✨ What it is

A public [Model Context Protocol](https://modelcontextprotocol.io) endpoint for this Gitea instance, powered by the official [gitea-mcp](https://gitea.com/gitea/gitea-mcp) server. Any account on git.skysparkle.cc can connect an AI agent to it.

- 🔑 **Your token, your permissions** — the server has no access of its own; every request acts as the user whose token it carries
- 🧩 **Works with any MCP client** that supports remote HTTP servers
- 🪶 **Nothing to install** — just a URL and a token

| | |
|---|---|
| **Server URL** | `https://mcp.skysparkle.cc/mcp` |
| **Auth header** | `Authorization: Bearer <your token>` |
| **Health check** | `https://mcp.skysparkle.cc/healthz` |

## 🚀 Getting started

### 1. Create an account

If you don't have one yet, [sign up](https://git.skysparkle.cc/user/sign_up) on git.skysparkle.cc.

### 2. Create an access token

1. Open **Settings → Applications → Manage Access Tokens**.
2. Name the token after the agent that will use it, for example `claude-mcp`.
3. Choose permissions. Give only what the agent needs:

   | Permission | Level | Needed for |
   |---|---|---|
   | `user` | Read | identifying you (required) |
   | `repository` | Read and Write | reading code, commits, branches, releases (Write — to commit) |
   | `issue` | Read and Write | issues, labels, milestones, comments |
   | `notification` | Read | your notifications (optional) |
   | `organization` | Read | your organizations (optional) |

   Choose **Read** everywhere if you only want the agent to look, never change.

4. Click **Generate Token** and copy it right away — Gitea shows it only once.

> [!WARNING]
> Treat the token like a password. Paste it only into your client's settings, never into a chat with the AI.

### 3. Connect your client

<details open>
<summary><b>Claude</b> — claude.ai, desktop and mobile apps</summary>

<br>

1. Open **Customize → Connectors → + Add → Add custom connector**.
2. **Name:** `Gitea` · **URL:** `https://mcp.skysparkle.cc/mcp` → **Continue**.
3. Authentication: **No sign in**.
4. Under **Request headers** add:
   - name: `Authorization`
   - value: `Bearer <your token>`
5. Click **Add**, then enable the connector in a chat via **+ → Connectors**.

The connector is linked to your Claude account, so it works on every device you use.

</details>

<details>
<summary><b>Claude Code</b></summary>

<br>

```sh
claude mcp add --transport http --scope user gitea https://mcp.skysparkle.cc/mcp \
  --header "Authorization: Bearer <your token>"
```

Check it with `claude mcp list` or `/mcp` inside a session.

</details>

<details>
<summary><b>Cursor</b></summary>

<br>

Add to `~/.cursor/mcp.json` (all projects) or `.cursor/mcp.json` (one project):

```json
{
  "mcpServers": {
    "gitea": {
      "url": "https://mcp.skysparkle.cc/mcp",
      "headers": {
        "Authorization": "Bearer <your token>"
      }
    }
  }
}
```

</details>

<details>
<summary><b>VS Code</b> — GitHub Copilot agent mode</summary>

<br>

Create `.vscode/mcp.json` in your workspace. VS Code asks for the token on first start and stores it securely, so it never lands in the file:

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "gitea_token",
      "description": "git.skysparkle.cc access token",
      "password": true
    }
  ],
  "servers": {
    "gitea": {
      "type": "http",
      "url": "https://mcp.skysparkle.cc/mcp",
      "headers": {
        "Authorization": "Bearer ${input:gitea_token}"
      }
    }
  }
}
```

</details>

<details>
<summary><b>Other clients</b></summary>

<br>

Any client that supports remote MCP servers over streamable HTTP will work. Use:

- **URL:** `https://mcp.skysparkle.cc/mcp`
- **Header:** `Authorization: Bearer <your token>`

</details>

### 4. Try it

Ask your agent something like:

> *Show me a list of my repositories on Gitea.*

If it answers with your repositories, you're all set.

## 🧰 What your agent can do

| Area | Examples |
|---|---|
| **Repositories** | list, create and fork repositories; browse the file tree |
| **Files** | read, create, edit and delete files — several files in one commit |
| **Branches & tags** | create, rename and delete branches; manage tags |
| **Commits** | history, single commits, comparisons, commit statuses |
| **Issues** | read, open, comment, close; labels and milestones |
| **Pull requests** | read, open, update and review |
| **Releases** | create, list and delete releases |
| **Actions** | workflow runs and configuration |
| **Wiki, projects, packages** | read and update |
| **Search** | users, repositories, issues, teams |

What actually works depends on the permissions of your token.

## 🛡️ Security

- Every action is performed **as you**, limited to what your token allows — you can't reach anyone else's private repositories.
- Revoke a token at any time in **Settings → Applications**. Do it immediately if a token was ever shown on a screenshot or pasted somewhere public.
- Use a **separate token per agent**, so you can revoke one without breaking the others.

##  Troubleshooting

| What you see | What it means |
|---|---|
| `Method Not Allowed` when opening the URL in a browser | Normal — `/mcp` accepts only POST requests from MCP clients. Open `/healthz` instead to check the server is up. |
| `401 Unauthorized` | The token is wrong, expired or revoked, or the header is missing the `Bearer ` prefix. |
| `502 Bad Gateway` / timeout | The server is temporarily unreachable. Wait a minute and try again. |
| The agent can read but not change anything | The token has only **Read** permissions. Create a new one with **Read and Write**. |

## 🤝 Contributing

Questions, problems and suggestions are welcome in the [issue tracker](https://git.skysparkle.cc/kalarina/Mcp-server-for-ai-agents/issues).

##  Credits

Built on [gitea-mcp](https://gitea.com/gitea/gitea-mcp) by the Gitea team, released under the MIT license.
