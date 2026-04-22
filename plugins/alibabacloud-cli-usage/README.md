# alibabacloud-cli-usage

> [!CAUTION]
> **UNOFFICIAL** — This plugin is NOT affiliated with Alibaba Cloud. Review all generated commands carefully before executing them in any environment.

Generate Alibaba Cloud CLI commands safely using MCP-based API discovery and command generation. This plugin **never executes commands directly** — it only generates commands for you to review and run manually.

## How It Works

This plugin uses the `cli-usage` MCP server (powered by `lazy.alibabacloud-mcp-proxy`) with a strict safety policy (`*=deny`) that prevents any direct CLI execution. The workflow is:

1. **Search** — Find the right API using `AlibabaCloud___SearchApis`
2. **Discover** — If search yields no results, progressively discover via `AlibabaCloud___ListProducts` → `AlibabaCloud___ListApis` → `AlibabaCloud___ListProductRegions`
3. **Define** — Get the full API definition with `AlibabaCloud___GetApiDefinition`
4. **Generate** — Produce a ready-to-use CLI command with `AlibabaCloud___GenerateCLICommand`
5. **Troubleshoot** — On errors, consult `AlibabaCloud___SearchDocument` and `AlibabaCloud___ReadDocument` for official documentation

## Agent Skill Triggers

| Agent Skill              | Triggers                                                                                                                                      |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **cli-command-generator** | "generate aliyun CLI command", "how to use aliyun CLI", "create CLI command for ECS", "aliyun CLI usage", "Alibaba Cloud CLI", "aliyun api call" |

## MCP Servers

| Server       | Purpose                                                    |
| ------------ | ---------------------------------------------------------- |
| **cli-usage** | API discovery and CLI command generation (execution denied) |

## Available MCP Tools

| Tool | Purpose |
| ---- | ------- |
| `AlibabaCloud___SearchApis` | Semantic search for APIs by keyword |
| `AlibabaCloud___GetApiDefinition` | Get full API definition by triplet (product, version, API name) |
| `AlibabaCloud___GenerateCLICommand` | Convert parameters into a ready-to-use CLI command |
| `AlibabaCloud___ListProducts` | List all Alibaba Cloud products |
| `AlibabaCloud___ListProductRegions` | List supported endpoints/regions for a product |
| `AlibabaCloud___ListApis` | List all APIs under a specific product |
| `AlibabaCloud___SearchDocument` | Search Alibaba Cloud documentation by keyword |
| `AlibabaCloud___ReadDocument` | Read a document in markdown format |

## Safety

- The MCP server runs with `--safety-policy *=deny`, blocking all direct command execution
- The skill is restricted to only permitted MCP tools via `allowed-tools`
- Generated commands are presented as text for manual review and execution
