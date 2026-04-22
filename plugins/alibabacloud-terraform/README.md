# alibabacloud-terraform

> [!CAUTION]
> **UNOFFICIAL** — This plugin is NOT affiliated with Alibaba Cloud. Review all generated Terraform code carefully before applying it in any environment.

Generate and modify Alibaba Cloud Terraform (HCL) configurations using MCP-based resource type discovery and documentation lookup. This plugin helps you write correct, production-quality HCL code by querying the actual Terraform resource schemas and official documentation.

## How It Works

This plugin uses the `terraform-usage` MCP server (powered by `lazy.alibabacloud-mcp-proxy`) with a safety policy that only allows IaC Service API calls (`iacservice-*=allow,*=deny`). The workflow is:

1. **Discover** — Find supported products with `AlibabaCloud___IaCService_ListProducts`
2. **Identify** — List Terraform resource types for a product with `AlibabaCloud___IaCService_ListResourceTypes`
3. **Schema** — Get full attribute schema with `AlibabaCloud___IaCService_GetResourceType`
4. **Document** — Read official Terraform docs with `AlibabaCloud___ReadDocument`
5. **Generate** — Write or modify HCL code based on gathered knowledge

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
| `AlibabaCloud___IaCService_ListProducts` | List all Alibaba Cloud products that support Terraform |
| `AlibabaCloud___IaCService_ListResourceTypes` | List Terraform resource types for a specific product |
| `AlibabaCloud___IaCService_GetResourceType` | Get full attribute schema for a Terraform resource type |
| `AlibabaCloud___ReadDocument` | Read official Terraform documentation for a resource |

## Safety

- The MCP server runs with `--safety-policy iacservice-*=allow,*=deny`, only allowing IaC Service API calls
- The skill is restricted to only permitted MCP tools via `allowed-tools`
- No Terraform commands are executed — HCL code is generated for manual review and application
