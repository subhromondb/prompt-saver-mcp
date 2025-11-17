# Team Setup Guide - MCP Prompt Saver Server

This guide will help your teammates set up the MCP Prompt Saver Server on their local machines so they can use it in Cursor IDE.

---

## Prerequisites

Before starting, make sure you have:
- ✅ **Python 3.10 or higher** installed
- ✅ **Cursor IDE** installed
- ✅ **Git** installed
- ✅ Access to the **prompt-saver-mcp** repository (or clone it)
- ✅ **MongoDB Atlas** credentials (shared team database or individual account)
- ✅ **Voyage AI API key** (each teammate needs their own)
- ✅ **OpenAI API key** (each teammate needs their own)

---

## Step 1: Clone the Repository

```bash
# Navigate to where you want to clone the repo
cd ~/Desktop  # or wherever you prefer

# Clone the repository
git clone <repository-url>
cd prompt-saver-mcp
```

**Note:** Replace `<repository-url>` with your actual repository URL.

---

## Step 2: Install Python Dependencies

```bash
# Make sure you're in the prompt-saver-mcp directory
cd prompt-saver-mcp

# Install the package in editable mode
pip install -e .
```

This installs all required dependencies (`mcp`, `pymongo`, `voyageai`, `openai`, `python-dotenv`, `pydantic`) and makes the package available system-wide.

---

## Step 3: Get Your API Keys

### 3.1 Voyage AI API Key (Required - Individual)

Each teammate needs their own Voyage AI API key:

