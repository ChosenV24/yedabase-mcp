<p align="center"><img src="assets/icon.png" width="96" height="96" alt="YedaBase"></p>

# YedaBase MCP server

YedaBase is an investor database for fundraising and investor research. It covers family offices, venture capital and startup investors, funds of funds, principal and permanent-capital investors, and registered investment advisers (RIAs), with decision-makers, investment focus, philanthropy, news and events. Family office records are researched and reviewed by the YedaBase team; every other record carries its inclusion evidence and source link. The YedaBase MCP server lets AI assistants search and open these records for you, with the same permissions you have on [yedabase.com](https://yedabase.com). It also carries the YedaBase Agent: playbooks your AI follows for shortlists, research, the way in to a firm, outreach and pipeline, and for matching and enriching your own investor list on your machine. YedaBase enriches your data; your data never enriches YedaBase.

This repository is the public listing for the server: connection steps, tools and support. It contains no server code.

## Server

| | |
| --- | --- |
| URL | `https://yedabase.com/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth 2.1 sign-in with a YedaBase account (PKCE S256, dynamic client registration). No API key to paste. |
| Registry name | `com.yedabase/yedabase` ([server.json](server.json)) |

Any YedaBase account can connect. When you connect, your client opens the YedaBase sign-in page: you sign in with the code we email you and approve what the client may do. On the annual plan you choose the workspace and the scopes (dataset, contacts, and your lists and notes, which the agent can also save to when you ask). You can revoke a connection at any time in YedaBase under Settings > API keys & MCP.

## Tools

Version 1.1.0 (15 Oct 2026) adds the YedaBase Agent: playbooks, resources, help, an account status tool and, on the annual plan, tools that save to your own workspace.

| Name | Title | Read-only | What it returns |
| --- | --- | --- | --- |
| `search_investors` | Search investors | Yes | Firms matching structured filters (category, type, region, country, domains, stage, `updated_since` and more), 25 per page, with decision-maker counts, upcoming events and the suggested route. On the free preview: teasers. |
| `get_investor` | Open an investor | Yes | One firm's full record: overview, inclusion evidence, category fields, decision-makers with contacts under the contacts-restricted rule, philanthropy, news, events, sources, data date and the suggested route (with its evidence and next action). On the free preview: the teaser unless the firm is unlocked. |
| `get_angles` | Conversation angles | Yes | Possible conversation angles for one firm: shared events and podcasts, shared causes, similar investors. Never relationships. |
| `list_fields` | Fields and categories | Yes | Investor categories, their types, field definitions, allowed values and the rules for placeholders and restricted contacts. |
| `get_person` | Open a decision-maker | Yes | One decision-maker with their firm (annual plan). |
| `get_list` | Open a list | Yes | One of your lists with its firms, each firm's status line and mandate status (annual plan, workspace scope). |
| `my_lists` | My lists | Yes | Your lists (annual plan, workspace scope). |
| `my_notes` | My notes | Yes | Notes you may read in your workspace (annual plan, workspace scope). |
| `list_playbooks` | List playbooks | Yes | The seven YedaBase playbooks (Start, Match & Enrich, Investor Shortlist, Investor Research, Way In, Reach Out, Pipeline). On the free preview: titles and descriptions, locked. |
| `get_playbook` | Open a playbook | Yes | One playbook as markdown (annual plan); on the free preview, its title and description with `locked: true`. |
| `get_resource` | Read a resource | Yes | The resources below, inline: CSV in pages of 2,000 rows, JSON and markdown whole. |
| `get_help` | Help library | Yes | One help topic (sign-in, connecting each client, the export pool, daily caps, sending limits and more), or the topic list. |
| `yeda_status` | Account and session status | Yes | Tier, seat, onboarding notes, lists, the workspace export pool, calls used today, data dates, the last tool error and tracked items. |
| `save_to_list` | Save firms to a list | No (adds to your list; idempotent) | Adds firms to one of your lists by name, creating it when missing; returns added and already present (annual plan, workspace scope). |
| `add_note` | Add a firm note | No (adds a note; idempotent) | A note on one firm, signed by you and shared with your workspace, up to 2,000 characters (annual plan, workspace scope). |
| `workspace_note` | Workspace note | No (replaces that note; idempotent) | Reads or replaces one note with a fixed name: about-us, connectors, events, team (shared); voice, yb-preferences, send-log (per person) (annual plan, workspace scope). |
| `export_list` | Export a list | No (debits the export pool once per list per day; idempotent) | A list in the 66-column export shape plus the suggested route, 200 rows per page; up to 300 contacts per export (annual plan, workspace scope). |
| `unlock_investor` | Unlock an investor (free preview) | No (spends one of 10 preview unlocks; idempotent) | Opens one firm's full record on the free preview and returns the unlocks left. |

Every tool works on YedaBase's own dataset and your own workspace only (`openWorldHint: false`). No tool deletes anything, and no tool changes YedaBase records; the write tools change only your own lists and notes, on your request.

## Resources

| URI | Type | What it holds |
| --- | --- | --- |
| `yedabase://index` | text/csv | Hashed firm index: sha256 of each live firm's root domain and name slug, with counts, flags and `data_version`. Used to match your own list on your machine; nothing in it is a record. |
| `yedabase://adv-cache` | text/csv | Public SEC Form ADV extract, refreshed quarterly, with its as-of date. |
| `yedabase://vocabulary` | application/json | Field definitions, allowed values, one inclusion sentence per category and fit terms. |
| `yedabase://help` | text/markdown | The YedaBase help library, one section per topic. |
| `yedabase://playbooks/<name>` | text/markdown | The seven playbooks (annual plan). |

The first four are open to every connection, the free preview included.

## Connect from

### Claude

1. In Claude, open **Settings > Connectors** and choose **Add custom connector**.
2. Name it YedaBase and paste the URL `https://yedabase.com/mcp`.
3. Choose **Connect**, sign in to YedaBase with the code we email you, and approve the connection.
4. Ask Claude about investors, for example "Find single family offices in Texas that invest in healthcare", or say "Hi YB" to start with the YedaBase Agent.

On Claude Team and Enterprise plans an owner may need to add the connector for the organisation first.

### ChatGPT

1. In ChatGPT, open **Settings > Apps & Connectors**. If YedaBase is listed there, choose it and select **Connect**, then skip to step 4.
2. To add it by URL: under **Advanced settings**, turn on **Developer mode**, then choose **Create**.
3. Enter the name YedaBase, the MCP server URL `https://yedabase.com/mcp` and the authentication **OAuth**, then create it.
4. When ChatGPT opens the YedaBase sign-in page, sign in with the code we email you and approve the connection.
5. In a chat, pick YedaBase from the tools menu and ask about investors.

Developer mode is offered on the ChatGPT plans where OpenAI makes it available; in a business workspace an admin may need to allow it.

### Gemini (Gemini CLI)

1. Add YedaBase to `~/.gemini/settings.json` (or a project's `.gemini/settings.json`):

   ```json
   {
     "mcpServers": {
       "yedabase": { "httpUrl": "https://yedabase.com/mcp" }
     }
   }
   ```

2. Start `gemini` and run `/mcp auth yedabase`.
3. In the browser window that opens, sign in to YedaBase with the code we email you, and approve the connection.
4. Run `/mcp` to check that YedaBase is connected, then ask about investors.

### Mistral Le Chat

1. In Le Chat, open **Intelligence > Connectors** and choose **Add connector**.
2. Choose **Custom MCP connector**, name it YedaBase and enter the server URL `https://yedabase.com/mcp`.
3. Choose **Connect**, sign in to YedaBase with the code we email you, and approve the connection.
4. In a chat, turn on YedaBase in the tools menu and ask about investors.

In a Le Chat organisation an admin may need to add the connector first.

### Perplexity

1. In Perplexity, open **Settings > Connectors** and choose to add a custom connector (a remote MCP server).
2. Name it YedaBase, enter the URL `https://yedabase.com/mcp` and choose OAuth if Perplexity asks for an authentication type.
3. Choose **Connect**, sign in to YedaBase with the code we email you, and approve the connection.
4. Ask about investors in a thread with the YedaBase connector turned on.

Custom connectors are available on the Perplexity plans and apps where Perplexity offers them.

### Cursor

One click: [Add to Cursor](https://cursor.com/en/install-mcp?name=yedabase&config=eyJ1cmwiOiJodHRwczovL3llZGFiYXNlLmNvbS9tY3AifQ%3D%3D) (opens Cursor), or paste this deeplink in your browser:

```
cursor://anysphere.cursor-deeplink/mcp/install?name=yedabase&config=eyJ1cmwiOiJodHRwczovL3llZGFiYXNlLmNvbS9tY3AifQ==
```

Or add it to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "yedabase": { "url": "https://yedabase.com/mcp" }
  }
}
```

Then open **Cursor Settings > MCP**, choose **Connect** next to YedaBase, sign in to YedaBase and approve the connection. Ask about investors in the Agent chat.

### VS Code

One click: [Install in VS Code](https://vscode.dev/redirect/mcp/install?name=yedabase&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fyedabase.com%2Fmcp%22%7D) (opens VS Code), or paste this link in your browser:

```
vscode:mcp/install?%7B%22name%22%3A%22yedabase%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fyedabase.com%2Fmcp%22%7D
```

Or run **MCP: Add Server** from the Command Palette, choose **HTTP** and paste `https://yedabase.com/mcp`. For one project, add `.vscode/mcp.json`:

```json
{
  "servers": {
    "yedabase": { "type": "http", "url": "https://yedabase.com/mcp" }
  }
}
```

Start the server; when VS Code asks to sign in, sign in to YedaBase and approve the connection. Ask about investors in Copilot Chat in agent mode.

### Windsurf

Open **Windsurf Settings > Cascade > MCP servers** and add a custom server, or edit `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "yedabase": { "serverUrl": "https://yedabase.com/mcp" }
  }
}
```

Refresh the MCP servers, sign in to YedaBase when Windsurf asks, approve the connection, and ask Cascade about investors.

## Free preview and full access

| | Free preview | Annual plan |
| --- | --- | --- |
| Search | The whole database, up to 50 rows per search, with region and country filters | The whole database with every filter, including state and city |
| Teasers | Name, category, type, region, country, a short teaser and availability indicators for every firm | Every firm opens as a full record |
| Unlocks | 10 firms in full, shared between the site and AI clients | Not needed: every record is open |
| Full records | The 10 firms you unlock | Every firm |
| Contacts | Decision-maker names and positions in the firms you unlock | Names, positions, work emails and LinkedIn where available, under the contacts-restricted rule |
| Lists and notes | Not included | Included, with the workspace scope: read, and saved to on your request |
| Exports | Not included | Excel and CSV on yedabase.com and `export_list` in your AI client, from a workspace pool of 1,000 firm rows per seat per calendar month, shared by the team |
| YedaBase Agent | Search, teasers, help and your account status | Research, lists, firm notes, workspace notes and list exports, each step on your approval |
| Playbooks | Titles and descriptions | All seven in full |
| Resources | Hashed index, SEC Form ADV extract, vocabulary, help | The same, plus the playbooks |
| Calls per day | 100 a day, 20 a minute | 500 a day per person, 60 a minute (REST and MCP together) |

Firms in the EU-27, Iceland, Liechtenstein, Norway, the United Kingdom, Switzerland, Israel, Canada, Brazil and Japan show decision-maker names and positions only, on every plan. Plan details: [yedabase.com/plans](https://yedabase.com/plans).

## Links

- MCP server overview: [yedabase.com/mcp](https://yedabase.com/mcp)
- Documentation: [API and MCP help](https://yedabase.com/help/api-and-mcp)
- Privacy policy: [yedabase.com/privacy](https://yedabase.com/privacy)
- Terms of service: [yedabase.com/terms](https://yedabase.com/terms)
- Support: [t@yedabase.com](mailto:t@yedabase.com) or [open an issue](../../issues/new/choose)

© Chosen V Inc. YedaBase is operated by Chosen V Inc.
