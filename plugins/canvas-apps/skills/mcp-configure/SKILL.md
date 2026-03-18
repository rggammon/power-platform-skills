---
name: canvas-apps-mcp-configure
version: 1.0.0
description: Configure the Canvas Authoring MCP server for Claude Code. USE WHEN "configure MCP", "set up MCP server", "MCP not working", "connect Canvas Apps MCP", "canvas-authoring not available", "MCP not configured", "set up canvas apps". DO NOT USE WHEN prerequisites are missing — direct the user to install .NET 10 SDK first.
author: Microsoft Corporation
user-invocable: true
model: sonnet
allowed-tools: Bash, AskUserQuestion
---

# Configure the Canvas Authoring MCP Server

This skill registers the Canvas Authoring MCP server with Claude Code using the user's Power Platform environment ID.

## Instructions

### 0. Check prerequisites

Verify that .NET 10 SDK or higher is installed

```bash
dotnet --list-sdks
```

If a version over 10.x.y is not listed, tell the user:
> ⚠️ .NET 10 SDK is required to run the Canvas Authoring MCP server. It looks like you don't have it installed. Please install it first to use this skill. https://dotnet.microsoft.com/download/dotnet/10.0


Then wait for the user to install it before continuing. If they say it's installed, run the command again to confirm. If it's still not found, repeat the message until they have it installed.

### 1. Determine which tool to configure

Determine whether needs to configure MCP for GitHub Copilot or for Claude Code:
- If explicitly mentioned in prompt, use that.
- Otherwise, determine which tool the user is running from the context.
- Only if choosing based on the context is impossible, ask the user:

> Which tool would you like to configure the Dataverse MCP server for?
> 1. **GitHub Copilot**
> 2. **Claude**

Based on the result, set the `TOOL_TYPE` variable to either `copilot` or `claude`. Store this for use in all subsequent steps.

### 2. Determine the MCP scope

Choose the configuration scope based on the tool. Use the scope explicitly mentioned by the user, or ask the user to choose.

**If TOOL_TYPE is `copilot`:**

The options are:
1. **Globally** (default, available in all projects)
2. **Project-only** (available only in this project)

Based on the scope, set the `CONFIG_PATH` variable:
- **Global**: `~/.copilot/mcp-config.json` (use the user's home directory)
- **Project**: `.mcp/copilot/mcp.json` (relative to the current working directory)

Store this path for use in steps 5 and 6.

**If TOOL_TYPE is `claude`:**

The options are:
1. **User** (available in all projects for this user)
2. **Project** (default, available only in this project)
3. **Local** (scoped to current project directory)

Based on the scope, set the `CLAUDE_SCOPE` variable:
- **User**: `CLAUDE_SCOPE` = `user`
- **Project**: `CLAUDE_SCOPE` = `project`
- **Local**: `CLAUDE_SCOPE` = `local`

Store this value for use in step 6.

### 3. Ask for the environment ID

Ask the user for their Power Platform environment ID:

> What is your Power Platform environment ID?
>
> This is the value for `CANVAS_ENVIRONMENT_NAME`. It looks like `Default-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` or `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.
>
> You can find it in the [Power Platform admin center](https://admin.powerplatform.microsoft.com) — open your environment and copy the **Environment ID** from the details panel.

Store this as `ENV_ID` for use in step 5.

### 4. Ask for the app ID

Ask the user for the ID of the app they want to connect to:

> What is the id of the app you're editing? 
> Make sure this is a canvas app, that it's open in PowerApps Designer, and that it has Coauthoring enabled.
>
> This is the value for `CANVAS_APP_ID`. It looks like `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.
>
> You can find it in the studio session, under Settings -> Support -> Session Details -> App Id

Store this as `APP_ID` for use in step 5.

### 5. Register the MCP server


**If TOOL_TYPE is `claude`:**
Run the following command to register the server with Claude Code:

```bash
claude mcp add --scope {CLAUDE_SCOPE} canvas-authoring -e CANVAS_ENVIRONMENT_ID={ENV_ID} -e CANVAS_APP_ID={APP_ID} \
-- dnx Microsoft.PowerApps.CanvasAuthoring.McpServer --yes --prerelease --source https://msazure.pkgs.visualstudio.com/_packaging/Power-Fx/nuget/v3/index.json
```

If the command fails because `canvas-authoring` is already registered, re-run with `--force` to overwrite it:

```bash
claude mcp add --scope {CLAUDE_SCOPE} canvas-authoring -e CANVAS_ENVIRONMENT_ID={ENV_ID} -e CANVAS_APP_ID={APP_ID} --force \
-- dnx Microsoft.PowerApps.CanvasAuthoring.McpServer --yes --prerelease --source https://msazure.pkgs.visualstudio.com/_packaging/Power-Fx/nuget/v3/index.json
```

**If TOOL_TYPE is `copilot`:**
1. If `CONFIG_PATH` is for a **project-scoped** configuration (`.mcp/copilot/mcp.json`), ensure the directory exists first:
   ```bash
   mkdir -p .mcp/copilot
   ```

2. Read the existing configuration file at `CONFIG_PATH`, or create a new empty config if it doesn't exist:
   ```json
   {}
   ```

3. Determine which top-level key to use:
   - If the config already has `"servers"`, use that
   - Otherwise, use `"mcpServers"`

4. Add or update the server entry:
   ```json
   {
     "mcpServers": {
       "canvas-authoring": {
         "type": "stdio",
         "command": "dnx",
         "args": [
           "Microsoft.PowerApps.CanvasAuthoring.McpServer",
           "--yes",
           "--prerelease",
           "--source",
           "https://msazure.pkgs.visualstudio.com/_packaging/Power-Fx/nuget/v3/index.json"
         ],
          "env": {
            "CANVAS_ENVIRONMENT_ID": "{ENV_ID}",
            "CANVAS_APP_ID": "{APP_ID}"
          } 
       }
     }
   }
   ```

5. Write the updated configuration back to `CONFIG_PATH` with proper JSON formatting (2-space indentation).

**Important notes:**
- Do NOT overwrite other entries in the configuration file
- Preserve the existing structure and formatting

### 6. Confirm and provide next steps

Tell the user:

> ✅ Canvas Authoring MCP server configured (`canvas-authoring`, scope: `{CLAUDE_SCOPE}`).
>
> **Restart Claude Code to activate it.** Remember to use `claude --continue` to resume this session without losing context.
>
> After restarting, verify the setup:
> - `canvas-authoring` should appear in the MCP server list
> - Ask Claude: "List available Canvas App controls" — should invoke `ListControls()`
