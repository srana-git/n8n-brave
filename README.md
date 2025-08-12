# N8N Workflow Management Hub

A centralized project for managing N8N workflows using the N8N MCP (Model Context Protocol) integration and specialized AI sub-agents. This hub enables intelligent workflow creation, optimization, debugging, and management through natural language interactions.

## 🎯 Project Purpose

This project serves as a management interface for your N8N instance, allowing you to:
- **Create new N8N automations** using natural language descriptions
- **Optimize existing workflows** for better performance
- **Debug and troubleshoot** workflow issues
- **Validate workflows** before deployment
- **Discover and integrate** with third-party services
- **Plan complex automation architectures** with AI assistance

Each workflow or automation task is organized in its own dedicated folder with complete documentation, configuration files, and implementation details.

## 🤖 N8N MCP Sub-Agents

This project leverages specialized AI agents through the N8N MCP integration. Each agent has specific expertise and tools:

### 1. **n8n-workflow-architect**
**Purpose:** High-level N8N workflow planning and architecture design  
**Capabilities:**
- Designs complete workflow architectures
- Plans data flow between systems
- Recommends optimal node configurations
- Creates scalability strategies
- Defines integration points

**Use When:** Starting a new complex automation project or redesigning existing workflows

### 2. **n8n-workflow-builder**
**Purpose:** Converts plans into working N8N workflows  
**Tools:** Full N8N MCP access, file operations  
**Capabilities:**
- Creates production-ready workflow JSON files
- Implements node configurations
- Sets up connections between nodes
- Adds error handling
- Creates sub-workflows

**Use When:** Building the actual workflow implementation from a plan

### 3. **n8n-node-finder**
**Purpose:** Discovers and recommends N8N nodes for specific tasks  
**Tools:** Node search, documentation access  
**Capabilities:**
- Finds appropriate nodes for any task
- Recommends alternatives
- Explains node configurations
- Provides usage examples
- Identifies community nodes

**Use When:** Looking for the right node or exploring available options

### 4. **n8n-integration-specialist**
**Purpose:** API integration and third-party service connections  
**Tools:** Web fetching, API documentation access  
**Capabilities:**
- Configures API authentications
- Sets up webhook integrations
- Handles OAuth flows
- Creates custom API requests
- Troubleshoots connection issues

**Use When:** Connecting N8N to external services or APIs

### 5. **n8n-debugger**
**Purpose:** Workflow debugging and troubleshooting  
**Tools:** Execution analysis, validation, logging  
**Capabilities:**
- Analyzes workflow executions
- Identifies error patterns
- Fixes expression issues
- Optimizes performance bottlenecks
- Reviews error logs

**Use When:** Workflows aren't working as expected or need troubleshooting

### 6. **n8n-optimizer**
**Purpose:** Performance optimization and workflow refactoring  
**Tools:** Workflow analysis, performance metrics  
**Capabilities:**
- Improves workflow efficiency
- Reduces execution time
- Optimizes memory usage
- Refactors complex workflows
- Implements caching strategies

**Use When:** Existing workflows need performance improvements

### 7. **n8n-validator**
**Purpose:** Pre-deployment validation and compliance checking  
**Tools:** Validation suite, compliance checks  
**Capabilities:**
- Validates workflow syntax
- Checks node configurations
- Verifies connections
- Tests expressions
- Ensures best practices

**Use When:** Before deploying workflows to production

## 📁 Project Organization

Each automation task creates its own folder containing:

```
n8n_brave/
├── CLAUDE.md                        # Context file for AI assistance
├── README.md                        # This file
├── .mcp.json                        # MCP configuration
│
├── ai-collections-system/           # Example: AI Collections workflow
│   ├── README.md                    # Task documentation
│   ├── *.json                       # Workflow files
│   ├── workflow-configuration.md    # Setup guide
│   └── prompts/                     # Supporting files
│
├── [your-next-automation]/          # Each new task gets its own folder
│   ├── README.md
│   ├── workflow files...
│   └── documentation...
│
└── shared/                          # Shared resources (if needed)
    ├── credentials/
    ├── templates/
    └── utilities/
```

## 🚀 How to Use This Hub

### Creating a New Automation

1. **Describe your automation need** in natural language
2. The appropriate sub-agents will:
   - Plan the workflow architecture
   - Find the required nodes
   - Build the implementation
   - Validate the workflow
3. A **new folder** will be created with:
   - Complete workflow JSON files
   - Configuration documentation
   - Setup instructions
   - Testing procedures

### Working with Existing Workflows

1. **Reference your N8N workflow** by name or ID
2. Available operations:
   - Debug execution issues
   - Optimize performance
   - Add new functionality
   - Refactor for maintainability
   - Update integrations

### Example Commands

```
"Create a workflow that syncs data from MySQL to Google Sheets every hour"
→ Uses workflow-architect, node-finder, and workflow-builder

"Debug why my webhook workflow isn't triggering"
→ Uses n8n-debugger to analyze the issue

"Optimize my data processing workflow that's running slowly"
→ Uses n8n-optimizer to improve performance

"Find all nodes that can send emails"
→ Uses n8n-node-finder to discover options
```

## 🔧 Configuration

### MCP Setup
The `.mcp.json` file contains the N8N API configuration and credentials needed for the MCP integration to communicate with your N8N instance.

### CLAUDE.md Context
The `CLAUDE.md` file will be created/updated to maintain context about:
- Your N8N instance configuration
- Existing workflows and their purposes
- Common patterns and preferences
- Integration credentials and endpoints

## 📚 Current Automations

### AI Collections System
**Location:** `/ai-collections-system/`  
**Purpose:** Automated debt collection with AI-powered personalization  
**Status:** Production Ready  
**Features:** 95% automation, settlement negotiations, response handling

*New automations will be listed here as they are created*

## 🛠️ Best Practices

1. **One Folder Per Task:** Each automation task gets its own dedicated folder
2. **Complete Documentation:** Every folder includes README and setup guides
3. **Version Control:** All workflows are stored as JSON for easy versioning
4. **Testing First:** Workflows include test data and validation procedures
5. **Modular Design:** Complex automations split into manageable sub-workflows

## 🔒 Security Notes

- Credentials are never stored in workflow files
- API keys are managed through N8N's credential system
- Sensitive data is handled according to best practices
- All workflows follow principle of least privilege

## 📈 Monitoring & Maintenance

Each automation folder includes:
- Performance benchmarks
- Monitoring setup instructions
- Maintenance schedules
- Troubleshooting guides
- Update procedures

## 🤝 Contributing

When adding new automations:
1. Create a new folder with descriptive name
2. Include comprehensive README
3. Document all dependencies
4. Provide test data
5. Update this main README

## 📄 License

This project and all contained workflows are proprietary and confidential.

---

**N8N Instance:** Connected via MCP  
**Last Updated:** January 2025  
**Maintained By:** AI-Assisted Workflow Management System