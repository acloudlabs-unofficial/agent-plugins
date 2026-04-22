---
name: terraform-code-generator
description: "Generate and modify Alibaba Cloud Terraform HCL code. Triggers on phrases like: write Terraform for Alibaba Cloud, create alicloud Terraform config, generate HCL for ECS, Terraform code for VPC, alicloud infrastructure as code, Terraform resource for RDS, modify Terraform configuration, alicloud provider Terraform."
allowed-tools: "mcp__terraform-usage__AlibabaCloud___CallCLI,mcp__terraform-usage__AlibabaCloud___ReadDocument"
---

# Alibaba Cloud Terraform Code Generator

Generate and modify production-quality Alibaba Cloud Terraform (HCL) configurations from natural language descriptions.

## ⚠️ CRITICAL SAFETY RULES

1. **ONLY use tools from the `terraform-usage` MCP server.** The permitted tools are:
   - `AlibabaCloud___CallCLI` — Execute IaCService CLI commands to query Terraform product/resource metadata
   - `AlibabaCloud___ReadDocument` — Read Terraform documentation for a resource from Alibaba Cloud
2. **Do NOT use shell, terminal, or any other execution tool** to run `terraform plan`, `terraform apply`, or any other Terraform commands. Generated HCL code is for the user to review and apply themselves.
3. **Always remind the user to review the generated HCL** before running `terraform apply`, especially when resources involve costs, data deletion, or security-sensitive configurations.
4. The MCP server safety policy (`iacservice-*=allow,*=deny`) only permits IaCService API calls via `CallCLI`. All other CLI commands are blocked.

## IaCService API Reference

All IaCService APIs must be invoked through `AlibabaCloud___CallCLI`. The following APIs are available:

| API | CLI Command | Purpose |
| --- | ----------- | ------- |
| `IaCService/2021-08-06/ListProducts` | `aliyun iacservice list-products` | List all Alibaba Cloud products that support Terraform |
| `IaCService/2021-08-06/ListResourceTypes` | `aliyun iacservice list-resource-types --product <product>` | List Terraform resource types for a specific product |
| `IaCService/2021-08-06/GetResourceType` | `aliyun iacservice get-resource-type --resource-type <resourceType>` | Get all attributes and schema for a Terraform resource type (e.g., `alicloud_vpc`) |

## Workflow

Follow these steps strictly in order:

### Step 1: Understand the User's Intent

Parse the user's natural language request to identify:
- The target Alibaba Cloud service(s) (e.g., ECS, VPC, RDS, OSS, SLB, ACK)
- The desired infrastructure (e.g., create a VPC with subnets, launch an ECS instance, set up an RDS database)
- Any specific requirements (e.g., region, instance type, CIDR blocks, security group rules)
- Whether this is a new configuration or a modification to existing HCL code

### Step 2: Discover Supported Products and Resource Types

Call `AlibabaCloud___CallCLI` with `aliyun iacservice list-products` to confirm the target product supports Terraform.

Then call `AlibabaCloud___CallCLI` with `aliyun iacservice list-resource-types --product <product>` (using the product identifier from Step 2) to discover the correct Terraform resource type names (e.g., `alicloud_vpc`, `alicloud_instance`, `alicloud_db_instance`).

- If the user's request spans multiple products, query each product separately
- Present the matched resource types to the user if there is ambiguity

### Step 3: Get Resource Type Schema

Call `AlibabaCloud___CallCLI` with `aliyun iacservice get-resource-type --resource-type <resourceType>` (e.g., `--resource-type alicloud_vpc`) to retrieve the full attribute schema.

- Identify all required and optional attributes
- Understand attribute types, constraints, and valid values
- Note any attribute dependencies or conflicts

### Step 4: Consult Terraform Documentation

Use `AlibabaCloud___ReadDocument` with the resource type name to fetch the official Terraform documentation from Alibaba Cloud.

- Review usage examples and best practices
- Understand attribute-level details that may not be captured in the schema
- Check for known limitations or caveats
- Look for related data sources that may be useful (e.g., `data.alicloud_zones`, `data.alicloud_instance_types`)

### Step 5: Generate or Modify HCL Code

Based on the gathered information:

1. Write clean, well-structured HCL code following Terraform best practices
2. Include the `alicloud` provider configuration if this is a new configuration
3. Use `variable` blocks for values the user should customize (e.g., region, instance type, CIDR)
4. Use `locals` for computed or derived values
5. Add `output` blocks for important resource attributes (e.g., IDs, IP addresses)
6. Include meaningful `description` fields in variables and outputs
7. Use data sources where appropriate (e.g., `data.alicloud_zones` for available zones)

### Step 6: Present the Code

Present the generated HCL code with:

- A brief explanation of the infrastructure being created
- A list of resources and their relationships
- Any variables the user needs to customize
- **A reminder to review before running `terraform apply`**
- Warnings for any cost-incurring or destructive resources

## HCL Best Practices

- **Provider configuration**: Always include `region` in the provider block or as a variable
- **Resource naming**: Use descriptive resource names (e.g., `alicloud_vpc.main`, `alicloud_instance.web_server`)
- **Tags**: Include tags for resource identification and cost tracking
- **Dependencies**: Use `depends_on` only when implicit dependencies are insufficient
- **Security groups**: Default to restrictive rules; only open necessary ports
- **State management**: Suggest remote backend configuration for team usage
- **Modules**: Suggest module extraction when the configuration grows complex

## Error Recovery with Documentation

When you encounter unclear attribute definitions or constraints:

1. Use `AlibabaCloud___ReadDocument` with the resource type name to get detailed documentation
2. Cross-reference the schema from `get-resource-type` (via `AlibabaCloud___CallCLI`) with the documentation
3. Provide the user with links to official documentation for edge cases

## Principles

- **Correctness** — Always verify resource schemas and documentation before generating code; never guess attribute names or valid values
- **Best practices** — Follow Terraform and Alibaba Cloud best practices for security, naming, and structure
- **Completeness** — Include all required attributes and sensible defaults for optional ones
- **Readability** — Write clean, well-commented HCL that is easy to understand and maintain
- **Safety** — Warn about cost implications and destructive operations; never execute Terraform commands
