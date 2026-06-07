# SEQTA MCP Server

A local MCP (Model Context Protocol) server that connects Claude to your [SEQTA Learn](https://seqta.com.au) student portal. Ask Claude about your timetable, assessments, messages, notices, and more — all pulled live from SEQTA.

Built for **Trinity Anglican College** (Perth, WA) but should work for any SEQTA school using Microsoft SSO login.

---

## Features

- 🗓️ **Timetable** — what's on today/this week
- 📝 **Assessments** — upcoming tasks and past marks
- 📢 **Notices** — daily school notices
- 💬 **Messages** — read your inbox
- 📚 **Course content** — files and lesson content per subject
- 📊 **Reports** — student report cards
- 🏠 **Homework** — dashboard homework summary
- 🔄 **Auto-refresh** — re-authenticates every 10 minutes automatically

---

## Requirements

- Python 3.8+
- A SEQTA Learn account (with Microsoft SSO login)
- [Claude Desktop](https://claude.ai/download)

Install dependencies:

```bash
pip install mcp requests beautifulsoup4
```

---

## Setup

### 1. Clone the repo

```bash
git clone https://github.com/yourusername/seqta-mcp.git
cd seqta-mcp
```

### 2. Add your credentials

Open `seqta_mcp.py` and edit the three lines at the top:

```python
SEQTA_BASE_URL = "https://students.yourschool.edu.au"
SEQTA_EMAIL    = "you@students.yourschool.edu.au"
SEQTA_PASSWORD = "yourpassword"
```

Also update `STUDENT_ID` with your numeric student ID (visible in SEQTA network requests or ask your school IT).

### 3. Register with Claude Desktop

Add this to your `claude_desktop_config.json`
(found at `C:\Users\<you>\AppData\Roaming\Claude\claude_desktop_config.json` on Windows,
or `~/Library/Application Support/Claude/claude_desktop_config.json` on Mac):

```json
{
  "mcpServers": {
    "seqta": {
      "command": "python",
      "args": ["C:\\full\\path\\to\\seqta_mcp.py"]
    }
  }
}
```

### 4. Restart Claude Desktop

SEQTA will appear as a connector under the **Desktop** section. You're ready to go!

---

## Usage

Just talk to Claude naturally:

> *"What's on my timetable this week?"*
> *"Do I have any upcoming assessments?"*
> *"Show me today's school notices"*
> *"Read my latest messages"*
> *"What homework do I have?"*
> *"How did I go in my English assessments?"*

---

## Files

| File | Description |
|---|---|
| `seqta_mcp.py` | The MCP server — run this |
| `fetch-info.py` | Standalone CLI tool to fetch and save SEQTA data as JSON |

### fetch-info.py (CLI tool)

Edit the same three credential lines at the top, then:

```bash
python fetch-info.py timetable
python fetch-info.py notices
python fetch-info.py assessments-upcoming
python fetch-info.py course-english
python fetch-info.py messages-list
python fetch-info.py all          # fetches everything → saves ~15 JSON files
```

---

## How it works

SEQTA uses Microsoft SSO (SAML2 / Azure AD) for authentication. The server automates the full browser login flow headlessly using `requests` and `beautifulsoup4`, extracts the `JSESSIONID` session cookie, and uses it for all subsequent API calls. The token is automatically refreshed every 10 minutes in the background.

---

## Notes

- **School network:** SEQTA may block requests from outside the school network or VPN depending on your school's configuration.
- **MFA:** If your school account has MFA enabled this won't work — it relies on email + password only.
- **Session ID:** A `.session` file is saved as a fallback cache in case re-auth fails.
- **Microsoft SSO tenant:** The tenant ID is hardcoded for Trinity Anglican College (`5f441b77-...`). If your school uses a different Microsoft tenant you'll need to update `TENANT_ID` in the script.

---

## Disclaimer

This project is not affiliated with or endorsed by SEQTA Software or any school. Use responsibly and in accordance with your school's acceptable use policy. Keep your credentials safe — never commit them to a public repo.

> ⚠️ **Never hardcode your password in a file you push to GitHub.** Consider using environment variables or a `.env` file (add it to `.gitignore`) before making the repo public.
