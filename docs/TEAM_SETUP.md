# Team Setup Guide - MCP Prompt Saver Server

Quick setup guide for teams using **shared API keys** and installing directly from GitHub.

---

## 👥 For Team Leads: Setting Up Shared API Keys

### 1. Get the API Keys

**Voyage AI:**
1. Go to [https://www.voyageai.com/](https://www.voyageai.com/)
2. Sign up for an account (free tier available)
3. Navigate to API keys section → Copy your API key

**OpenAI:**
1. Go to [https://platform.openai.com/api-keys](https://platform.openai.com/api-keys)
2. Sign up for an account (free credits available)
3. Click "Create new secret key" → Copy immediately

**MongoDB Atlas:**
1. Set up a MongoDB Atlas cluster (free tier available)
2. Create a database user
3. Configure network access (allow from anywhere)
4. Get the connection string
5. Set up the vector search index (see Step 3 below)

### 2. Share Keys Securely

**Recommended methods:**
- ✅ Password Manager (1Password, LastPass, Bitwarden)
- ✅ Encrypted Team Chat (Slack private channel, Signal)
- ✅ Secure Document (Google Drive with limited access)

**What to share:**
- Voyage AI API key
- OpenAI API key
- MongoDB connection string

### 3. Set Up MongoDB Vector Search Index

1. In MongoDB Atlas, go to your cluster
2. Click "Browse Collections"
3. Create database `prompt_saver` and collection `prompts` (if needed)
4. Go to "Search" tab → "Create Search Index" → "JSON Editor"
5. Paste this configuration:

```json
{
  "fields": [
    {
      "type": "vector",
      "path": "embedding",
      "numDimensions": 1024,
      "similarity": "dotProduct"
    }
  ]
}
```

6. Name: `vector_index`, Database: `prompt_saver`, Collection: `prompts`
7. Click "Create Search Index" and wait for it to build

---

## Prerequisites

- ✅ Python 3.10+ installed
- ✅ Cursor IDE installed
- ✅ Git installed (for pip install from GitHub)
- ✅ Shared API keys from your team lead

---

## Step 1: Install from GitHub

```bash
pip install git+https://github.com/YOUR-ORG/prompt-saver-mcp.git
```

**Replace `YOUR-ORG`** with your actual GitHub organization/username.

This installs all dependencies and makes the package available system-wide.

---

## Step 2: Find Your Python Path

```bash
python -c "import sys; print(sys.executable)"
```

**Save this path** - you'll need it in Step 3.

**Example output:**
```
/Users/yourname/.pyenv/versions/3.11.14/bin/python
```

---

## Step 3: Configure MCP Server in Cursor

### 3.1 Create MCP Config File

**File location:**
- **macOS/Linux:** `~/.cursor/mcp.json`
- **Windows:** `%APPDATA%\Cursor\mcp.json`

```bash
# macOS/Linux
mkdir -p ~/.cursor
touch ~/.cursor/mcp.json
```

### 3.2 Add Configuration

Edit `~/.cursor/mcp.json` and add:

```json
{
  "mcpServers": {
    "prompt-saver-team": {
      "command": "/PATH/TO/YOUR/PYTHON",
      "args": ["-m", "prompt_saver_mcp.server"],
      "env": {
        "MONGODB_URI": "mongodb+srv://username:password@cluster.mongodb.net/",
        "MONGODB_DATABASE": "prompt_saver",
        "MONGODB_COLLECTION": "prompts",
        "VOYAGE_AI_API_KEY": "your_voyage_ai_api_key_here",
        "VOYAGE_AI_EMBEDDING_MODEL": "voyage-3-large",
        "OPENAI_API_KEY": "your_openai_api_key_here",
        "OPENAI_MODEL": "gpt-4o-mini"
      }
    }
  }
}
```

### 3.3 Replace Placeholders

1. **`/PATH/TO/YOUR/PYTHON`** → Your Python path from Step 2
2. **`mongodb+srv://...`** → MongoDB connection string from team lead
3. **`your_voyage_ai_api_key_here`** → Shared Voyage AI key from team lead
4. **`your_openai_api_key_here`** → Shared OpenAI key from team lead

**Example:**
```json
{
  "mcpServers": {
    "prompt-saver-team": {
      "command": "/Users/john/.pyenv/versions/3.11.14/bin/python",
      "args": ["-m", "prompt_saver_mcp.server"],
      "env": {
        "MONGODB_URI": "mongodb+srv://team-user:password@promptdb.6qjw6ob.mongodb.net/",
        "MONGODB_DATABASE": "prompt_saver",
        "MONGODB_COLLECTION": "prompts",
        "VOYAGE_AI_API_KEY": "pa-abc123xyz789",
        "VOYAGE_AI_EMBEDDING_MODEL": "voyage-3-large",
        "OPENAI_API_KEY": "sk-proj-abc123xyz789",
        "OPENAI_MODEL": "gpt-4o-mini"
      }
    }
  }
}
```

---

## Step 4: Test Setup

### 4.1 Test Server Manually

```bash
python -m prompt_saver_mcp.server
```

Should see: `Configuration validated successfully`

Press `Ctrl+C` to stop.

### 4.2 Restart Cursor

**Important:** After editing `~/.cursor/mcp.json`, restart Cursor completely:
1. Quit Cursor (Cmd+Q / Alt+F4)
2. Reopen Cursor

### 4.3 Verify Connection

In Cursor chat, ask: **"List all tools"**

You should see MCP tools like:
- `mcp_prompt-saver-team_save_prompt`
- `mcp_prompt-saver-team_search_prompts`
- etc.

If you see these, **you're all set!** 🎉

---

## Step 5: Test It Works

Try these commands in Cursor chat:

- **"Search my prompts for [topic]"**
- **"Generate a preview of this conversation"**
- **"Save this previewed prompt"**

---

## Troubleshooting

### MCP server not connecting

- ✅ Check Python path is absolute (starts with `/`)
- ✅ Verify package installed: `pip list | grep prompt-saver`
- ✅ Check JSON syntax (no trailing commas)
- ✅ Restart Cursor after editing config
- ✅ Check Cursor Settings → MCP Servers for errors

### "Failed to connect to MongoDB"

- ✅ Check MongoDB URI format (should start with `mongodb+srv://`)
- ✅ Verify IP is allowed in MongoDB Atlas Network Access
- ✅ Test connection string manually

### "API_KEY is required"

- ✅ Check API keys are correct in `~/.cursor/mcp.json`
- ✅ Remove any spaces before/after keys
- ✅ Verify keys are active (check dashboards)

### "Module not found"

- ✅ Reinstall: `pip install git+https://github.com/YOUR-ORG/prompt-saver-mcp.git`
- ✅ Verify Python version: `python --version` (should be 3.10+)
- ✅ Check you're using the correct Python interpreter

---

## Quick Reference

**Install:**
```bash
pip install git+https://github.com/YOUR-ORG/prompt-saver-mcp.git
```

**Config file:** `~/.cursor/mcp.json`

**Test server:**
```bash
python -m prompt_saver_mcp.server
```

**Update package:**
```bash
pip install --upgrade git+https://github.com/YOUR-ORG/prompt-saver-mcp.git
```

---

## Team Best Practices

1. **Use consistent server name** - Agree on `prompt-saver-team` (or your convention)
2. **Share credentials securely** - Use password manager or encrypted channel
3. **Monitor API usage** - Watch for rate limits with shared keys
4. **Rotate keys periodically** - Every 3-6 months for security
5. **Keep API keys private** - Never commit to Git repositories

---

**Need help?** Ask your team lead or check [TEAM_SETUP_CHECKLIST.md](TEAM_SETUP_CHECKLIST.md) for quick reference.
