---
name: cli-command-generator
description: "Generate Alibaba Cloud CLI commands from natural language. Triggers on phrases like: generate aliyun CLI command, how to use aliyun CLI, create CLI command for ECS, aliyun CLI usage, Alibaba Cloud CLI, run aliyun command, aliyun api call, cloud resource management CLI."
allowed-tools: "mcp__cli-usage__AlibabaCloud___SearchApis,mcp__cli-usage__AlibabaCloud___GetApiDefinition,mcp__cli-usage__AlibabaCloud___GenerateCLICommand"
---

# Alibaba Cloud CLI Command Generator

Generate safe, ready-to-use Alibaba Cloud CLI commands from natural language descriptions.

## ⚠️ CRITICAL SAFETY RULES

1. **NEVER execute any CLI command directly.** This skill only generates commands for the user to review and run manually.
2. **ONLY use tools from the `cli-usage` MCP server.** The three permitted tools are:
   - `AlibabaCloud___SearchApis`
   - `AlibabaCloud___GetApiDefinition`
   - `AlibabaCloud___GenerateCLICommand`
3. **Do NOT use shell, terminal, or any other execution tool.** All output is command text for the user to copy and execute themselves.
4. **Always warn the user to review the generated command** before executing, especially for destructive operations (delete, stop, release, etc.).

## Workflow

Follow these steps strictly in order:

### Step 1: Understand the User's Intent

Parse the user's natural language request to identify:
- The target Alibaba Cloud service (e.g., ECS, RDS, OSS, VPC, SLB)
- The desired operation (e.g., create, describe, delete, modify, list)
- Any specific parameters mentioned (e.g., region, instance type, name)

### Step 2: Search for the Right API

Use `AlibabaCloud___SearchApis` to find the matching API.

- Use clear, specific keywords derived from the user's intent
- If the first search returns no relevant results, try alternative keywords or service names
- Present the matched API to the user for confirmation if multiple candidates exist

### Step 3: Get the API Definition

Use `AlibabaCloud___GetApiDefinition` with the API's triplet information (product, version, API name) obtained from Step 2.

- Review the API definition to understand all required and optional parameters
- Identify which parameters the user has already provided
- Determine which required parameters are still missing

### Step 4: Construct Parameters and Generate the CLI Command

Based on the API definition and user-provided information:

1. Map user inputs to the correct API parameter names
2. Ask the user for any missing **required** parameters
3. Apply sensible defaults for optional parameters when appropriate
4. Use `AlibabaCloud___GenerateCLICommand` to convert the parameters into a properly formatted CLI command

### Step 5: Present the Command

Present the generated CLI command to the user with:

- The complete command ready to copy-paste
- A brief explanation of what the command does
- A list of key parameters and their values
- **A warning to review the command before executing**, especially for write/delete operations

## Principles

- **Safety first** — Never execute commands, only generate them
- **Accuracy** — Always verify API definitions before generating commands; do not guess parameter names
- **Transparency** — Explain what each command does and what parameters mean
- **Minimal questions** — Only ask for truly required parameters that the user hasn't provided
- **Destructive operation warnings** — Prominently warn when a command will delete, stop, release, or modify resources
