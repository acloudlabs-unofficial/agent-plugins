---
name: cli-command-generator
description: "Generate Alibaba Cloud CLI commands from natural language. Triggers on phrases like: generate aliyun CLI command, how to use aliyun CLI, create CLI command for ECS, aliyun CLI usage, Alibaba Cloud CLI, run aliyun command, aliyun api call, cloud resource management CLI."
allowed-tools: "mcp__cli-usage__AlibabaCloud___SearchApis,mcp__cli-usage__AlibabaCloud___GetApiDefinition,mcp__cli-usage__AlibabaCloud___GenerateCLICommand,mcp__cli-usage__AlibabaCloud___ListProducts,mcp__cli-usage__AlibabaCloud___ListProductRegions,mcp__cli-usage__AlibabaCloud___ListApis,mcp__cli-usage__AlibabaCloud___SearchDocument,mcp__cli-usage__AlibabaCloud___ReadDocument"
---

# Alibaba Cloud CLI Command Generator

Generate safe, ready-to-use Alibaba Cloud CLI commands from natural language descriptions.

## ⚠️ CRITICAL SAFETY RULES

1. **NEVER execute any CLI command directly.** This skill only generates commands for the user to review and run manually.
2. **ONLY use tools from the `cli-usage` MCP server.** The permitted tools are:
   - `AlibabaCloud___SearchApis` — Semantic search for APIs by keyword
   - `AlibabaCloud___GetApiDefinition` — Get full API definition by triplet (product, version, API name)
   - `AlibabaCloud___GenerateCLICommand` — Convert parameters into a CLI command
   - `AlibabaCloud___ListProducts` — List all Alibaba Cloud products
   - `AlibabaCloud___ListProductRegions` — List supported endpoints/regions for a product
   - `AlibabaCloud___ListApis` — List all APIs under a specific product
   - `AlibabaCloud___SearchDocument` — Search Alibaba Cloud documentation by keyword
   - `AlibabaCloud___ReadDocument` — Read a document in markdown format
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

**Fallback: Progressive Discovery**

If `AlibabaCloud___SearchApis` returns no relevant results, use the rule-based discovery tools to narrow down incrementally:

1. `AlibabaCloud___ListProducts` — Browse all available Alibaba Cloud products to identify the correct service
2. `AlibabaCloud___ListApis` — List all APIs under the identified product to find the right operation
3. `AlibabaCloud___ListProductRegions` — Check which regions/endpoints the product supports (useful for region-specific parameters)

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

**Important:** The generated CLI command follows the [CLI v2 syntax](references/cli-v2-syntax.md). Key rules:
- Command format: `aliyun <product> <api-name> [--parameter-name value]`
- All API names and parameters use **lowercase kebab-case** (e.g., `DescribeInstances` → `describe-instances`, `InstanceId` → `--instance-id`)
- **`RegionId` must be passed as `--biz-region-id`**, NOT `--region-id` (which is reserved for CLI endpoint routing)

### Step 5: Present the Command

Present the generated CLI command to the user with:

- The complete command ready to copy-paste
- A brief explanation of what the command does
- A list of key parameters and their values
- **A warning to review the command before executing**, especially for write/delete operations

## Error Recovery with Documentation

When a generated command fails or produces unexpected results, use the documentation tools to gather additional context:

1. `AlibabaCloud___SearchDocument` — Search Alibaba Cloud documentation by keyword to find relevant guides, tutorials, or API usage examples
2. `AlibabaCloud___ReadDocument` — Read the full document content in markdown format to understand parameter constraints, prerequisites, or service-specific requirements

Use these tools to:
- Clarify parameter formats or valid values when the API definition alone is insufficient
- Find prerequisites (e.g., required permissions, dependent resources that must exist first)
- Understand service-specific constraints or quotas
- Provide the user with links to official documentation for further reading

## Principles

- **Safety first** — Never execute commands, only generate them
- **Accuracy** — Always verify API definitions before generating commands; do not guess parameter names
- **Transparency** — Explain what each command does and what parameters mean
- **Minimal questions** — Only ask for truly required parameters that the user hasn't provided
- **Destructive operation warnings** — Prominently warn when a command will delete, stop, release, or modify resources

## References

- [CLI v2 Syntax Guide](references/cli-v2-syntax.md) — New CLI naming conventions, parameter conversion rules, and the critical `--biz-region-id` rename
