# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is an N8N Workflow Management Hub that uses the N8N MCP (Model Context Protocol) integration to create, manage, optimize, and debug N8N workflows through natural language interactions. The repository connects to a live N8N instance at `https://process.bravemedia.io` via MCP.

## Project Organization Rules

### Folder Structure Requirements
- **ALWAYS create a new folder** for each automation task/workflow
- Use kebab-case for folder names (e.g., `mysql-to-sheets-sync`)
- Each workflow folder MUST contain:
  - `README.md` - Complete documentation of the workflow
  - `*.json` files - N8N workflow JSON exports
  - `workflow-configuration-guide.md` - Setup and configuration instructions
  - Supporting files in subfolders (e.g., `/prompts/`, `/templates/`)

### Current Project Structure
```
n8n_brave/
├── .mcp.json                        # N8N MCP configuration (contains API credentials)
├── CLAUDE.md                        # This file
├── README.md                        # Main hub documentation
├── ai-collections-system/           # Example workflow implementation
│   ├── *.json                       # Workflow files
│   ├── workflow-configuration.md    # Setup guide
│   └── prompts/                     # AI prompt templates
└── [new-automation-folders]/        # Each new task creates its own folder
```

## N8N MCP Integration

### Available Sub-Agents
When working with N8N workflows, use these specialized agents:

1. **n8n-workflow-architect** - Design workflow architecture and plan implementations
2. **n8n-workflow-builder** - Convert plans into working N8N workflow JSON files
3. **n8n-node-finder** - Discover and recommend appropriate N8N nodes
4. **n8n-integration-specialist** - Configure API integrations and external services
5. **n8n-debugger** - Troubleshoot workflow execution issues
6. **n8n-optimizer** - Improve workflow performance and efficiency
7. **n8n-validator** - Validate workflows before deployment

### MCP Connection Details
- **N8N Instance URL:** `https://process.bravemedia.io`
- **Authentication:** API key stored in `.mcp.json`
- **MCP Server:** `n8n-mcp` via npx

## Workflow Development Process

### Creating New Workflows
1. Create a dedicated folder for the workflow
2. Use `n8n-workflow-architect` to plan the architecture
3. Use `n8n-node-finder` to identify required nodes
4. Use `n8n-workflow-builder` to create the implementation
5. Use `n8n-validator` to verify before deployment
6. Document everything in the folder's README.md

### Working with Existing Workflows
- Reference workflows by their folder name or N8N workflow ID
- Use `n8n-debugger` for execution issues
- Use `n8n-optimizer` for performance improvements
- Always update documentation after changes

## N8N Workflow JSON Structure

### Standard Workflow Components
```json
{
  "name": "Workflow Name",
  "nodes": [...],           // Array of node configurations
  "connections": {...},     // Node connection mappings
  "settings": {...},        // Workflow settings
  "staticData": null,
  "pinData": {},
  "versionId": "1.0.0",
  "tags": [...]
}
```

### Node Configuration Pattern
```json
{
  "parameters": {...},      // Node-specific parameters
  "id": "unique_id",       // Unique node identifier
  "name": "Node Name",     // Display name
  "type": "n8n-nodes-base.nodeType",
  "typeVersion": 1,
  "position": [x, y],      // Visual position
  "credentials": {...}     // Credential references (never actual values)
}
```

## Common N8N Nodes and Patterns

### Trigger Nodes
- `n8n-nodes-base.webhook` - HTTP webhook triggers
- `n8n-nodes-base.cron` - Scheduled triggers
- `n8n-nodes-base.emailReadImap` - Email triggers

### Data Processing
- `n8n-nodes-base.code` - Custom JavaScript/Python execution
- `n8n-nodes-base.if` - Conditional branching
- `n8n-nodes-base.switch` - Multi-path routing
- `n8n-nodes-base.merge` - Combine data streams
- `n8n-nodes-base.set` - Data transformation

### External Services
- `n8n-nodes-base.httpRequest` - Generic HTTP requests
- `n8n-nodes-base.mySql` - MySQL database operations
- `n8n-nodes-base.redis` - Redis cache operations
- `n8n-nodes-base.slack` - Slack messaging
- `n8n-nodes-base.openAi` - OpenAI API integration

## Credential Management

### Credential References in Workflows
- Never store actual credentials in workflow JSON
- Use credential references like:
  ```json
  "credentials": {
    "mySql": {
      "id": "mysql_readonly",
      "name": "MySQL Read-Only"
    }
  }
  ```

### Standard Credential Names
- `mysql_readonly` - Read-only database access
- `mysql_write` - Write database access
- `redis_cache` - Redis caching
- `openai_api` - OpenAI API
- `slack_bot` - Slack bot token
- `ghl_api` - GoHighLevel API

## Testing and Validation

### Webhook Testing
```bash
# Test webhook endpoints
curl -X POST https://your-n8n.com/webhook/[path] \
  -H "Content-Type: application/json" \
  -d '{"test": "data"}'
```

### Workflow Validation Checklist
- All nodes have proper error handling
- Credentials are referenced, not embedded
- Webhook paths are unique
- Expression syntax is valid (`{{$json["field"]}}`)
- Connections form valid execution paths

## Documentation Standards

### README.md Requirements for Each Workflow
1. Purpose and overview
2. Prerequisites
3. Installation/import instructions
4. Configuration requirements
5. Testing procedures
6. Troubleshooting guide
7. Performance metrics/targets

### Workflow Naming Convention
- Format: `[System] - [Action] - [Target]`
- Example: `MySQL - Sync Data - Google Sheets`

## Important Implementation Details

### Expression Syntax
- N8N uses `{{}}` for expressions
- Access JSON data: `{{$json["fieldName"]}}`
- Access node data: `{{$node["NodeName"].json["field"]}}`
- Use JavaScript: `{{new Date().toISOString()}}`

### Error Handling Patterns
- Use try-catch in Code nodes
- Implement retry logic for external services
- Add error workflow references in settings
- Log errors to Slack or monitoring system

### Performance Considerations
- Use batch operations when available
- Implement caching with Redis for repeated queries
- Add rate limiting for external APIs
- Use `splitInBatches` node for large datasets

## Existing Workflow Examples

### AI Collections System (`/ai-collections-system/`)
- Complex multi-workflow system
- Uses MySQL, Redis, OpenAI, Slack integrations
- Implements webhook triggers and scheduled reports
- Contains AI prompt templates in `/prompts/`
- Full configuration guide in `workflow-configuration-guide.md`

## MCP Tool Usage Notes

- The N8N instance is live - be careful with production workflows
- Use validation before deploying changes
- Check execution history for debugging: `n8n_list_executions`
- Workflow IDs are strings, not numbers
- API operations are asynchronous - check status after creation