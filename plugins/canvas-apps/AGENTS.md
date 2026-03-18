# AGENTS.md — Canvas Apps Plugin

This file provides guidance to AI Agents when working with the **canvas-apps** plugin.

## What This Plugin Is

A plugin for authoring Power Apps Canvas Apps. The Canvas Authoring MCP server (`CanvasAuthoringMcpServer`) exposes tools and prompts that Claude uses directly — no skill orchestration layer is needed. Claude calls the MCP tools in conversation to generate, validate, and compile Canvas App YAML files (`.pa.yaml`) in conjunction with a running coauthoring studio session

## Local Development

Test this plugin locally:

```bash
claude --plugin-dir /path/to/plugins/canvas-apps
```

## Architecture

```
.claude-plugin/plugin.json     ← Plugin metadata (name, version, keywords)
AGENTS.md                      ← Plugin guidance for AI agents (this file)
CLAUDE.md                      ← Symlink → AGENTS.md
references/
  TechnicalGuide.md            ← YAML syntax, control selection, layout strategies, Power Fx patterns
  DesignGuide.md               ← Aesthetic guidelines, anti-patterns, design process
skills/
  mcp-configure/
    SKILL.md                   ← Registers the Canvas Authoring MCP server with Claude Code
  generate-app/
    SKILL.md                   ← Generates pa.yaml source files for a described Canvas App
```

## Skills

| Skill | Description |
|-------|-------------|
| `/canvas-apps-mcp-configure` | Register the Canvas Authoring MCP server with Claude Code |
| `/canvas-apps-generate-app` | Generate a complete Canvas App from a natural language description |

## MCP Tools

The `canvas-authoring` MCP server exposes the following tools:

| Tool | Description |
|------|-------------|
| `ListControls` | Lists all available Canvas App controls and their properties |
| `DescribeControl` | Returns detailed property schema for a specific control |
| `CompileCanvas` | Compiles `.pa.yaml` source files into a `.msapp` package |

## Prerequisites

Before the MCP server will start, you need:

**.NET 10 SDK** — [Download from Microsoft](https://dotnet.microsoft.com/download/dotnet/10.0)
