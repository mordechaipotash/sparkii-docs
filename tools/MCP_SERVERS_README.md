# MCP Servers Configuration Guide

## Overview
This document describes the Model Context Protocol (MCP) servers configured for the Sparkii Command Center. These servers extend Claude's capabilities with specialized tools and integrations.

## Configured MCP Servers

### 1. 🗄️ Supabase Sparkii (`supabase_sparkii`)
**Purpose**: Manages Supabase database operations for Sparkii projects  
**Capabilities**:
- Database queries and migrations
- Table management and schema operations
- Real-time subscriptions
- Edge functions deployment
- Branch management for development workflows

**Key Features**:
- Execute SQL queries directly
- Apply database migrations
- Generate TypeScript types from schema
- Monitor logs and performance advisors
- Manage development branches

### 2. 🐙 GitHub (`github`)
**Purpose**: GitHub repository management and collaboration  
**Capabilities**:
- Repository creation and management
- Pull request workflows
- Issue tracking
- Code search and file operations
- Branch management

**Auto-approved Operations**:
- `search_repositories` - No manual approval needed

**Key Features**:
- Create and manage repositories
- Handle PR reviews and merges
- Push multiple files in batch
- Search code across repositories

### 3. 💻 Desktop Commander (`desktop-commander`)
**Purpose**: Advanced file system and process management  
**Capabilities**:
- File operations (read, write, edit, search)
- Process and terminal management
- Interactive REPL sessions (Python, Node.js, etc.)
- System-level operations
- Data analysis workflows

**Key Features**:
- Streaming file search with pattern matching
- Multi-file batch operations
- Process lifecycle management
- Configuration management

### 4. 🔍 Exa Search (`exa`)
**Purpose**: Advanced web search and research capabilities  
**Capabilities**:
- Real-time web search
- Company research
- LinkedIn profile search
- URL content extraction
- Deep AI-powered research tasks

**Key Features**:
- Comprehensive web searches
- Business intelligence gathering
- Content crawling and extraction
- AI research agent for complex queries

### 5. 🌐 Puppeteer (`puppeteer`)
**Purpose**: Browser automation and web testing  
**Capabilities**:
- Navigate to URLs
- Take screenshots
- Interact with page elements (click, fill, select)
- Execute JavaScript in browser context
- Browser automation workflows

**Key Features**:
- Full browser control
- Visual testing capabilities
- Form automation
- Web scraping support

### 6. 📝 Notion (`notion`)
**Purpose**: Notion workspace management  
**Capabilities**:
- Database queries and updates
- Page and block management
- Content creation and editing
- Search across workspace
- Comment management

**Key Features**:
- Full Notion API access
- Database operations
- Content management
- Collaborative features

### 7. 🖥️ IDE Integration (`ide`)
**Purpose**: VS Code integration and IDE features  
**Capabilities**:
- File navigation
- Code editing assistance
- Project structure understanding
- IDE command execution

### 8. 📚 Context7 (`context7`)
**Purpose**: Documentation and knowledge base access  
**Capabilities**:
- Access curated documentation
- Framework and library references
- Best practices and patterns
- Quick documentation lookup

### 9. 🎨 Shadcn Components (`shadcn` & `shadcn-ui`)
**Purpose**: UI component library access and management  
**Capabilities**:
- Browse available components
- Get component demos and source code
- Access UI blocks and patterns
- Component metadata and dependencies

**Key Features**:
- Complete shadcn/ui component library
- Pre-built UI blocks
- Usage examples and demos
- Component installation guidance

## Usage in Claude

### Accessing MCP Tools
MCP tools are accessed through the `mcp__` prefix:
```
mcp__[server_name]__[function_name]
```

### Examples:
```bash
# Supabase operations
mcp__supabase_sparkii__list_tables
mcp__supabase_sparkii__execute_sql

# GitHub operations
mcp__github__create_repository
mcp__github__create_pull_request

# Desktop Commander
mcp__desktop-commander__read_file
mcp__desktop-commander__start_process

# Exa search
mcp__exa__web_search_exa
mcp__exa__deep_researcher_start

# Puppeteer browser control
mcp__puppeteer__puppeteer_navigate
mcp__puppeteer__puppeteer_screenshot

# Notion operations
mcp__notion__API-post-database-query
mcp__notion__API-create-a-page

# Shadcn components
mcp__shadcn__list_components
mcp__shadcn__get_component_demo
```

## Common Workflows

### 1. Database Development (Supabase)
```yaml
Development Flow:
  1. Create development branch
  2. Apply migrations
  3. Test queries
  4. Merge to production
```

### 2. Code Collaboration (GitHub)
```yaml
PR Workflow:
  1. Create feature branch
  2. Push files
  3. Create pull request
  4. Review and merge
```

### 3. UI Development (Shadcn)
```yaml
Component Integration:
  1. List available components
  2. Get component demo
  3. Get source code
  4. Implement in project
```

### 4. Research & Analysis (Exa)
```yaml
Research Flow:
  1. Start deep research task
  2. Monitor progress
  3. Extract insights
  4. Apply findings
```

### 5. Browser Testing (Puppeteer)
```yaml
Test Flow:
  1. Navigate to page
  2. Interact with elements
  3. Take screenshots
  4. Validate behavior
```

## Security Notes

⚠️ **Important**: This configuration contains sensitive tokens and API keys. These should be:
- Kept secure and never shared publicly
- Rotated regularly
- Stored in environment variables for production use
- Never committed to version control

## Troubleshooting

### Server Not Responding
- Check if the server is properly installed
- Verify API keys and tokens are valid
- Ensure network connectivity

### Permission Errors
- Verify access tokens have required permissions
- Check organization/workspace access levels
- Ensure proper authentication

### Performance Issues
- Consider server response times
- Batch operations when possible
- Use appropriate servers for specific tasks

## Best Practices

1. **Use the right tool**: Each server has specialized capabilities
2. **Batch operations**: Combine multiple operations when possible
3. **Monitor resources**: Be aware of API rate limits
4. **Security first**: Never expose sensitive data
5. **Document workflows**: Keep track of complex multi-server operations

## Server Dependencies

All servers are installed via `npx` for zero-configuration setup:
- No manual installation required
- Automatically downloads latest versions
- Cross-platform compatibility
- Minimal system requirements

## Updates and Maintenance

Servers are automatically updated to latest versions through npx:
- `@latest` tag ensures current versions
- No manual update process needed
- Backward compatibility maintained

---

*Last Updated: January 2025*  
*Configuration Location: `.mcp.json`*