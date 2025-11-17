# Team Setup Checklist - Quick Reference

Quick checklist for setting up MCP Prompt Saver Server with **shared API keys**.

---

## ✅ Prerequisites

- [ ] Python 3.10+ installed (`python --version`)
- [ ] Cursor IDE installed
- [ ] Git installed
- [ ] Get **shared API keys** from team lead:
  - [ ] Voyage AI API key
  - [ ] OpenAI API key
  - [ ] MongoDB connection string

---

## ✅ Step 1: Install from GitHub

```bash
pip install git+https://github.com/YOUR-ORG/prompt-saver-mcp.git
```

**Replace `YOUR-ORG`** with your GitHub organization/username.

---

## ✅ Step 2: Find Python Path

```bash
python -c "import sys; print(sys.executable)"
```

**Copy this path:** `___________________________`

---

## ✅ Step 3: Configure MCP Server

**File:** `~/.cursor/mcp.json` (macOS/Linux) or `%APPDATA%\Cursor\mcp.json` (Windows)

**Create file and add:**

```json
{
  "mcpServers": {
    "prompt-saver-team": {
      "command": "YOUR_PYTHON_PATH_HERE",
      "args": ["-m", "prompt_saver_mcp.server"],
      "env": {
        "MONGODB_URI": "YOUR_MONGODB_URI_HERE",
        "MONGODB_DATABASE": "prompt_saver",
        "MONGODB_COLLECTION": "prompts",
        "VOYAGE_AI_API_KEY": "YOUR_VOYAGE_KEY_HERE",
        "VOYAGE_AI_EMBEDDING_MODEL": "voyage-3-large",
        "OPENAI_API_KEY": "YOUR_OPENAI_KEY_HERE",
        "OPENAI_MODEL": "gpt-4o-mini"
      }
    }
  }
}
```

**Replace:**
- [ ] `YOUR_PYTHON_PATH_HERE` → Your Python path from Step 2
- [ ] `YOUR_MONGODB_URI_HERE` → Shared MongoDB connection string
- [ ] `YOUR_VOYAGE_KEY_HERE` → Shared Voyage AI API key
- [ ] `YOUR_OPENAI_KEY_HERE` → Shared OpenAI API key

---

## ✅ Step 4: Test Setup

- [ ] Test server: `python -m prompt_saver_mcp.server` (should show "Configuration validated successfully")
- [ ] **Restart Cursor completely** (Cmd+Q / Alt+F4, then reopen)
- [ ] In Cursor chat, ask: **"List all tools"**
- [ ] Should see MCP tools starting with `mcp_prompt-saver-team_`

---

## ✅ Step 5: Verify It Works

- [ ] Try: **"Search my prompts for test"**
- [ ] Try: **"Generate a preview of this conversation"**

---

## 🆘 Troubleshooting

**MCP server not connecting?**
- Check Python path is absolute (starts with `/`)
- Verify package installed: `pip list | grep prompt-saver`
- Check JSON syntax (no trailing commas)
- Restart Cursor after editing config

**Can't find tools?**
- Make sure you restarted Cursor
- Check Cursor Settings → MCP Servers for errors

**MongoDB connection failed?**
- Check connection string format
- Verify IP is allowed in MongoDB Atlas Network Access

**API key errors?**
- Check keys are correct (no extra spaces)
- Verify keys are active

---

## 📚 Full Documentation

For detailed instructions, see: [TEAM_SETUP.md](TEAM_SETUP.md)

---

**Setup complete?** Try saving your first prompt! 🎉
