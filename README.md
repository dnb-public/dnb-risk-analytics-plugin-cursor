# D&B Risk Analytics MCP Server — Quick Start Guide

## Welcome

Thank you for choosing Dun & Bradstreet! This document will help you set up and authenticate into the D&B Risk Analytics MCP server.

For inquiries, please contact CursorLeads@dnb.com.

---

## What This Plugin Does

This plugin connects Cursor to your D&B Risk Analytics portfolio via the D&B Risk Analytics MCP Server.

## Prerequisites for Access

- **Cursor 2.5+**

---

## Install

### Step 1 — Generate API Credentials
In your Risk Analytics workspace, go to **Admin → Integrations → API** and generate a Client ID and Client Secret.
### Step 2 — Set Environment Variables
Set the following environment variables on your system before launching Cursor:
| Variable | Value |
|---|---|
| `DNB_RA_MCP_CLIENT_ID` | Your Client ID |
| `DNB_RA_MCP_CLIENT_SECRET` | Your Client Secret |

**macOS/Linux** (`~/.zshrc` or `~/.bashrc`):
```bash
export DNB_RA_MCP_CLIENT_ID=your_client_id
export DNB_RA_MCP_CLIENT_SECRET=your_client_secret
```

**Windows** (System Properties → Environment Variables or PowerShell):
```powershell
[System.Environment]::SetEnvironmentVariable("DNB_RA_MCP_CLIENT_ID","your_client_id","User")
[System.Environment]::SetEnvironmentVariable("DNB_RA_MCP_CLIENT_SECRET","your_client_secret","User")
```

### Step 3 — Install the Plugin
- Open Cursor Settings (Cmd+, / Ctrl+,)
- Go to Plugins
- Search for dnb-risk-analytics-plugin-cursor
- Click Install
- Restart Cursor (or reload the window via Ctrl+Shift+P → Developer: Reload Window) so the environment variables are loaded.

### Step 4 — Connect and Authenticate
After reloading Cursor, the plugin will automatically attempt to connect to the D&B Risk Analytics MCP Server. A browser window will open and redirect you to the D&B Risk Analytics login page. Sign in with your **Risk Analytics email address** to complete the connection.

To verify the connection, open Cursor Settings (Ctrl+Shift+J / Cmd+Shift+J) → Tools & MCP and confirm the dnb-risk-analytics server shows as enabled.

If you encounter issues, open the Output panel (Ctrl+Shift+U / Cmd+Shift+U) and select MCP Logs from the dropdown for details.

---

## What is the D&B Risk Analytics MCP Server?

The D&B Risk Analytics MCP server leverages the standardized MCP client-server architecture to provide AI and automation applications with secure, scalable access to trusted, comprehensive business data in D&B Risk Analytics. This enables intelligent systems—such as AI assistants, agents, and enterprise applications—to seamlessly integrate with the D&B Risk Analytics platform for enhanced decision-making and contextual insights.

Through this architecture, D&B Risk Analytics capabilities become an intelligent extension of your development environment or AI application, delivering real-time access to risk insights and compliance data. By standardizing connectivity, the MCP server helps ensure interoperability, security, and performance across diverse AI ecosystems.

**The D&B Risk Analytics MCP server exposes a Risk Analytics Agent as a Tool capable of executing both read and write operations across a wide variety of use cases:**

- Agents able to access detailed company insights, including firmographic data, financial risk indicators, predictive risk indicators and much more, all powered by D&B's comprehensive data.
- Agents able to provide concise summaries of thousands of data points, for more efficient risk management and analysis.
- Agents able to provide deep insight on beneficial ownership structures and corporate linkage family trees.
- Agents able to perform actionable tasks, including: adding entities to portfolio, adding and updating users, initiating screenings, assigning custom fields, assigning tags, and more.

---

## Authentication

This plugin uses OAuth 2.1 with PKCE. Authentication is handled automatically by Cursor — no manual setup is required beyond setting your environment variables in Step 2.

For developers building direct integrations (outside of Cursor), see the full [D&B Risk Analytics MCP Authentication Guide](https://view.highspot.com/viewer/7db8f86177a2380fe790e5153100cb2f).

---

## Customer Support

For information on the tools, domain, data blocks, and prompt examples, see documentation [here](https://view.highspot.com/viewer/c30512264f4ef2ef3c481ddb5bdd8c97#1).

If you have any issues during onboarding, you may contact D&B Customer Service:

| Region | Contact |
|--------|---------|
| US | 1.800.234.3867 or open a case via [Digital Service Center](https://support.dnb.com) |
| CA | 1-800-463-6362 |
| UK & Ireland | 44(0)845 145 1700 |
| AsiaPac | 91 114 149 7974 |
| China | 86 21 23213775 |

Worldwide Network alliance members, please submit inquiries using the [support page](https://www.dnb.com/utility-pages/contact-us.html).

For all other markets, please visit [Choose your country](https://www.dnb.com/utility-pages/contact-us.html).

---

## D&B Trust Centre

For information on D&B's security and privacy practices, please visit the [D&B Trust Centre](https://www.dnb.com/utility-pages/trust-center.html).

---