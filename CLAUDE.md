# n8n Workflow Builder

This project is a Claude-assisted workspace for building production-ready n8n workflows. When the user describes what they need, use the n8n MCP server tools and n8n skills to design, build, validate, and test workflows directly in their n8n instance.

## n8n MCP Server Tools

### Core Tools

| Tool | Purpose |
|------|---------|
| `tools_documentation` | Get documentation for any MCP tool |
| `search_nodes` | Full-text search across all n8n nodes (supports community node filtering and config examples) |
| `get_node` | Retrieve node info, documentation, properties, or version details |
| `validate_node` | Validate a node's configuration (quick or comprehensive, multiple profiles) |
| `validate_workflow` | Validate a complete workflow including AI Agent validation |
| `search_templates` | Find workflow templates by keyword, node type, task, or metadata |
| `get_template` | Get complete workflow JSON from a template |

### Workflow Management

| Tool | Purpose |
|------|---------|
| `n8n_create_workflow` | Create new workflows with nodes and connections |
| `n8n_get_workflow` | Retrieve workflows (full details, execution stats, topology, or minimal) |
| `n8n_update_full_workflow` | Replace an entire workflow |
| `n8n_update_partial_workflow` | Update a workflow using diff operations |
| `n8n_delete_workflow` | Delete a workflow permanently |
| `n8n_list_workflows` | List workflows with filtering and pagination |
| `n8n_validate_workflow` | Validate a workflow in n8n by ID |
| `n8n_workflow_versions` | Manage version history and rollback |
| `n8n_deploy_template` | Deploy a template from n8n.io with automatic error correction |

### Execution & System

| Tool | Purpose |
|------|---------|
| `n8n_test_workflow` | Test/trigger workflow execution with auto-detection and custom data |
| `n8n_executions` | List, retrieve, or delete execution records |
| `n8n_health_check` | Check n8n API connectivity and available features |

## n8n Skills

Seven skills activate automatically based on context:

1. **Expression Syntax** — Correct `{{}}` expression writing and variable access. Critical: webhook data is under `$json.body`, not `$json` directly.
2. **MCP Tools Expert** — Guides tool selection and usage. Handles nodeType format differences (`nodes-base.*` vs `n8n-nodes-base.*`) and validation profiles.
3. **Workflow Patterns** — Five proven architectural patterns: webhook processing, HTTP API integration, database operations, AI agent workflows, and scheduled tasks.
4. **Validation Expert** — Interprets validation errors, identifies false positives, and guides profile selection.
5. **Node Configuration** — Operation-aware node setup including AI connection types (8 types for AI Agent workflows).
6. **Code JavaScript** — Correct return format is `[{json: {...}}]`. Covers 10 production-tested patterns and common error solutions.
7. **Code Python** — No external libraries available (no requests, pandas, numpy). Use JavaScript for 95% of code node use cases.

## Workflow Building Process

Follow these steps for every workflow request:

1. **Clarify requirements** — Ask about: trigger type, data sources and destinations, expected input/output shapes, error scenarios, frequency/volume, and whether this is new or modifying an existing workflow.
2. **Search for existing patterns** — Use `search_templates` and `n8n_list_workflows` to find similar workflows that can serve as a starting point.
3. **Research nodes** — Use `search_nodes` and `get_node` to understand the nodes needed, their required properties, and configuration options.
4. **Build the workflow** — Use `n8n_create_workflow` or `n8n_deploy_template` to construct the workflow. Follow the quality standards below.
5. **Validate** — Run `validate_workflow` and `n8n_validate_workflow` to catch configuration errors before testing.
6. **Test** — Use `n8n_test_workflow` with realistic data. Verify the happy path and at least one error path.

## Workflow Quality Standards

### Naming
- Workflow names should be descriptive and action-oriented (e.g., "Sync Shopify Orders to Airtable" not "Workflow 1")
- Node names must describe what they do (e.g., "Filter Active Users" not "IF")
- Use consistent Title Case for workflow names and sentence case for node names

### Structure
- Arrange nodes in a logical left-to-right flow: trigger → process → output
- Use Sticky Note nodes to document complex logic, business rules, or non-obvious decisions
- Keep workflows focused — one workflow per concern. Split large workflows using sub-workflow calls

### Error Handling
- Add an Error Trigger workflow for critical workflows that sends notifications on failure
- Configure retry logic on HTTP Request nodes (at least 2 retries with backoff for external API calls)
- Add fallback paths using IF nodes after operations that can fail
- Never silently swallow errors — always log or notify

### Data Handling
- Validate and sanitize input data early in the workflow (especially webhook payloads)
- Use Set nodes to reshape and clean data between processing steps
- Only pass necessary fields downstream — strip large or sensitive payloads with Set nodes
- Use Merge nodes explicitly rather than relying on implicit data passing

### Credentials & Secrets
- Never hardcode API keys, tokens, or passwords in any node configuration
- Always use n8n's built-in credential system
- Reference credentials by their n8n credential name

### Expressions
- Webhook data is accessed via `$json.body`, not `$json` directly
- Use `$json` for data from the previous node in the chain
- Use `$node["Node Name"].json` to reference specific upstream nodes
- Prefer simple expressions over complex nested ones — use a Code node for complex transformations

### Code Nodes
- JavaScript: always return `[{json: {...}}]` format
- Python: avoid unless the task specifically requires it — no external libraries are available
- Keep code nodes small and focused — if a code node exceeds ~30 lines, consider splitting the logic

### Safety
- Never edit production workflows directly — always make a copy first
- Use `n8n_workflow_versions` to check version history before making changes
- Export backups before significant modifications
- Validate all changes before activating a workflow

## Quality Checklist

Before considering a workflow complete, verify:

- [ ] Workflow has a clear, descriptive name
- [ ] All nodes have meaningful names (no defaults like "IF", "HTTP Request")
- [ ] Error handling is in place for external calls and critical operations
- [ ] Input data is validated early in the flow
- [ ] No hardcoded credentials or secrets
- [ ] Expressions use correct syntax (`$json.body` for webhooks)
- [ ] Code nodes return data in the correct format
- [ ] Workflow has been validated with `validate_workflow`
- [ ] Workflow has been tested with `n8n_test_workflow`
- [ ] Sticky notes document any non-obvious logic
- [ ] Workflow is focused on a single concern
