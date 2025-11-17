# Team Setup Checklist - Quick Reference

Use this checklist to quickly set up the MCP Prompt Saver Server.

---

## ✅ Prerequisites Check

- [ ] Python 3.10+ installed (`python --version`)
- [ ] Cursor IDE installed
- [ ] Git installed
- [ ] Access to repository (or clone URL)

---

## ✅ Step 1: Clone Repository

```bash
cd ~/Desktop  # or your preferred location
git clone <repository-url>
cd prompt-saver-mcp
```

---

## ✅ Step 2: Install Dependencies

```bash
pip install -e .
```

---

## ✅ Step 3: Get API Keys

**Choose one:**

**Option A: Shared Keys (Small Teams)**
- [ ] Get **shared API keys** from your team lead:
  - [ ] Voyage AI API key (shared)
  - [ ] OpenAI API key (shared)
  - [ ] MongoDB Connection String (shared)

**Option B: Individual Keys (Larger Teams)**
- [ ] Get your **own API keys**:
  - [ ] Voyage AI API Key - [voyageai.com](https://www.voyageai.com/)
  - [ ] OpenAI API Key - [platform.openai.com](https://platform.openai.com/api-keys)
  - [ ] MongoDB Connection String - Get from team lead or set up your own

---

## ✅ Step 4: Find Python Path

```bash
python -c "import sys; print(sys.executable)"
```

**Copy this path:** `___________________________`

---

## ✅ Step 5: Configure MCP Server

**File location:** `~/.cursor/mcp.json` (macOS/Linux) or `%APPDATA%\Cursor\mcp.json` (Windows)

**Copy this template and fill in your values:**

```json
{
  "mcpServers": {
    "prompt-saver-team": {
      "command": "YOUR_PYTHON_PATH_HERE",
      "args": ["-m", "prompt_saver_mcp.server"],
      "cwd": "YOUR_REPO_PATH_HERE",
      "env": {
        "MONGODB_URI": "YOUR_MONGODB_URI_HERE",
        "MONGODB_DATABASE": "prompt_saver",
        "MONGODB_COLLECTION": "prompts",
        "VOYAGE_AI_API_KEY": "YOUR_VOYAGE_KEY_HERE",
        "VOYAGE_AI_EMBEDDING_MODEL": "voyage-3-large",
        "OPENAI_API_KEY": "YOUR_OPENAI_KEY_HERE",
        "OPENAI_MODEL": "gpt-4o-mini",
        "PYTHONPATH": "YOUR_REPO_PATH_HERE"
      }
    }
  }
}
```

**Replace:**
- [ ] `YOUR_PYTHON_PATH_HERE` → Your Python path from Step 4
- [ ] `YOUR_REPO_PATH_HERE` → Full path to `prompt-saver-mcp` directory (use twice)
- [ ] `YOUR_MONGODB_URI_HERE` → MongoDB connection string (shared or individual)
- [ ] `YOUR_VOYAGE_KEY_HERE` → Voyage AI API key (shared from team lead OR your own)
- [ ] `YOUR_OPENAI_KEY_HERE` → OpenAI API key (shared from team lead OR your own)

---

## ✅ Step 6: Test Setup

- [ ] Test server manually: `python -m prompt_saver_mcp.server` (should show "Configuration validated successfully")
- [ ] **Restart Cursor completely** (Cmd+Q / Alt+F4, then reopen)
- [ ] In Cursor chat, ask: **"List all tools"**
- [ ] Should see MCP tools starting with `mcp_prompt-saver-team_`

---

## ✅ Step 7: Verify It Works

- [ ] Try: **"Search my prompts for test"**
- [ ] Try: **"Generate a preview of this conversation"**

---

## 🆘 Troubleshooting

**MCP server not connecting?**
- Check Python path is absolute (starts with `/`)
- Check `cwd` path is absolute
- Verify JSON syntax (no trailing commas)
- Restart Cursor after editing config

**Can't find tools?**
- Make sure you restarted Cursor
- Check Cursor Settings → MCP Servers for errors
- Verify package is installed: `pip list | grep prompt-saver`

**MongoDB connection failed?**
- Check connection string format
- Verify IP is allowed in MongoDB Atlas Network Access
- Test connection string manually

---

## 📚 Full Documentation

For detailed instructions, see: [TEAM_SETUP.md](TEAM_SETUP.md)

---

**Setup complete?** Try saving your first prompt! 🎉

