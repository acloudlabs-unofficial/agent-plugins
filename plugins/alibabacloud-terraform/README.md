# alibabacloud-terraform

> [!CAUTION]
> **UNOFFICIAL** — This plugin is NOT affiliated with Alibaba Cloud. Review all generated Terraform code carefully before applying it in any environment.

Generate and modify Alibaba Cloud Terraform (HCL) configurations using MCP-based resource type discovery and documentation lookup. This plugin helps you write correct, production-quality HCL code by querying the actual Terraform resource schemas and official documentation.

## How It Works

This plugin uses the `terraform-usage` MCP server (powered by `lazy.alibabacloud-mcp-proxy`) with a safety policy that only allows IaC Service API calls (`iacservice-*=allow,*=deny`). All IaCService APIs are invoked through the `AlibabaCloud___CallCLI` tool as CLI commands. The workflow is:

1. **Discover** — Find supported products via `aliyun iacservice list-products`
2. **Identify** — List Terraform resource types via `aliyun iacservice list-resource-types --product <product>`
3. **Schema** — Get full attribute schema via `aliyun iacservice get-resource-type --resource-type <resourceType>`
4. **Search Docs** — Search documentation with `AlibabaCloud___SearchDocument` to find relevant URLs
5. **Read Docs** — Read full documentation with `AlibabaCloud___ReadDocument` using URLs from search results
6. **Generate** — Write or modify HCL code based on gathered knowledge

## Agent Skill Triggers

| Agent Skill                  | Triggers                                                                                                                                                  |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **terraform-code-generator** | "write Terraform for Alibaba Cloud", "create alicloud Terraform config", "generate HCL for ECS", "Terraform code for VPC", "alicloud infrastructure as code" |

## MCP Servers

| Server             | Purpose                                                            |
| ------------------ | ------------------------------------------------------------------ |
| **terraform-usage** | Terraform resource discovery and documentation (execution denied) |

## Available MCP Tools

| Tool | Purpose |
| ---- | ------- |
| `AlibabaCloud___CallCLI` | Execute IaCService CLI commands (`list-products`, `list-resource-types`, `get-resource-type`) |
| `AlibabaCloud___SearchDocument` | Search Alibaba Cloud documentation by keyword to find relevant URLs |
| `AlibabaCloud___ReadDocument` | Read a specific document by URL (must use URLs from `SearchDocument`) |

## Safety

- The MCP server runs with `--safety-policy iacservice-*=allow,*=deny`, only allowing IaC Service API calls
- The skill is restricted to only permitted MCP tools via `allowed-tools`
- No Terraform commands are executed — HCL code is generated for manual review and application
