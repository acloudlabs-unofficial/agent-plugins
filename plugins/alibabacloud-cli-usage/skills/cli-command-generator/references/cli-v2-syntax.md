# Alibaba Cloud CLI v2 Syntax Guide

This document describes the new CLI v2 command syntax used by Alibaba Cloud CLI.

Reference: [CLI Center - Alibaba Cloud OpenAPI Portal](https://api.aliyun.com/api-tools/cli)

## Command Format

The new CLI v2 uses a **lowercase kebab-case** naming convention for all commands and parameters:

```bash
aliyun <product> <api-version> <api-name> [--parameter-name value ...]
```

### Naming Rules

| Element | Old Format (v1) | New Format (v2) |
| ------- | --------------- | --------------- |
| Product | `ecs` | `ecs` |
| API Name | `DescribeInstances` | `describe-instances` |
| Parameter | `--InstanceId` | `--instance-id` |
| Nested Parameter | `--Tag.1.Key` | `--tag.1.key` |

**Key rule:** All PascalCase API names and parameter names are converted to lowercase kebab-case (words separated by hyphens).

### Examples

**List ECS instances:**

```bash
# Old v1 style
aliyun ecs DescribeInstances --RegionId cn-hangzhou

# New v2 style
aliyun ecs 2014-05-26 describe-instances --biz-region-id cn-hangzhou
```

**Create a VPC:**

```bash
aliyun vpc 2016-04-28 create-vpc --biz-region-id cn-hangzhou --cidr-block 172.16.0.0/12
```

**Describe RDS instances:**

```bash
aliyun rds 2014-08-15 describe-db-instances --biz-region-id cn-hangzhou
```

## ⚠️ CRITICAL: RegionId Parameter Rename

In CLI v2, the API parameter `RegionId` has been **renamed** to `--biz-region-id` to avoid conflicts with the CLI's own `--region-id` flag (which controls the CLI client's endpoint routing).

| Context | Flag | Purpose |
| ------- | ---- | ------- |
| CLI client config | `--region-id` | Controls which endpoint the CLI connects to |
| API business parameter | `--biz-region-id` | The `RegionId` parameter sent in the API request body |

**Always use `--biz-region-id` when the API definition includes a `RegionId` parameter.** Using `--region-id` instead will only change the CLI's endpoint routing and will NOT pass `RegionId` to the API.

## Parameter Conversion Rules

1. **PascalCase → kebab-case**: `InstanceId` → `--instance-id`
2. **Consecutive uppercase**: `VPCId` → `--vpc-id`, `DBInstanceId` → `--db-instance-id`
3. **Nested/indexed parameters**: `Tag.1.Key` → `--tag.1.key`, `SecurityGroupIds.1` → `--security-group-ids.1`
4. **Boolean parameters**: `--dry-run true` or `--dry-run false`
5. **RegionId exception**: `RegionId` → `--biz-region-id` (NOT `--region-id`)

## API Version

The API version is **required** in v2 commands and appears between the product name and the API name:

```bash
aliyun <product> <api-version> <api-name>
```

For example:
- ECS: `aliyun ecs 2014-05-26 describe-instances`
- VPC: `aliyun vpc 2016-04-28 describe-vpcs`
- RDS: `aliyun rds 2014-08-15 describe-db-instances`
- SLB: `aliyun slb 2014-05-15 describe-load-balancers`
- OSS: Uses a separate `ossutil` tool, not the standard CLI

## Output Format

By default, the CLI outputs JSON. You can control the output format:

```bash
# JSON output (default)
aliyun ecs 2014-05-26 describe-instances --biz-region-id cn-hangzhou

# Table output
aliyun ecs 2014-05-26 describe-instances --biz-region-id cn-hangzhou --output table

# Only specific fields
aliyun ecs 2014-05-26 describe-instances --biz-region-id cn-hangzhou --output cols=InstanceId,InstanceName,Status
```
