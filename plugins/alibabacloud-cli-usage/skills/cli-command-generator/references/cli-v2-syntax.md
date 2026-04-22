# Alibaba Cloud CLI v2 Syntax Guide

This document describes the new CLI v2 command syntax used by Alibaba Cloud CLI.

Reference: [CLI Center - Alibaba Cloud OpenAPI Portal](https://api.aliyun.com/api-tools/cli)

## Command Format

The new CLI v2 uses a **lowercase kebab-case** naming convention for all commands and parameters:

```bash
aliyun <product> <api-name> [--parameter-name value ...]
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

**Query available ECS resources:**

```bash
aliyun ecs describe-available-resource \
  --biz-region-id cn-hangzhou \
  --destination-resource InstanceType
```

Full parameter list for `describe-available-resource`:

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| `--biz-region-id` | ✅ | Region ID (renamed from `RegionId`) |
| `--destination-resource` | ✅ | Target resource type to query |
| `--instance-charge-type` | ❌ | Billing method |
| `--spot-strategy` | ❌ | Spot instance strategy |
| `--spot-duration` | ❌ | Spot instance duration |
| `--zone-id` | ❌ | Availability zone |
| `--io-optimized` | ❌ | I/O optimized flag |
| `--dedicated-host-id` | ❌ | Dedicated host ID |
| `--instance-type` | ❌ | Instance type filter |
| `--system-disk-category` | ❌ | System disk category |
| `--data-disk-category` | ❌ | Data disk category |
| `--network-category` | ❌ | Network category |
| `--cores` | ❌ | CPU core count filter |
| `--memory` | ❌ | Memory size filter |
| `--resource-type` | ❌ | Resource type |
| `--scope` | ❌ | Query scope |

**Transform RDS instance billing type:**

```bash
aliyun rds transform-db-instance-pay-type \
  --db-instance-id rm-xxxxxxxxx \
  --pay-type Prepaid
```

Full parameter list for `transform-db-instance-pay-type`:

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| `--db-instance-id` | ✅ | RDS instance ID |
| `--pay-type` | ✅ | Target billing type |
| `--client-token` | ❌ | Idempotency token |
| `--used-time` | ❌ | Subscription duration |
| `--period` | ❌ | Subscription period unit |
| `--business-info` | ❌ | Business info |
| `--auto-renew` | ❌ | Enable auto-renewal |
| `--auto-use-coupon` | ❌ | Auto-apply coupon |
| `--promotion-code` | ❌ | Promotion code |

**List ECS instances:**

```bash
# Old v1 style
aliyun ecs DescribeInstances --RegionId cn-hangzhou

# New v2 style
aliyun ecs describe-instances --biz-region-id cn-hangzhou
```

## ⚠️ CRITICAL: RegionId Parameter Rename

In CLI v2, the API parameter `RegionId` has been **renamed** to a fixed name `--biz-region-id` to avoid conflicts with the CLI's own `--region-id` flag (which controls the CLI client's endpoint routing).

| Context | Flag | Purpose |
| ------- | ---- | ------- |
| CLI client config | `--region-id` | Controls which endpoint the CLI connects to |
| API business parameter | `--biz-region-id` | The `RegionId` parameter sent in the API request body |

**Always use `--biz-region-id` when the API definition includes a `RegionId` parameter.** Using `--region-id` instead will only change the CLI's endpoint routing and will NOT pass `RegionId` to the API.

## Parameter Conversion Rules

1. **PascalCase → kebab-case**: `InstanceId` → `--instance-id`
2. **Consecutive uppercase treated as word boundaries**: `VPCId` → `--vpc-id`, `DBInstanceId` → `--db-instance-id`
3. **Nested/indexed parameters**: `Tag.1.Key` → `--tag.1.key`, `SecurityGroupIds.1` → `--security-group-ids.1`
4. **Boolean parameters**: `--dry-run true` or `--dry-run false`
5. **RegionId exception**: `RegionId` → `--biz-region-id` (NEVER `--region-id`)

## Multi-Version APIs

Some Alibaba Cloud products have multiple API versions. Plugins whose description contains the `multi-version` keyword support multi-version API calls.

You can check installed plugins and their multi-version support:

```bash
aliyun plugin list
```

Example output:

```
Name                Version             Description
----                -------             -----------
aliyun-cli-ecs      0.1.0               Aliyun CLI plugin for Elastic Compute Service operations.
aliyun-cli-ess      0.1.0               Aliyun CLI plugin for Auto Scaling operations with multi-version API support.
aliyun-cli-fc       0.1.0               Aliyun CLI plugin for Function Compute 3.0 operations.
```

### Using the Default API Version

When no version is specified, the CLI uses the product's default API version:

```bash
aliyun ess describe-scaling-groups --biz-region-id cn-hangzhou
```

### Specifying an API Version

Use `--api-version` to target a specific API version:

```bash
aliyun ess describe-scaling-groups --api-version 2022-02-22 --biz-region-id cn-hangzhou
```

### Listing Available API Versions

Use `list-api-versions` to see all supported versions for a product:

```bash
aliyun ess list-api-versions
```

> **Note:** Only products with multi-version plugins support `--api-version` and `list-api-versions`. For single-version products, the version is implicit and cannot be changed.

## Output Format

By default, the CLI outputs JSON. You can control the output format:

```bash
# JSON output (default)
aliyun ecs describe-instances --biz-region-id cn-hangzhou

# Table output
aliyun ecs describe-instances --biz-region-id cn-hangzhou --output table

# Only specific fields
aliyun ecs describe-instances --biz-region-id cn-hangzhou --output cols=InstanceId,InstanceName,Status
```
