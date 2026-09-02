---
name: primo-mcp
description: Query and manage an IT fleet through Primo's hosted MCP server. Use when the request involves devices (inventory, specs, encryption, OS version, enrollment, lock, wipe, scripts), employees and admins, onboarding or offboarding, IT tickets and tasks, accessories, device groups, SaaS applications and identities, installed software and vulnerabilities, MDM controls, compliance alerts, orders, shipments, custom fields, or knowledge base articles. Also use when the user names Primo, or asks a fleet question whose answer lives in an IT asset system rather than in the repo.
---

# Primo MCP

Primo is a hosted IT management platform. The `primo` MCP server exposes its public API
as tools, so there is no local process, no API key, and no REST call to make by hand.

## Find the tool before calling it

Read the live `tools/list` and use the names and input schemas it returns. Tool names are
API operation ids such as `getDevices`, `lockDevice`, `deprovisionSaasIdentity`. The set
moves as Primo ships, so never carry a name over from a past session or guess one from the
pattern.

Each tool description states its category, whether it is read or write, and its required
inputs. Each carries annotations: `readOnlyHint`, `destructiveHint`, `idempotentHint`.

Resolve ids by listing or searching first: employee by email, device by serial or hostname,
SaaS app by name. Never construct an id. If a required input is neither in the schema
default nor given by the user, ask for it. Do not invent emails, serials, or enum members.

## Stay read-only unless asked

Answer questions with read tools. When a finding implies a fix, report it and offer the
mutation, then wait.

The default endpoint is read-only. Write tools still appear in `tools/list`; calling one
returns HTTP 403 with `WRITE_OPERATION_BLOCKED_BY_READONLY_MODE`. That is the endpoint
configuration, not a bad call. Tell the user to reconnect with
`https://api.getprimo.com/mcp?readOnly=false` instead of retrying or routing around it.

A 403 with `MISSING_REQUIRED_PERMISSION` is different. The signed-in Primo user lacks the
permission the operation needs, and only a Primo admin can grant it.

## Confirm destructive calls

Treat every tool whose `destructiveHint` is true as needing the user's go-ahead. State the
exact target and what will happen, then call. Wipe, delete, retire, disenroll, and SaaS
deprovisioning cannot be undone, and running a script executes arbitrary code on real
hardware.

For a bulk target (a device group, a filter, "all Macs"), resolve it first and show the
count with a sample of what matched before executing anything.

## Tenant

A user in one company needs no tenant parameter. A user in several gets the tenant from
**My Account → AI Settings → Default MCP Tenant**, or per connection from
`?x-company-id=<id>` on the MCP URL. When results look like the wrong company, check the
tenant before doubting the data.

## Reference

- Server docs: https://docs.getprimo.com/get-started/primo-mcp-server
- REST reference for the same operations: https://api.getprimo.com/apidoc
- OpenAPI: https://api.getprimo.com/openapi.json