1. Go to [https://www.voyageai.com/](https://www.voyageai.com/)
2. Sign up for a free account
3. Navigate to your dashboard/API keys section
4. Copy your API key
5. **Save it** - you'll need it in Step 5

### 3.2 OpenAI API Key (Required - Individual)

Each teammate needs their own OpenAI API key:

1. Go to [https://platform.openai.com/](https://platform.openai.com/)
2. Sign up for an account (you'll get free credits)
3. Go to API Keys: [https://platform.openai.com/api-keys](https://platform.openai.com/api-keys)
4. Click "Create new secret key"
5. Copy the key immediately (you won't see it again!)
6. **Save it** - you'll need it in Step 5

### 3.3 MongoDB Atlas (Shared or Individual)

**Option A: Using Shared Team Database (Recommended)**

If your team shares a MongoDB Atlas database:
- Ask your team lead for the MongoDB connection string
- It will look like: `mongodb+srv://username:password@cluster.mongodb.net/`
- **Save it** - you'll need it in Step 5

**Option B: Individual MongoDB Atlas Setup**

If each teammate needs their own database:

1. Go to [https://www.mongodb.com/cloud/atlas](https://www.mongodb.com/cloud/atlas)
2. Sign up for a free account
3. Create a new cluster (free M0 tier)
4. Create a database user (Database Access → Add New Database User)
5. Configure network access (Network Access → Allow Access from Anywhere)
6. Get your connection string (Database → Connect → Connect your application)
7. Create the vector search index (see Step 4)

---

## Step 4: Set Up MongoDB Vector Search Index (If Using Individual Database)

**Skip this step if using a shared team database** (it should already be set up).

If you're setting up your own MongoDB Atlas:

1. In MongoDB Atlas, go to your cluster
2. Click "Browse Collections"
3. Create a database named `prompt_saver` (if it doesn't exist)
4. Create a collection named `prompts` (if it doesn't exist)
5. Go to the "Search" tab (next to "Collections")
6. Click "Create Search Index"
7. Choose "Vector Search" (or "JSON Editor")
8. Paste this configuration:

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

9. Name the index: `vector_index`
10. Select the database: `prompt_saver`
11. Select the collection: `prompts`
12. Click "Next" and then "Create Search Index"
13. Wait for the index to finish building (may take a few minutes)

---

## Step 5: Find Your Python Path

You need to know the full path to your Python executable:

```bash
# Run this command to find your Python path
python -c "import sys; print(sys.executable)"
```

**Example output:**
```
/Users/yourname/.pyenv/versions/3.11.14/bin/python
```

**Save this path** - you'll need it in Step 6.

---

## Step 6: Configure MCP Server in Cursor

### 6.1 Locate Your Cursor Config Directory

The MCP configuration file is located at:
- **macOS/Linux:** `~/.cursor/mcp.json`
- **Windows:** `%APPDATA%\Cursor\mcp.json`

### 6.2 Create or Edit the MCP Config File

**If the file doesn't exist**, create it:

```bash
# macOS/Linux
mkdir -p ~/.cursor
touch ~/.cursor/mcp.json
```

**Quick Option:** Copy the template file from the repository:

```bash
# Copy the template (adjust path as needed)
cp /path/to/prompt-saver-mcp/mcp.json.template ~/.cursor/mcp.json
```

**Edit the file** (`~/.cursor/mcp.json`) and add this configuration:

```json
{
  "mcpServers": {
    "prompt-saver-team": {
      "command": "/PATH/TO/YOUR/PYTHON",
      "args": ["-m", "prompt_saver_mcp.server"],
      "cwd": "/PATH/TO/prompt-saver-mcp",
      "env": {
        "MONGODB_URI": "mongodb+srv://username:password@cluster.mongodb.net/",
        "MONGODB_DATABASE": "prompt_saver",
        "MONGODB_COLLECTION": "prompts",
        "VOYAGE_AI_API_KEY": "your_voyage_ai_api_key_here",
        "VOYAGE_AI_EMBEDDING_MODEL": "voyage-3-large",
        "OPENAI_API_KEY": "your_openai_api_key_here",
        "OPENAI_MODEL": "gpt-4o-mini",
        "PYTHONPATH": "/PATH/TO/prompt-saver-mcp"
      }
    }
  }
}
```

### 6.3 Replace the Placeholders

**Replace these values:**

1. **`/PATH/TO/YOUR/PYTHON`** → Your Python path from Step 5
   - Example: `/Users/john/.pyenv/versions/3.11.14/bin/python`

2. **`/PATH/TO/prompt-saver-mcp`** → Full path to your cloned repository
   - Example: `/Users/john/Desktop/prompt-saver-mcp`

3. **`mongodb+srv://username:password@cluster.mongodb.net/`** → Your MongoDB connection string
   - Replace `username` and `password` with your actual credentials
   - Keep the `@` and everything after it

4. **`your_voyage_ai_api_key_here`** → Your Voyage AI API key from Step 3.1

5. **`your_openai_api_key_here`** → Your OpenAI API key from Step 3.2

### 6.4 Example Configuration

Here's a complete example (with fake values):

```json
{
  "mcpServers": {
    "prompt-saver-team": {
      "command": "/Users/john/.pyenv/versions/3.11.14/bin/python",
      "args": ["-m", "prompt_saver_mcp.server"],
      "cwd": "/Users/john/Desktop/prompt-saver-mcp",
      "env": {
        "MONGODB_URI": "mongodb+srv://team-user:team-password@promptdb.6qjw6ob.mongodb.net/",
        "MONGODB_DATABASE": "prompt_saver",
        "MONGODB_COLLECTION": "prompts",
        "VOYAGE_AI_API_KEY": "pa-abc123xyz789",
        "VOYAGE_AI_EMBEDDING_MODEL": "voyage-3-large",
        "OPENAI_API_KEY": "sk-proj-abc123xyz789",
        "OPENAI_MODEL": "gpt-4o-mini",
        "PYTHONPATH": "/Users/john/Desktop/prompt-saver-mcp"
      }
    }
  }
}
```

**Important Notes:**
- Use **absolute paths** (full paths starting with `/`)
- The `PYTHONPATH` should match your `cwd` path
- Keep all the environment variable names exactly as shown
- The server name (`prompt-saver-team`) can be customized, but keep it consistent across your team

---

## Step 7: Test the Setup

### 7.1 Test the Server Manually

```bash
# Navigate to your prompt-saver-mcp directory
cd /path/to/prompt-saver-mcp

# Test if the server starts
python -m prompt_saver_mcp.server
```

You should see:
```
2025-XX-XX XX:XX:XX - __main__ - INFO - Configuration validated successfully
```

Press `Ctrl+C` to stop it.

### 7.2 Restart Cursor

**Important:** After editing `~/.cursor/mcp.json`, you **must restart Cursor completely**:

1. Quit Cursor completely (Cmd+Q on macOS, Alt+F4 on Windows/Linux)
2. Reopen Cursor
3. Cursor will automatically connect to the MCP server

### 7.3 Verify MCP Connection in Cursor

After restarting Cursor, test the connection:

1. Open Cursor chat
2. Ask: **"List all tools"**
3. You should see the MCP tools listed:
   - `mcp_prompt-saver-team_save_prompt`
   - `mcp_prompt-saver-team_preview_prompt`
   - `mcp_prompt-saver-team_search_prompts`
   - `mcp_prompt-saver-team_get_prompt_details`
   - etc.

If you see these tools, **you're all set!** 🎉

---

## Step 8: Test with a Real Command

Try searching for prompts:

**In Cursor chat, say:**
```
"Search my prompts for [any topic]"
```

Or test saving a prompt:

```
"Generate a preview of this conversation as a prompt"
```

Then:
```
"Save this previewed prompt"
```

---

## Troubleshooting

### Problem: "No server info found" or MCP server not connecting

**Solutions:**
1. **Check your Python path** - Make sure it's the full absolute path
2. **Check your `cwd` path** - Make sure it points to the correct directory
3. **Verify the package is installed** - Run `pip install -e .` again
4. **Check JSON syntax** - Make sure `~/.cursor/mcp.json` is valid JSON (no trailing commas)
5. **Restart Cursor** - Always restart after editing `mcp.json`
6. **Check Cursor logs** - Go to Cursor Settings → MCP Servers → Check for error messages

### Problem: "Failed to connect to MongoDB"

**Solutions:**
1. **Check your MongoDB URI** - Make sure username and password are correct
2. **Verify network access** - Make sure your IP is allowed in MongoDB Atlas Network Access
3. **Check the connection string format** - Should start with `mongodb+srv://`
4. **Test connection manually** - Try connecting with MongoDB Compass or `mongosh`

### Problem: "VOYAGE_AI_API_KEY is required"

**Solutions:**
1. **Check your API key** - Make sure it's correct in `~/.cursor/mcp.json`
2. **No extra spaces** - Remove any spaces before/after the key
3. **Verify the key is active** - Check your Voyage AI dashboard

### Problem: "OPENAI_API_KEY is required"

**Solutions:**
1. **Check your API key** - Make sure it starts with `sk-` or `sk-proj-`
2. **Verify you have credits** - Check your OpenAI account balance
3. **No extra spaces** - Remove any spaces before/after the key

### Problem: "Vector search not working"

**Solutions:**
1. **Check the index exists** - Go to MongoDB Atlas → Search tab → Verify `vector_index` exists
2. **Wait for index to build** - New indexes can take a few minutes
3. **Verify dimensions** - Make sure the index uses `1024` dimensions (not 2048)
4. **Check collection name** - Should be `prompts` (plural)

### Problem: "Module not found" or import errors

**Solutions:**
1. **Reinstall the package** - Run `pip install -e .` again
2. **Check PYTHONPATH** - Make sure it matches your `cwd` path in `mcp.json`
3. **Verify Python version** - Run `python --version` (should be 3.10+)
4. **Check virtual environment** - Make sure you're using the correct Python interpreter

### Problem: Server starts but tools don't appear

**Solutions:**
1. **Check server name** - Make sure it matches in Cursor's MCP settings
2. **Restart Cursor** - Always restart after configuration changes
3. **Check Cursor version** - Make sure you're using a recent version of Cursor that supports MCP
4. **Verify JSON syntax** - Use a JSON validator to check `~/.cursor/mcp.json`

---

## Quick Reference

### MCP Config File Location
- **macOS/Linux:** `~/.cursor/mcp.json`
- **Windows:** `%APPDATA%\Cursor\mcp.json`

### Required Environment Variables
- `MONGODB_URI` - MongoDB connection string
- `MONGODB_DATABASE` - Database name (usually `prompt_saver`)
- `MONGODB_COLLECTION` - Collection name (usually `prompts`)
- `VOYAGE_AI_API_KEY` - Your Voyage AI API key
- `VOYAGE_AI_EMBEDDING_MODEL` - Model name (usually `voyage-3-large`)
- `OPENAI_API_KEY` - Your OpenAI API key
- `OPENAI_MODEL` - Model name (usually `gpt-4o-mini`)
- `PYTHONPATH` - Path to prompt-saver-mcp directory

### Common Commands

```bash
# Find Python path
python -c "import sys; print(sys.executable)"

# Install package
pip install -e .

# Test server
python -m prompt_saver_mcp.server

# Check if package is installed
pip list | grep prompt-saver
```

---

## Next Steps

Once your setup is working:

1. **Start saving prompts** - After completing useful conversations, save them as reusable templates
2. **Search before starting** - Use `search_prompts` to find relevant prompts before beginning new tasks
3. **Share with team** - All teammates using the same MongoDB database can see each other's prompts
4. **Improve prompts** - Use `improve_prompt_from_feedback` to refine prompts based on experience

---

## Need Help?

- **Quick Checklist:** See [TEAM_SETUP_CHECKLIST.md](TEAM_SETUP_CHECKLIST.md) for a step-by-step checklist
- **Template File:** Use `mcp.json.template` in the repository root as a starting point
- Check the main [README.md](../README.md) for detailed documentation
- See [MCP_ONLY_WORKFLOW.md](MCP_ONLY_WORKFLOW.md) for usage examples
- Ask your team lead for MongoDB credentials or repository access
- Check Cursor's MCP server logs for detailed error messages

---

## Team Best Practices

1. **Use consistent server names** - Agree on a naming convention (e.g., `prompt-saver-team`)
2. **Share MongoDB credentials securely** - Use a password manager or secure channel
3. **Document custom configurations** - If your team has specific requirements, document them
4. **Test before committing** - Make sure changes work for everyone before pushing to main branch
5. **Keep API keys private** - Never commit API keys to the repository

---

**Happy prompt saving!** 🚀

