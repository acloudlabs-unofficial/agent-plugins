# alibabacloud-cli-usage

> [!CAUTION]
> **UNOFFICIAL** — This plugin is NOT affiliated with Alibaba Cloud. Review all generated commands carefully before executing them in any environment.

Generate Alibaba Cloud CLI commands safely using MCP-based API discovery and command generation. This plugin **never executes commands directly** — it only generates commands for you to review and run manually.

## How It Works

This plugin uses the `cli-usage` MCP server (powered by `lazy.alibabacloud-mcp-proxy`) with a strict safety policy (`*=deny`) that prevents any direct CLI execution. The workflow is:

1. **Search** — Find the right API using `AlibabaCloud___SearchApis`
2. **Define** — Get the full API definition with `AlibabaCloud___GetApiDefinition`
3. **Generate** — Produce a ready-to-use CLI command with `AlibabaCloud___GenerateCLICommand`

## Agent Skill Triggers

| Agent Skill              | Triggers                                                                                                                                      |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **cli-command-generator** | "generate aliyun CLI command", "how to use aliyun CLI", "create CLI command for ECS", "aliyun CLI usage", "Alibaba Cloud CLI", "aliyun api call" |

## MCP Servers

| Server       | Purpose                                                    |
| ------------ | ---------------------------------------------------------- |
| **cli-usage** | API discovery and CLI command generation (execution denied) |

## Safety

- The MCP server runs with `--safety-policy *=deny`, blocking all direct command execution
- The skill is restricted to only three MCP tools via `allowed-tools`
- Generated commands are presented as text for manual review and execution
